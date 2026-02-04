# Установка и настройка NTP сервера Chrony
#### CentOS/RHEL
```bash
sudo yum install -y chrony
```
#### Debian/Ubuntu
```bash
sudo apt install -y chrony
```

#### Серверы с которых NTP будет получать эталонное время и другие параметры
/etc/chrony.conf — в CentOS, RHEL, Fedora  
/etc/chrony/chrony.conf — в Ubuntu, Debian  

```bash
sudo vi /etc/chrony.conf
```

> server ntp1.vniiftri.ru iburst  
> server ntp2.vniiftri.ru iburst  
> server 127.127.1.0  
> driftfile /var/lib/chrony/drift  
> makestep 1.0 3  
> rtcsync  
> allow 192.168.56.0/21  
> keyfile /etc/chrony.keys  
> ntsdumpdir /var/lib/chrony  
> leapsectz right/UTC  
> logdir /var/log/chrony

<img width="1288" height="792" alt="image" src="https://github.com/user-attachments/assets/43d47a6a-4efd-40fe-bd9e-917123bd2735" />



- **server** — NTP-сервер для синхронизации
- **stratumweight** — задержка синхронизации
- **driftfile** — расположение и имя файла, содержащего данные смещения
- r**tcsync** — включает режим, в котором системное время периодически копируется в RTC
- **makestep** — эта директива корректирует смещение во времени путем снижения скорости или замедления хода часов
- **bindcmdaddress** — позволяет указать IP-адрес интерфейса, на котором chronyd будет прослушивать пакеты команд мониторинга
- **keyfile** — файл, содержащий пары ID-ключей для аутентификации пакетов NTP
- **commandkey** — используется для установки номера ключа, используемого для аутентификации пользовательских команд с помощью программы chronyc во время выполнения
- **generatecommandkey** — если командный ключ не найден при запуске, chronyd создаст новый командный ключ из файла /dev/urandom и запишет его в файл ключа
- **noclientlog** — клиентские обращения не должны регистрироваться
- **logchange** — пороговое значение для настройки системных часов, которые будут генерировать сообщение системного журнала
- **logdir** — путь к файлу журнала


#### Вывести только незакоментированные строки
```bash
sudo grep ^[[:alnum:]] /etc/chrony.conf
```
#### Добавить сервис chronyd.service в автозагрузку
```bash
sudo systemctl enable --now chronyd.service
```
```bash
sudo systemctl is-active chronyd.service
```
##### Изменить часовой пояс
```bash
sudo timedatectl set-timezone Europe/Moscow
```
#### Добавить правиво в файрволл
```bash
sudo firewall-cmd --add-service=ntp --permanent
sudo firewall-cmd --reload
```
----
## Управление Chronyd
#### Информация об источниках chrony
```bash
chronyc sources
```
#### Состояние синхронизации
```bash
chronyc tracking
```
##### Перечисленные пункты содержат следующую информацию:

- **Reference ID** — идентификатор и имя сервера, с которым компьютер в настоящее время синхронизирован;
- **Stratum** — количество переходов к серверу с установленными эталонными часами;
- **Ref time** — время по Гринвичу, в которое было выполнено последнее измерение из эталонного источника;
- **System time** — задержка системных часов от синхронизированного сервера;
- **Last offset** — расчетное смещение последнего обновления часов;
- **RMS offset** — долгосрочное среднее арифметическое значения смещения;
- **Frequency** — частота, на которой часы системы будут работать неправильно, если хронограф не проведет коррекцию. Она выражена в ppm — ч/м (частей на миллион);
- **Residual freq** — остаточная частота указывает на разницу между измерениями от опорного источника и используемой в настоящее время частотой;
- **Skew** — расчетная погрешность, связанная с погрешностью частоты;
- **Root delay** — суммарная задержка сетевого пути к опорному серверу, с которым синхронизируется компьютер;
- **Leap status** — статус скачка (изменения) времени, который может иметь одно из следующих значений:
- **«Normal»** – означает нормальную корректировку времени;
- **«Insert second»** – означает, что произведена корректировка времени добавлением дополнительной секунды в последнюю минуту текущего месяца;
- **«Delete second»** – означает, что произведена корректировка времени удалением дополнительной секунды из последней минуты текущего месяца;
- **«Not synchronised»** – означает, что компьютер в данный момент времени не синхронизирован.


#### Статистика
```bash
chronyc sourcestats
```
#### Отключиться/подключиться от/к глобальной сети Интернет
```bash
chronyc offline
chronyc online
```
#### Проверка статуса источников NTP:
```bash
chronyc activity
```
----

## Настройка chrony на клиенте

```bash
sudo yum install chrony -y
```
```bash
sudo vi /etc/chrony.conf
```
или
```bash
sudo sed -i 's/pool 2.centos.pool.ntp.org iburst/server 192.168.56.10 iburst/' /etc/chrony.conf
sudo sed -i 's/sourcedir \/run\/chrony-dhcp/#sourcedir \/run\/chrony-dhcp/' /etc/chrony.conf
```
```bash
sudo timedatectl set-timezone Europe/Moscow
```
```bash
systemctl list-units --type service --all | grep ntp
```
Если служба ntpd запущена, необходимо ее остановить и отключить автозапуск
```bash
sudo systemctl enable --now chronyd.service
```
```bash
sudo systemctl restart chronyd.service
```
```bash
timedatectl status
```
```bash
chronyc sources
```


