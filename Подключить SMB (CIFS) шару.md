# Подключить общую сетевую папку Windows по SMB (CIFS)

#### установить утилиту: cifs-utils.

```bash
sudo apt-get install cifs-utils
или
sudo dnf install cifs-utils
```

#### Создать точку монтирования:
```bash
$ sudo mkdir /mnt/share
```

#### Смонтировать сетевую папку

```bash
sudo mount -t cifs -o username=User_test,password=Paswd,uid=1000,iocharset=utf8 //192.168.0.200/backup /mnt/share
```
или

```bash
sudo mount -t cifs -o credentials=/home/sysops/.windowscredentials,uid=1000,iocharset=utf8 //192.168.0.200/backup /mnt/share
```

или 
#### Автоматическое монтирование сетевой папки Windows через: /etc/fstab

```bash
sudo nano /etc/fstab
```

```bash
//192.168.0.200/backup /mnt/share cifs user,rw,credentials=/home/sysops/.windowscredentials,iocharset=utf8,nofail,_netdev,vers=3.0 0 0
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
*`//192.168.0.200/backup – сетевая папка Windows`*  
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

