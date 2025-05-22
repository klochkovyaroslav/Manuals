# Получаем информацию о железе сервера в Linux

```bash
lswh

lshw -short

lshw -class disk

lshw -html > server_info.html

lscpu

lspci

lspci | grep -i 'net'

lsscsi

lsusb

lsblk

df -H

free -m

free -g

vmstat -s
```

## dmidecode

```bash
#посмотреть информацию о cpu  
sudo dmidecode -t processor

#ram информация  
sudo dmidecode -t memory  

#информация о bios  
sudo dmidecode -t bios

#информации о материнской плате
dmidecode --type baseboard
```

## ФАЙЛЫ /PROC

```bash
#cpu информация  
cat /proc/cpuinfo

#cpu информация - поддержка виртуализации?
grep flags /proc/cpuinfo | head -n1 | grep -Eo '(vmx|svm|nx)'

#информация о памяти  
cat /proc/meminfo

#SCSI/Sata устройства:  
cat /proc/scsi/scsi

#Партиции диска
cat /proc/partitions
```

## Информация о жестких дисках сервера в Linux

```bash
yum install hdparm -y

hdparm -I /dev/sdb

smartctl -d ata -a -i /dev/sdb
```

## Информация о Видеоадаптере

```bash
sudo lspci | grep -E "VGA|3D"
sudo lshw -c video
```

#### Проверка нормального функционирования установленных драйверов осуществляется командой
```bash
sudo lspci -vnn | grep -i VGA -A 18
```
> найти «kernel driver in use: NVIDIA»


#### Проверка Поддержки аппаратного ускорения проверяется через утилиту glxinfo
##### Установить glxinfo
```bash
sudo apt install mesa-utils
```
```bash
sudo glxinfo | grep OpenGL | grep renderer
```

#### используемый видеодрайвер:
```bash
sudo lshw -c video | grep driver
```

#### посмотреть количество памяти, доступное видеокарте:
```bash
glxinfo | egrep -i 'device|memory'
```
