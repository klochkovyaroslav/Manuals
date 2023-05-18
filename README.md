# Manuals

### Редактирование файла Sudoers

Команда sudo настраивается с помощью файла, расположенного в каталоге /etc/sudoers  

```bash
sudo visudo
```

Строки пользовательских привилегий  Давайте посмотрим, что означают различные поля:  

**root** ALL=(ALL:ALL) ALL Первое поле показывает имя пользователя, которое правило будет применять к (root).  
root **ALL**=(ALL:ALL) ALL Первое “ALL” означает, что данное правило применяется ко всем хостам.  
root ALL=(**ALL**:ALL) ALL Второе “ALL” означает, что пользователь root может запускать команды от лица всех пользователей.  
root ALL=(ALL:**ALL**) ALL Третье “ALL” означает, что пользователь root может запускать команды от лица всех групп.  
root ALL=(ALL:ALL) **ALL** Последнее “ALL” означает, что данные правила применяются всем командам.  


### NetworkManager service.

```bash
sudo systemctl start NetworkManager
nmcli general status - состояние интерфейсов
nmcli device status
nmcli general hostname - посмотреть имя хоста
nmcli connection show - список доступных подключений
nmcli connection add con-name "static" ifname enp2s0 autoconnect no type ethernet ip4 192.168.0.210 gw4 192.168.0.1 - Создание соединения с статическим адресом
nmcli conn modify "static" +ipv4.addresses 192.168.0.240/24 - добавить еще IP адрес 
nmcli conn modify "static" ipv4.dns 8.8.8.8 - добавить DNS
nmcli conn modify "static" +ipv4.dns 8.8.4.4 добавить еще DNS
nmcli connection up static

nmcli radio wifi
nmcli radio wifi on
nmcli device wifi list
nmcli device wifi connect "TP-Link" password 12345678 name "TP-Link Wifi" - подключиться к wifi сети с именем: "TP-Link" с паролем 12345678

ip addr show
ip -s link show enp4s0 -статистика по интерфейсу
ip route show match 0/0 - посмотреть таблицу маршрутизации

sudo systemctl status NetworkManager.service
sudo systemctl restart NetworkManager.service
```

### TCPDUMP
https://blog.sedicomm.com/2017/05/30/tcpdump-poleznoe-rukovodstvo-s-primerami/

```bash
sudo tcpdump -i eth0 port '(50001 or 443)'
sudo tcpdump icmp  - конкретный тип трафика
sudo tcpdump dst 192.168.0.100  - чтобы увидеть входящий трафик:
sudo tcpdump net 192.168.0.100/24
sudo tcpdump portrange 8000-9000
sudo tcpdump -vv src mars and not dst port 22
```

## AppArmor в Ubuntu и Debian

```bash
sudo apparmor_status
```
#### Stop apparmor service using systemd  

```bash
sudo systemctl stop apparmor
```

#### Disable apparmor from starting on system boot   

```bash
systemctl disable apparmor
```

#### Remove apparmor package and dependencies using apt (optional)  

```bash
sudo apt remove --assume-yes --purge apparmor
```


## Утилита ldapsearch

#### Основные опции  

-n – отобразить действия, которые будут выполнены, но не выполнять их;  
-v - verbose, подробный режим работы;  
-A – отображать только атрибуты, без значений;  
-L (-LL, -LLL) — формат выводимой информации (-L - LDIFv1, -LL — отключить вывод комментариев, -LLL — отключение отображения версии LDIF)  
-x – использовать обычную аутентификацию, не SASL;  
-D – использовать binddn (имя пользователя) для подключения к серверу;  
-w[password] – указать пароль password в командной строке при вводе запроса;  
-W – запрос на ввод пароля будет выдан во время выполнения операции;  
-h  – адрес LDAP-сервера;  
-p – порт сервера LDAP;  
-b – стартовый каталог для поиска;  
-s[base|one|sub] – searchScope поиска, уровень вложенности поиска:  
-l - timelimit на время выполнения поиска;  
-z - sizelimit на размер данных в запросе поиска;  
-Z – использовать TLS во время выполнения операции  

#### Пример:

```bash
ldapsearch -x -b "DC=wp,DC=demo" -H "ldap://192.168.1.18" -W sAMAccountName=i.ivanov.sm -D "i.ivanov.sm@wp.demo"
```


## Команды - разное:

### PING сразу нескольких машин по очереди.

```bash
for i in {18..19}; do ping -c3 "192.168.1.$i"; done
```

### Повторить какие порты слушает сервер:

```bash
ss -tulpn
```

### Повторить предыдущую команду (ss -tulpn) в bash:

```bash
!ss
или
!!
```

### Скопировать файл по сети на server2 в каталог: root

```bash
sudo scp file1.tar.gz root@server2:/root/
```

### Переименовать файл
```bash
sudo cp file1.txt{,.bak}
```
