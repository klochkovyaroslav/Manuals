# Получаем информацию о железе сервера в Linux

```bash
lswh

lshw -short

lscpu

lspci

lsscsi

lsusb

lsblk

df -H

free -m
```

## dmidecode

```bash
#посмотреть информацию о cpu  
sudo dmidecode -t processor

#ram информация  
sudo dmidecode -t memory  

#информация о bios  
sudo dmidecode -t bios
```

## ФАЙЛЫ /PROC

```bash
#cpu информация  
cat /proc/cpuinfo  

#информация о памяти  
cat /proc/meminfo  

#SCSI/Sata устройства:  
cat /proc/scsi/scsi  

#Партиции диска
cat /proc/partitions
```
