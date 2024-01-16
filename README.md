# LINUX

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
nmcli device status - Проверка статуса устройств
nmcli device show enp0s8 - Запрос информации об устройстве
nmcli general hostname - посмотреть имя хоста
nmcli connection show - список доступных подключений
Настройка подключений:
nmcli connection modify ethernet-enp0s8 ipv4.address 192.168.4.26/24 - установить IP-адрес
Сменить динамический (DHCP) на статический IP-адрес
nmcli connection modify ethernet-enp0s8 ipv4.method manual - установить метод (не DHCP)
Установить DHCP, вместо manual используйте auto:
nmcli connection modify ethernet-enp0s8 ipv4.method auto
nmcli connection down ethernet-enp0s8
nmcli connection up ethernet-enp0s8
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

### GREP -global gegular expression print

```bash
grep "Failed" ./log
grep -i "Failed" ./log  -игнорировать регистр
sudo grep -v ^# /etc/ssh/sshd_config - Отфильтровать начало строки начинается с #
sudo grep -v ^# /etc/ssh/sshd_config | grep -v ^$ - Отфильтровать начало строки начинается с # и убрать пустые строки.

```

### HEAD

```bash
head ./log
head -3 -вывести первые n строк
head -n 3 ./log -вывести первые n строк (более корректно)
```

### TAIL

```bash
head ./log
head -3 -вывести последние n строк
head -n 3 ./log -вывести последние n строк (более корректно)
head -f ./log -врежимереального времени смотреть файл
```

### Утилита LESS  

Поиск по тексту ставим: / text  
Поиск вниз жмем n  
Поиск вверх жмем N  


### Утилита TEE  
Вывод команды ls параллельно в файл и в терминал  

```bash
ls -l | tee -a file
```
### Потоки и перенаправления в Linux - STDOUT-1, STDERR-2, STDIN-0

```bash
ls -l > stdout.xtx - перенаправить стандантный поток в stdout
ls -l 1> stdout.xtx - перенаправить стандантный поток в stdout
ls -l fgdhfg 2> stdoerr.xtx - перенаправить поток ошибок в stderr
ls -l fgdhfg 2> stdoerr.xtx 1> stdout.txt
ls -l fgdhfg ./ 2> stdoerr.xtx 1> stdout.txt
ls -l dfgdf ./ 1> file 2>&1 - Перенаправить поток ошибок "2", туда же куда направлен поток стандартного вывода "1"
ls -l dfgdf ./ 2>&1 | grep dfgdf
ls -l dfgdf ./ 2>&1 1> /dev/null | grep dfg - Перенаправить на поток ввода grep только поток ошибок "2", а поток "1" отправить в /dev/null
```


### Коды возврата 0- ОК, не 0 - ошибка.

```bash
ls -l kdkfd
echo $? - посмотреть последный код возврата команды ls
```


 
## Создание разделов при помощи PARTED

```bash
parted /dev/sdb print

Создаем таблицу разделов
parted /dev/sdb mklabel gpt

Создаем раздел (начало 1МБ конец раздела 10 Гб)
parted /dev/sdb mkpart primary 1MB 10GB
parted /dev/sdb print

Создаем файловую систему ext4

mkfs.ex4 /dev/sdb1

Временно смонтируем новый раздел sdb1 в каталог /mnt/dir1

mkdir /mnt/dir1
mount /dev/sdb1 /mnt/dir1

Проверяем что получилось
df -h


Увеличить раздел можно на лету(online)
parted /dev/sdb resizepart

Указать номер раздела
1
Система сообщает что раздел используется (примонтирован), говорим:
Yes
Указываем конечную точку:
20GB

parted /dev/sdb print
lsblk

Меняем размер файловой системы
resize2fs /dev/sdb1
df -h
```


## Создание RAID при помощи MDADM
RAID5 на 4 диска

```bash
mdadm --create /dev/md0 --level=raid5 --raid-devices=4 /dev/sd{c..f}

Создание массива займет некоторое время
Смотреть как создается raid массив:
cat /proc/mdstat

Посмотреть детали созданного raid массива:
mdadm --detail /dev/md0

Если диски переданы системе например по Fibre Chanel то нужно при создании фаловой системы на массиве задать геометрию диска вручную:
stride=512 - единица обмена данными
stripe-width=1536 - уровень чередования это stride*3 (3- количество дисков в массиве на которые пишутся данные)

mkfs.ex4 -E stride=512,stripe-width=1536 /dev/md0

Смотрим что получилось:
blkid /dev/md0

Примонтируем раздел на постоянной основе через /etc/fstab
vim /etc/fstab

Можно подтянуть UUID устройства при помощи
:.!blkid | grep md0

UUID=00000001-0000-0000-0000-000000000001  /mnt/raid  xfs  defaults 0 0

mount -a
df -h
```
##############################################################################################

# WINDOWS


#### Сброс настроек протокола TCP/IP в Windows

```
ipconfig /flushdns
nbtstat -R
nbtstat -RR

netsh int reset all
netsh int ip reset
netsh winsock reset
```

#### Остановить зависшую службу Windows из командной строки

```
sc queryex wuauserv
taskkill /PID 16980 /F
или
TASKKILL /F /FI "SERVICES eq wuauserv"
taskkill /F /FI "status eq not responding" -Все зависшие службы
TASKKILL /S test01.local /F /FI "SERVICES eq wuauserv" - На удаленном PC
```

#### Остановить зависшую службу Windows из PowerShell

```
PS < 6 версии
Get-WmiObject -Class win32_service | Where-Object {$_.state -eq 'stop pending'}
PS >= 6 версии
Get-CimInstance -Class win32_service | where-Object state -eq 'stop pending'
```

#### Выполнить скрипт по сети PowerShell

```
Invoke-Command -ComputerName Server1, PC2 -ScriptBlock {Get-NetAdapterAdvancedProperty –Name Ethernet –DisplayName "Jumbo Packet"}
```

#### Проверка наличия скрытых сетевых адаптеров

```
Get-PnpDevice -class net | ? Status -eq Unknown | Select FriendlyName,InstanceId
```

#### Удалить скрытый сетевой адаптер

```
$InstanceId = “PCI\VEN_8086&DEV_10D3&SUBSYS_07D015AD&REV_00\000C29FFFF66A80700”
$RemoveKey = "HKLM:\SYSTEM\CurrentControlSet\Enum\$InstanceId"
Get-Item $RemoveKey | Select-Object -ExpandProperty Property | %{ Remove-ItemProperty -Path $RemoveKey -Name $_ -Verbose}
```

#### Удалить ВСЕ скрытые сетевые адаптеры

```
$Devs = Get-PnpDevice -class net | ? Status -eq Unknown | Select FriendlyName,InstanceId
ForEach ($Dev in $Devs) {
$RemoveKey = "HKLM:\SYSTEM\CurrentControlSet\Enum\$($Dev.InstanceId)"
Get-Item $RemoveKey | Select-Object -ExpandProperty Property | %{ Remove-ItemProperty -Path $RemoveKey -Name $_ -Verbose }}
```

#### Удалить скрытый сетевой адаптер вручную из Реестра

```
HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces
```

#### Посмотреть текущие сессии по сети 

```
Invoke-Command -ComputerName Server1 -ScriptBlock {query session}
```
#### Подключиться к удаленной RDP сессии по по RDP 

```
Mstsc /v:10.0.0.100 /shadow:2 /control /Prompt /noConsentPrompt
```

## Active directory

Утилита позволяет подключиться к AD на более глубоком уровне  

```bash
adsiedit.msc
```
