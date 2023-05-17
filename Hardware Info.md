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

#посмотреть информацию о cpu  
sudo dmidecode -t processor

#ram информация  
sudo dmidecode -t memory  

#информация о bios  
sudo dmidecode -t bios
