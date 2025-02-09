# Настройка SELinux в CentOS

### Общие команды
#### Посмотреть состояние работы SELinux (развернуто):
```bash
sestatus
```
#### Посмотреть кратко — работает или нет:
```bash
getenforce
```
#### Отключить SELinux (разово до перезагрузки):
```bash
setenforce 0
```
#### Включить (разово):
```bash
setenforce 1
```
#### Отключить SELINUX навсегда
```bash
sudo -s;  
setenforce 0;  
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config;
```
----

## Настройка SELinux


#### В системе должен быть установлен пакет policycoreutils-python:

```bash
sudo install policycoreutils-python-utils
```

#### Просмотр текущих настроек контекста безопасности
##### Для файлов:
```bash
ls -Z
```
> команда покажет все файлы в текущей директории и выведет для каждого контекст безопасности.

#### Для процессов:
```bash
ps -aeZ
```
> можно использовать grep для выборки определенного процесса или файла.  
> Вывод каталогов/файлов и применяемых к ним по умолчанию контекстов безопасности:
```bash
semanage fcontext -l
```
#### Смена контекста безопасности
Задаем метки по умолчанию, которые должны применяться ко всем файлам в каталоге:
```bash
semanage fcontext --add --type NetworkManager_etc_rw_t '/etc/NetworkManager/NetworkManager(/.*)?'
```
> в данном примере мы задали правило, что всем файлам в директории /etc/NetworkManager/NetworkManager будут назначаться SELinux права (тип) NetworkManager_etc_rw_t. Однако нужно понимать, что это не приведет к смене прав для файлов, которые уже находятся в каталоге.

#### Сбрасываем права на заданные по умолчанию:
```bash
restorecon /etc/NetworkManager/NetworkManager
```
> все права сменятся на те, которые мы указали командой semanage fcontext.

#### Редактируем права на файл:
```bash
chcon -v --type=cron_spool_t /var/spool/cron
```
> команда задаст права на /var/spool/cron. Также можно использовать рекурсивный запрос, чтобы применить метки ко всех файлам в каталоге. Это делается с добавлением опции -R.

----

### Открытие портов
#### Права на использование сетевых портов также контролируются с помощью SELinux.

#### Пример для разрешения использовать http-запросы:
```bash
semanage port -m -t http_port_t -p tcp 80
```
```bash
semanage port -m -t http_port_t -p tcp 443
```
> в данном примере для портов 80 (http) и 443 (https) для контекста http_port_t.

#### Посмотреть список портов, и для каких контекстов они разрешены можно командой:
```bash
semanage port -l
```
----

### Политики
#### Список установленных редактируемых политик можно посмотреть командой:
```bash
getsebool -a
```
> команда выведет название политик и их статус: включена — on, отключена — off.

#### Чтобы изменить состояние готовой политики, вводим команду:
```bash
setsebool -P <имя политики> <on или off>
```
#### Например:
```bash
setsebool -P httpd_enable_cgi on
```
----

### Диагностика проблем
#### Для поиска проблем в настройке политик сначала необходимо перевести режим работы SELinux в permissive. Для этого открываем файл:
```bash
vi /etc/selinux/config
```
и отредактировать опцию SELINUX:

> SELINUX=permissive

#### После перезагружаемся:
```bash
shutdown -r now
```
#### Теперь можно просмотреть лог-файл:
```bash
tail -f /var/log/audit/audit.log
```

#### SELinux в Ubuntu
> В других системах на базе ядра Linux, например, Debian/Ubuntu, настройки SELinux выполняется способами, описанными выше.  
> Единственное отличие — по умолчанию, данная система безопасности не установлена. Для установки необходимо выполнить команду:
```bash
apt-get install selinux
```

Настройка SELinux в CentOS - [ссылка](https://www.dmosk.ru/miniinstruktions.php?mini=selinux-setting).
Примеры - [ссылка](https://www.dmosk.ru/miniinstruktions.php?mini=selinux-example).
