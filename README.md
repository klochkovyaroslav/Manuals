# LINUX
## Target(Цели) systemd:

```
poweroff.target: выключение системы.
rescue.target: запускает сеанс спасательной оболочки.
multi-user.target: настраивает систему на неграфическую (консольную) многопользовательскую систему.
graphical.target: настройка системы на использование графического многопользовательского интерфейса с сетевыми службами.
reboot.target: перезагружает систему.
halt.target: останавливает систему, не отключая её
```

#### Чтобы проверить текущую цель в вашей системе, выполните команду:F

```bash
systemctl get-default
```

#### команда переводит систему в неграфическое состояние (после перезагрузки).

```bash
sudo systemctl set-default multi-user.target
```

#### команда возвращает в загрузку в графический интерфейс:
```bash
sudo systemctl set-default graphical.target
```
### Как узнать, какой менеджер отображения используется на Linux

```bash
 systemctl status display-manager.service
```
```bash
dpkg -l | grep -i 'Display Manager\|Login Manager' | awk '$2 !~ /^lib/'
```

#### Как переключиться между менеджерами отображения

```bash
sudo systemctl disable gdm.service
sudo systemctl enable sddm.service
```
#### Как поменять Display Manager с помощью dpkg-reconfigure

```bash
sudo dpkg-reconfigure gdm3
```

[Как установить новый Display Manager в Debian, Ubuntu, Linux Mint, Kali Linux и их производных](https://zalinux.ru/?p=8982#1)


## Имя в системе (Hostname)

```bash
hostname
hostnamectl
```
## Сменить Имя в системе (Hostname)
#### Способ 1. Используем команду hostnamectl
```bash
hostnamectl set-hostname server1.local
```

#### Способ 2. Редактируем файлы /etc/hostname и /etc/hosts
```bash
#! /bin/bash
echo Enter new name of host
echo Example: newname.mydomain.local.com
read hostnm

sed -i 's/'$HOSTNAME'/'$hostnm'/' /etc/sysconfig/network
sed -i 's/'$HOSTNAME'/'$hostnm'/' /etc/sysctl.conf
sed -i 's/'$HOSTNAME'/'$hostnm'/' /etc/hosts
```

## Включение TRIM на SSD

```bash
systemctl status fstrim.service
systemctl status fstrim.timer
```

#### Узнайте версию прошивки:

```bash
sudo hdparm -i /dev/ваш_диск | grep -i Fw
sudo hdparm -I /dev/sda | grep "TRIM"
```
#### Проверка TRIM

```bash
lsblk --discard
```
Если вы видите нулевые значения в колонках DISC-GRAN (discard granularity) и DISC-MAX (discard max bytes), значит TRIM НЕ работает.  
Здесь DISC-GRAN равен 512B потому что размер сектора на SSD равен 512 bytes.  

#### Проверить еще можно командой ручного вызова TRIM:

```bash
sudo fstrim -v /
sudo fstrim -av
```


## Редактирование файла Sudoers

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

### Пример
#### WinSCP с правами root:

Найти файл sftp-server:  
```bash
whereis sftp-server | awk ' {print $2}'
```
```bash
sudo visudo
```
В конец файла вставить:  
```bash
user ALL=NOPASSWD:/usr/lib/sftp-server
```
```bash
zabbix ALL = NOPASSWD: /usr/sbin/asterisk
```

#### В настройках приложения WinSCP

Кнопка: **Еще**, в пункте меню: **Среда -> SFTP**  
**Сервер SFTP:** sudo /usr/lib/sftp-server  


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

### NMAP
#### Просканировать на открытые порты
```bash
nmap --open -T5 -Pn 192.168.88.1
```
> -T5 скорость сканирования  
> -Pn не пинговать  
> --open только открытые порты  

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


## Ядро

#### Узнать какое ядро загружено

```bash
cat /proc/cmdline
grep -Eo 'BOOT_IMAGE=[^ ]*' /proc/cmdline
```

# Команды - разное:

## Alias
#### Создать алиас

```bash
alias ospf_stat_core1='ssh -i ~/.ssh/zabbix/id_rsa sshro@192.168.10.76 show ip ospf neighbor'
```
#### Удалить алиас

```bash
unalias ospf_stat_core1
```
#### Добавить alias на "постоянку"

```bash
nano ~/.bashrc
```
Добавить в конец файла  
```bash
alias ospf_stat_core1='ssh -i ~/.ssh/zabbix/id_rsa sshro@192.168.10.76 show ip ospf neighbor'
```

#### Linux команда FOLD: управление шириной строки
```bash
fold -s -w 60 some_file.txt
```

#### Linux команда PASTE для построчного объединения текстовых файлов
```bash
paste file1.txt file2.txt
```

#### В команде LS вывести вместо имени и группы владельца числовые значения 
```bash
ls -ln text.txt
ls --numeric-uid-gid text.txt
```

#### Разделить файл на части по указанному кол-ву строк. Имена новых файлов будут: xaa, xab итд
```bash
split -l 100 some_text_file.txt
```
#### Разделить файл на части по указанному кол-ву частей
```bash
split -n 2 some_file.iso
```
#### Разделить файл на части по размеру
```bash
split -b 500M some_file.iso
```
#### Восстановление исходного файла
```bash
cat x*.iso > restored_some_file.iso
```
#### Проверить что файл восстановлен корректно:
```bash
md5sum *.iso
```


### Разделяемые библиотеки
#### Посмотреть разделяемые библиотеки например для /bin/bash
```bash
sudo ldd /bin/bash
```
#### Файл с указанием размещения разделяемых библиотек
```bash
nano /etc/ld.so.conf
```
#### после изменения файлов выполнить команду:
```bash
ldconfig
```


#### Указать системе в какой папке искать разделяемые библиотеки. ВРЕМЕННО
```bash
export LD_LIBRARY_PATH=/path/to/your/library:$LD_LIBRARY_PATH
```
#### Указать системе в какой папке искать разделяемые библиотеки. ПОСТОЯННО
```bash
echo 'export LD_LIBRARY_PATH=/path/to/your/library:$LD_LIBRARY_PATH' >> ~/.bashrc
```

## Процессы

#### Список всех активных процессов:

```bash
ps axu
```
```bash
ps axu | grep bash
ps aux | grep docker | tail -n 2
pidof bash
```
```
kill — убить процесс по его идентификатору (Process ID);
pkill — убить процесс по его имени;
killall — убить все процессы с указанным именем.
```

## Узнать версию пакета в системе DEB
#### Узнать версию уже установленного пакета

```bash
apt list --all-versions tmux
dpkg -s tmux | grep version (-s детальная информация о пакете)
apt-cache show tmux | grep Filename
```
#### Полностью обновить систему Debian

```bash
apt update && apt upgrade && apt dist-upgrade && apt --purge autoremove
```

## Чем занято место на диске

```bash
 du -smh * | sort -h
 ls -A | xargs -d"\n" du -msc | sort -n
```

#### Найти файлы, которые занимают больше 1Gb

```bash
find . -mount -type f -size +1G 2>/dev/null
```
2>/dev/null используется, чтобы не показывать ошибки (например, если нет доступа к файлу)  

####  Утилита ncdu - анализ занятого места

```bash
apt install ncdu
```
#### Содержимое всего диска
```bash
ncdu /
```

#### файл протокола неудачных попыток входа в систему

```bash
faillog
faillog -u root (reset count)
faillog -r (reset count)
```

## SIGNAL

### Команда: pkill


#### 1 (HUP): перезагрузить процесс.

```bash
pkill -1 firefox
pkill -HUP nginx
```

#### 15 (TERM): аккуратно завершить процесс.

```bash
pkill -15 firefox
```

#### 9 (KILL): убить процесс.

```bash
pkill -9 firefox
```

#### По умолчанию без аргументов 15 (TERM): аккуратно завершить процесс.

```bash
pkill '^ssh$'
```

## Отключить устройство программно DEBIAN 12

#### Отключаем модуль WIFI:

```bash
sudo lspci -knn | grep -iA2 'wire'
sudo nano /etc/modprobe.d/intel-microcode-blacklist.conf
добавить:
blacklist rt2800pci
```

#### Создать файл и записать произвольными данными:

```bash
dd if=/dev/random of=test_test3.raw bs=1G count=1024
```

#### Создать файл и записать нулями:

```bash
dd if=/dev/zero of=test_test3.raw bs=1G count=1024
```


## Проверить скорость чтение/запись диска

#### Скорость записи 1Gb данных:
```bash
time $(dd if=/dev/zero of=test.raw bs=1M count=1024 && sync)
sync; dd if=/dev/zero of=tempfile bs=1M count=1024; sync
```

#### Скорость чтения 1Gb данных:

```bash
dd if=tempfile of=/dev/null bs=1M count=1024
```


## NC (NETCAT)

#### Копировать файлы по сети с помошью: nc

На "сервере":  

```bash
nc -lp 9898 -q 1 < test.zip
```

На "клиенте":  

```bash
nc 192.168.1.100 9898 > test_new.zip
```


## Прерывания

```bash
cat /proc/interrupts
```

### Задать имя хоста
sudo hostnamectl set-hostname proxmox

### PING сразу нескольких машин по очереди.

```bash
for i in {18..19}; do ping -c3 "192.168.1.$i"; done
```

### Список всех открытых портов сервера:

```bash
netstat -tulpn
ss -tulpn
ss -tanp | grep 6514
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

### Переименовать файл
```bash
sudo cp file1.txt{,.bak}
```

### Поиск файла
```bash
sudo find / -iname *postgresql.conf*
sudo find / -iname 'postgresql.????' - искать текст за которыми следуют еще 4 символа
sudo find / -name zabbix.conf.php
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

#### Посмотреть хеш файла ключа SSH ECDSA

```bash
cd /etc/ssh
sudo ssh-keygen -l -E sha256 -f ssh_host_ecdsa_key.pub
```
#### Сгенирировать новую пару файлов ключа SSH ECDSA

```bash
cd /etc/ssh
sudo ssh-keygen -t ecdsa
указать путь к файлу: /etc/ssh/ssh_host_ecdsa_key
```
#### Удалить хеш файла ключа SSH ECDSA из ./known_hosts

```bash
sudo ssh-keygen -f "/home/root/./ssh/known_hosts" -R 192.168.1.20
```

## Journalctl

```bash
journalctl -S "2024-04-24 10:00:00" -U "2024-04-24 19:00:00"
journalctl -n 10 -u NetworkManager.service
journalctl -F _UID
journalctl _UID=1000
```

## LOGROTATE

```bash
nano /etc/logrotate.d/mylog.conf
```
содержимое:
> /var/log/my.log {  
        rotate 7  
        daily  
        maxsize 10K  
        compress  
        missingok  
        notifempty }
> 
проверить как работает:  
```bash
logrotate /etc/logrotate.conf
```

## Перенастройка системных региональных настроек (locales):

#### Запустить команду:
В псевдографике, снять с выделение en_US.UTF-8 UTF-8 выбрать ru_RU.UTF-8 UTF-8 +OK и еще раз выбираем ru_RU.UTF-8 UTF-8 +OK

```bash
sudo localedef ru_RU.UTF-8 -i ru_RU -fUTF-8 ; \
export LANGUAGE=ru_RU.UTF-8 ; \
export LANG=ru_RU.UTF-8 ; \
export LC_ALL=ru_RU.UTF-8 ; \
sudo locale-gen ru_RU.UTF-8 ; \
sudo dpkg-reconfigure locales
```

## Восстановление загрузки ПК с Debian12 после установки обовлений.
#### Проблема в том, что слетает драйвер NVidia. Помогает переустановка драйвера.

#### Изменяем загрузку GRUB.

Выбираем строку, которая начинается со слова linux или kernel.  
Удаляем из нее слова quiet и splash, если они есть, и дописываем в конец **init=/bin/bash**:  

![image](https://github.com/user-attachments/assets/de0e1cb1-223b-4021-981b-cfe864a7f5be)

```
params 'Debian GNU/Linux'

load_video
insmod gzio
if [ x$grub_platform = xen ]; then insmod xzio; insmod lzopio; fi
insmod part_msdos
insmod ext2
set root='hd1,msdos1'
if [ x$feature_platform_search_hint = xy ]; then
  search --no-floppy --fs-uuid --set=root --hint-bios=hd1,msdos1 --hint-efi=hd1,msdos1 --hint-baremetal=ahci1,msdos1 86cd8758-3b3e-4191-918f-77b3d08f1749
else
  search --no-floppy --fs-uuid --set=root 86cd8758-3b3e-4191-918f-77b3d08f1749
fi
echo 'Loading Linux 6.1.0-25-amd64 ...'
linux /boot/vmlinuz-6.1.0-25-amd64 root=UUID=86cd8758-3b3e-4191-918f-77b3d08f1749 rw init=/bin/bash
echo 'Loading initial ramdisk ...'
initrd /boot/initrd.img-6.1.0-25-amd64
```
![image](https://github.com/user-attachments/assets/58633f95-9ef4-4395-a818-158abcba8771)


```bash
systemctl set-default --force multi-user.target
```
```bash
su -
```
```bash
/home/user/Downloads/NVIDIA-Linux-x86_64-550.76.run
```
```bash
systemctl set-default --force graphical.target
```
#### Проверть, что драйвер добавлен в initramfs:
```bash
apt install dracut-core
lsinitrd /boot/initrd.img-6.1.0-28-amd64 | grep nvidia
```

## Virtualbox
#### Список всех виртуалок:
```bash
vboxmanage list vms
vboxmanage list vms --long | egrep '^(Name|State)'
```
### Запустить виртуалку:
#### В оконном режиме
```bash
vboxmanage startvm ubuntu1604
```
#### В режиме headless
```bash
vboxmanage startvm --type headless server1
```
#### Создать полный клон ВМ:
```bash
vboxmanage clonevm ubuntu1 --name server3 --register
```

#### Pause, Resume, Poweroff:
```bash
vboxmanage controlvm server3 pause
vboxmanage controlvm server3 resume
vboxmanage controlvm server3 poweroff
```

[Управление VirtualBox из консоли с помощью vboxmanage](https://eax.me/vboxmanage)



---
##############################################################################################

# WINDOWS

## Команды

### TRIM для SSD в Windows


#### Как проверить опцию TRIM
```bash
fsutil behavior query disabledeletenotify
```
> TRIM Вкючен = **0**  
> TRIM Откючен = **1**

#### Включить опцию TRIM
```bash
fsutil behavior set disabledeletenotify NTFS 0
```

### Информация о диске

#### Запрос сведений о томе NTFS
```bash
fsutil fsinfo ntfsinfo z:
```
#### Отобразить сведения о секторах для диска
```bash
fsutil fsinfo sectorinfo g:
```

#### Для получения статистических данных отдельного тома
```bash
fsutil fsinfo statistics z:
```

#### Свойство компьютера

```
Свойство компьютера
sysdm.cpl

Брандмауэр Защитника Windows
firewall.cpl
```

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

#### Посмотреть текущие активные RDP сессии по сети 

```
query session /server:Server1
или
Invoke-Command -ComputerName Server1 -ScriptBlock {query session}
```
#### Подключиться к удаленной RDP сессии по по RDP 

```
Mstsc /v:10.0.0.100 /shadow:2 /control /Prompt
Mstsc /v:10.0.0.100 /shadow:2 /control /Prompt /noConsentPrompt
```

Если ошибка:  

![image](https://github.com/user-attachments/assets/402e62e6-8558-4c82-9ba2-d1cbf32627f3)

```
gpedit.msc
```
> Computer Configuration -> Administrative Templates -> Windows components -> Remote Desktop Services -> Remote Session Host -> Connections  
> Конфигурация Компьютера -> Административные шаблоны –> Компоненты Windows –> Службы удаленных рабочих столов – Узел сеансов удаленных рабочих столов –> Подключения

![image](https://github.com/user-attachments/assets/c63f70f5-a247-4d3e-ae39-f36ce2a93e3b)


## Active directory

Утилита позволяет подключиться к AD на более глубоком уровне  

```bash
adsiedit.msc
```



## Connecting to Hyper-V VM console via RDP

#### Get VM ID
```bash
Get-VM -Name Test-VM-01 | Select-Object id
```

#### Create a new .rdp file
```bash
full address:s:10.0.1.20
pcb:s:ca76a5af-bd93-4be9-82f8-135e29e99172
server port:i:2179
negotiate security layer:i:0
```


## Брандмауэр Защитника Windows - Firewall

#### Отключить/включить Брандмауэр Защитника Windows - Firewall

```bash
netsh advfirewall set all state off/on
```
