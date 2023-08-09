# Manuals

### Редактирование файла Sudoers

Команда sudo настраивается с помощью файла, расположенного в каталоге /etc/sudoers  

```bash
sudo visudo
```

Строки пользовательских привилегий, что означают различные поля:  

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
ldapsearch -x -b "DC=wp,DC=demo" -H "ldap://192.168.1.100" -W sAMAccountName=p.ivanov.sm -D "p.ivanov.sm@wp.demo"
```


## Команды - разное:

### PING сразу нескольких машин по очереди.

```bash
for i in {18..19}; do ping -c3 "192.168.1.$i"; done
```

### Список всех открытых портов сервера:

```bash
netstat -tulpn
ss -tulpn
watch ss -tulpn
```

### Повторить предыдущую команду (ss -tulpn) в bash:

```bash
!ss
или
!!
```

### Передать многострочную строку в канал в Bash

```bash
cat << EOF | tee 1.txt
aaa
bbb
ccc
EOF
```

### Скопировать файл по сети на server2 в каталог: root

```bash
sudo scp file1.tar.gz root@server2:/root/
```

### Переименовать файл
```bash
sudo cp file1.txt{,.bak}
```

### Поиск файла
```bash
sudo find / -iname *postgresql.conf*
sudo find / -iname 'postgresql.????' - искать текст за которыми следуют еще 4 символа
```

### Склон диска в дамп файл .img

Здесь в опции "**if**" указываем путь к устройству, с которого снимаем дамп,  
в опции "**of**" указываем путь к файлу, в который сохраняем дамп,  
а опция "**status**" пригодится для включения отображения хода снятия дампа

```bash
dd if=/dev/sdb of=/media/flash/764G4A1C6L79F18V/Backup-disk.img status=progress
```


## Подключить общую сетевую папку Windows по SMB (CIFS)

```bash
sudo apt-get install cifs-utils
или
sudo dnf install cifs-utils
```

####- Создать точку монтирования:
```bash
$ sudo mkdir /mnt/share
```

#### Смонтировать сетевую папку

```bash
sudo mount -t cifs -o username=User_test,password=Paswd,uid=1000,iocharset=utf8 //192.168.31.33/backup /mnt/share
```
или

```bash
sudo mount -t cifs -o credentials=/home/sysops/.windowscredentials,uid=1000,iocharset=utf8 //192.168.31.33/backup /mnt/share
```

или автоматическое монтирование сетевой папки Windows через /etc/fstab

```bash
sudo mcedit /etc/fstab
```

```bash
//192.168.31.33/backup /mnt/share cifs user,rw,credentials=/home/sysops/.windowscredentials,iocharset=utf8,nofail,_netdev,vers=3.0 0 0
```


#### Чтобы не указывать учетные данные в команде монтирования, их можно сохранить в файле

```bash
touch ~/.windowscredentials && chmod 600 ~/.windowscredentials
```
Внести в файл:  
```
username=User_test   
password=Paswd  
domain = local.ru
``` 


#### Основные опции:
*`//192.168.31.33/backup – сетевая папка Windows`*  
*`/mnt/share – точка монтирования`*  
*`-t cifs – указать файловую систему для монтирования`*  
*`-o опции монтирования (эту опцию можно использовать только с правами root, поэтому в команде используется sudo)`*  
*`username=User_test,password=Paswd – имя и пароль пользователя Windows`*  
*`Можно указать имя пользователя guest, если разрешен анонимный доступ к сетевой папке`*  
*`iocharset=utf8 – включить поддержку кодировки UTF8 для отображения имен файлов`*  
*`uid=1000 – использовать этого пользователя Linux в качестве владельца файлов в папке`*  
*`rw – смонтировать SBM папку на чтение и запись`*  
*`nofail – продолжить загрузку ОС если не удается смонтировать файловую систему`*  
*`_netdev – указывает что подключается файловая система по сети. Linux не будет монтировать такие файловые системы пока на хосте не будет инициализирована сеть.`*  

