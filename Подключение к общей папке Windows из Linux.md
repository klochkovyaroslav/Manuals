# Подключение к общей папке Windows из Linux


#### Устанавить пакет: cifs-utils
```bash
sudo apt install cifs-utils
```

#### Создать папку куда будем монтировать сетевую папку
```bash
mkdir kuu_d
```
#### Подключить общую папку (CIFS/Samba) с помощью mount
```bash
sudo mount -t cifs -o username=user,password=12345,uid=1000,iocharset=utf8 //192.168.88.28/d kuu_d/
```
