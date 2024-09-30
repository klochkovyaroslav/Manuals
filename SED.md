# SED: Примеры.


#### файлы с именем: test+первая цифра: 2-цать или 3-цать, вторая цифра от 0 до 9 и убрать первую строку из вывода команды
```bash
watch -n 5 "ls -sh /mnt | grep 'test[2-3][09]' | sed '1d'"
```

#### При помощи редактора SED и вывести на экран содержимое файла some_file.txt и предварительно удалить строки содержащие подстороку: "etc", в самом файле строка не удалится, только на экране
```bash
sed '/etc/d' some_file.txt
```
Содержимое файла some_file.txt  
> ggg etc  
ddd etc fff  
bbb fffetc  
nnn  
etc
>
ответ:  
> nnn
#### При помощи редактора SED и вывести на экран содержимое файла some_file.txt и предварительно удалить только кусок самой строки содержащей: "etc", в самом файле строка не удалится, только на экране
```bash
sed 's/etc//g' some_file1.txt
```
Содержимое файла some_file.txt  
> ggg etc  
ddd etc fff  
bbb fffetc  
nnn  
etc
>
ответ:  
> ggg  
ddd  fff  
bbb fff  
nnn
>
#### При помощи редактора SED и вывести на экран содержимое файла some_file.txt и предварительно вставив строку содержащую подстороку: "etc" в начало каждой строки, в самом файле строка не добавляется, только на экране
```bash
sed 's/^/etc /' some_file.txt
```

#### Настройка конфигурации zabbix server
```bash
#!/bin/bash
v_path=/etc/zabbix
#sed -i 's/SSHKeyLocation=/SSHKeyLocation=\/home\/zabbix\/\.ssh/' /etc/zabbix/zabbix_server.conf
sed -i -e 's|# SSHKeyLocation=|SSHKeyLocation=/home/zabbix/.ssh/|g' ${v_path}//zabbix_server.conf
sed -i -e 's|# StartPollers=5|StartPollers=50|g' ${v_path}//zabbix_server.conf
sed -i -e 's|# CacheSize=32M|CacheSize=2048M|g' ${v_path}//zabbix_server.conf
sed -i -e 's|# StartIPMIPollers=0|StartIPMIPollers=5|g' ${v_path}//zabbix_server.conf
sed -i -e 's|# StartPollersUnreachable=1|StartPollersUnreachable=10|g' ${v_path}//zabbix_server.conf
sed -i -e 's|# StartPingers=1|StartPingers=10|g' ${v_path}//zabbix_server.conf
sed -i -e 's|# HistoryCacheSize=16M|HistoryCacheSize=128M|g' ${v_path}//zabbix_server.conf
sed -i -e 's|# ValueCacheSize=8M|ValueCacheSize=1G|g' ${v_path}//zabbix_server.conf
sed -i -e 's|# DBPassword=|DBPassword=P@ssWd|g' ${v_path}//zabbix_server.conf
```

#### DNS Configuration

```bash
sed -i 's/NETCONFIG_DNS_STATIC_SERVERS=""/NETCONFIG_DNS_STATIC_SERVERS="192.168.0.167 10.0.0.10"/' /etc/sysconfig/network/config
sed -i 's/NETCONFIG_DNS_STATIC_SEARCHLIST=""/NETCONFIG_DNS_STATIC_SEARCHLIST="test.org"/' /etc/sysconfig/network/config
netconfig update –f
```

#### PasswordAuthentication 'yes' заменить на 'no'

```bash
sed -i '65s/PasswordAuthentication yes/PasswordAuthentication no/g' /etc/ssh/sshd_config
```

#### SSH config (port, time)
```bash
sed -i 's/#Port 22/Port 6982/' /etc/ssh/sshd_config
sed -i 's/#LoginGraceTime 2m/LoginGraceTime 10s/' /etc/ssh/sshd_config
```

#### Переименовать систему в Linux

```bash
#! /bin/bash
echo Enter new name of host
echo Example: newname.head.energotransbank.com
read hostnm

sed -i 's/'$HOSTNAME'/'$hostnm'/' /etc/sysconfig/network
sed -i 's/'$HOSTNAME'/'$hostnm'/' /etc/sysctl.conf
sed -i 's/'$HOSTNAME'/'$hostnm'/' /etc/hosts
```
