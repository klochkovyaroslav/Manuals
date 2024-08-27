# Zabbix и все что с ним связано

#### Мониторинг загрузки процессора
```
**Item:**
Key - perf_counter[\Processor Information(_Total)\% Processor Utility]
Type of information - Numeric (float)
Units - %

**Tags:**
Name - component
Value - cpu
```

## Настройка аутентификации на базе ключа в zabbix

### Перенос парофили пользователя в "стандарное место" /home/zabbix из CentOS папка /var/lib/zabbix, для Debian она /var/run/zabbix.
#### Для изменения этой настройки у аккаунта zabbix пользователя все работающие процессы, которые его используют должны быть остановлены:

```bash
sudo systemctl stop zabbix-server.service
sudo systemctl stop zabbix-agent.service
```

#### Изменения размещения домашней папки

```bash
sudo test -d /home/zabbix || sudo mkdir /home/zabbix
cat /etc/passwd | grep zabbix
sudo usermod -m -d /home/zabbix zabbix
cat /etc/passwd | grep zabbix
```

#### Установка разрешений к домашней папке:

```bash
sudo chown zabbix:zabbix /home/zabbix
sudo chmod 700 /home/zabbix
```

#### Изменения в конфигурации сервера для поддержки ssh ключей

```bash
sudo nano /etc/zabbix/zabbix_server.conf
```
#### Расскомментировать (# SSHKeyLocation=)
Это +- 602 строка, или для поиска строки в NANO нажать CTRL+W ввести _# SSHKeyLocation=_  

```
SSHKeyLocation=/home/zabbix/.ssh
```

#### Запустить ранее остановленные процессы:

```bash
sudo systemctl start zabbix-server.service
sudo systemctl start zabbix-agent.service

sudo systemctl status zabbix-server.service
sudo systemctl status zabbix-agent.service
```

### Генерирования публичных и приватных ключей в /home/zabbix/.ssh
-N "" - не запрашивать passphrase  

```bash
sudo -u zabbix ssh-keygen -t rsa -N ""
```

#### Скопировать публичный ключ (id_rsa.pub) на тот сервер к кототому будет подключаться zabbix
для Linux:  

```bash
sudo -u zabbix ssh-copy-id root@192.168.1.100
```

для CISCO NEXUX 9XXX:  
Подключиться к CISCO NEXUX 9XXX по ssh

```bash
configure terminal
```
в одну строку:  
```bash
username my_user sshkey
ssh-rsa AAAAC3dzaC3yc2EAAeABIwADAIEAy19oF6QaZl9G+3f1XswK3OiW4H7YycyuA50rv7gsEPj
jqBYmsi6PAVKui1nIf/DQhum+fJNqJP/eLowb7ubO+lVKRXFY/G+lJNIQW3g9igG30c6k6+
VGn+NjnI1B7ihvpVh7dLddMOXwOnXHYshXmSiH3UD/vKyziEh5S4Tplx8=
```

```bash
exit
```
```bash
show user-account
```
```bash
copy running-config startup-config
```

####  Проверка подключения по SSH по ключу:
Что бы проверить подключение к CISCO NEXUX 9XXX по ssh с zabbix-сервера под пользователем _zabbix_:  

```bash
sudo -u zabbix ssh -i /home/zabbix/.ssh/id_rsa my_user@192.168.10.100
```

## Пример bash скрипта по мониторингу ospf на cisco nexus 9000 для zabbix

#### Авторизация по логину + пароль

```bash
#!/bin/bash
account="sshro"
ip="192.168.1.77"
command="show ip ospf neighbor"
vlan="Vlan100"

#mts=$( sshpass -f /usr/lib/zabbix/externalscripts/.sshf ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o LogLevel=quiet sshro@10.250.1.77 'show ip ospf neigh' | grep FULL | awk '{print $7}' | grep 'Vlan100')
#mts=$( sshpass -f /usr/lib/zabbix/externalscripts/.sshf ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o LogLevel=quiet ${account}@${ip} 'show ip ospf neigh' | grep FULL | awk '{print $7}' | grep 'Vlan100')
beeline=$( sshpass -f /usr/lib/zabbix/externalscripts/.sshf ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o LogLevel=quiet ${account}@${ip} ${command} | grep FULL | awk '{print $7}' | grep ${vlan})
if [[ $beeline = ${vlan} ]]
then echo '1'
else
echo '0'
fi
```

#### Авторизация по ssh ключу

```bash
#!/bin/bash
account="sshro"
ip="192.168.1.77"
command="show ip ospf neighbor"
vlan="Vlan743"

beeline=$(ssh -i /home/zabbix/.ssh/id_rsa -o LogLevel=quiet ${account}@${ip} ${command} | grep FULL | awk '{print $7}' | grep ${vlan})
if [[ $beeline = ${vlan} ]]
then echo '1'
else
echo '0'
fi
```
#### Запуск скрипта от пользователя zabbix:

```bash
sudo /lib/zabbix/externalscripts/mon_ospf_core1
```

---

# Обновить версию Zabbix с 6.4 до 7 в Debian 12.

## Подготовка к обновлению.

#### Остановить сервер и агент.

```bash
sudo systemctl stop zabbix-server zabbix-agent
```


#### Сделать бэкап базы данных PostgreSQL.

```bash
sudo -u postgres pg_dump -U postgres zabbix | /usr/bin/gzip > ~/zabbix.sql.gz
```

#### Сохраним php скрипты админки и все файлы конфигурации.

```bash
sudo mkdir /opt/zabbix-backup/
sudo cp /etc/zabbix/zabbix_server.conf /opt/zabbix-backup/
sudo cp /etc/nginx/conf.d/zabbix.conf /opt/zabbix-backup
sudo cp -R /usr/share/zabbix/ /opt/zabbix-backup
sudo cp -R /usr/share/zabbix-* /opt/zabbix-backup
```

## Установка обновления Zabbix 6.4 до 7.

#### Подключить репозитории 7 версии Zabbix.

```bash
sudo rm -Rf /etc/apt/sources.list.d/zabbix.list
sudo wget https://repo.zabbix.com/zabbix/7.0/debian/pool/main/z/zabbix-release/zabbix-release_7.0-1+debian12_all.deb
sudo dpkg -i zabbix-release_7.0-1+debian12_all.deb
sudo apt update
```



```bash
sudo apt list --installed | grep zabbix
sudo apt-get install --only-upgrade zabbix-server-mysql zabbix-frontend-php zabbix-agent
или
sudo apt upgrade zabbix-server-mysql zabbix-frontend-php zabbix-agent
```

## Запустить сервер и агент.

```bash
sudo systemctl start zabbix-server zabbix-agent
sudo systemctl status zabbix-server zabbix-agent
```

## Если в браузере ошибка:
### "The Zabbix database version does not match current requirements. Your database version: 6040000. Required version: 7000000. Please contact your system administrator"

### Настроить файл


#### Вывести все незакомментированниые и не пустые строки

```bash
sudo grep -Pv "^(#|$)" /etc/zabbix/zabbix_server.conf
или
sudo grep ^[[:alnum:]] /etc/zabbix/zabbix_server.conf
```

LogFile=/var/log/zabbix/zabbix_server.log  
LogFileSize=0  
PidFile=/run/zabbix/zabbix_server.pid  
SocketDir=/run/zabbix  
DBName=zabbix  
DBUser=zabbix  
**DBPassword=P@ssw0rd  
StartPollers=50**  
**StartIPMIPollers=5**  
**StartPollersUnreachable=10**  
**StartPingers=10**  
SNMPTrapperFile=/var/log/snmptrap/snmptrap.log  
**CacheSize=2048M**  
**HistoryCacheSize=128M**  
**ValueCacheSize=1G**  
Timeout=4  
FpingLocation=/usr/bin/fping  
Fping6Location=/usr/bin/fping6  
**SSHKeyLocation=/home/zabbix/.ssh**  
LogSlowQueries=3000  
StatsAllowedIP=127.0.0.1  
EnableGlobalScripts=0  


```bash
sudo nano /etc/zabbix/zabbix_server.conf
```

```bash
nano zabbix_conf_configuration.sh
```

```bash
#!/bin/bash
v_path=/etc/zabbix
#sed -i 's/SSHKeyLocation=/SSHKeyLocation=\/home\/zabbix\/\.ssh/' /etc/zabbix/zabbix_server.conf
sed -i 's|# SSHKeyLocation=|SSHKeyLocation=/home/zabbix/.ssh/|g' ${v_path}//zabbix_server.conf
sed -i -e 's|# StartPollers=5|StartPollers=50|g' ${v_path}//zabbix_server.conf
sed -i -e 's|# CacheSize=32M|CacheSize=2048M|g' ${v_path}//zabbix_server.conf
sed -i -e 's|# StartIPMIPollers=0|StartIPMIPollers=5|g' ${v_path}//zabbix_server.conf
sed -i -e 's|# StartPollersUnreachable=1|StartPollersUnreachable=10|g' ${v_path}//zabbix_server.conf
sed -i -e 's|# StartPingers=1|StartPingers=10|g' ${v_path}//zabbix_server.conf
sed -i -e 's|# HistoryCacheSize=16M|HistoryCacheSize=128M|g' ${v_path}//zabbix_server.conf
sed -i -e 's|# ValueCacheSize=8M|ValueCacheSize=1G|g' ${v_path}//zabbix_server.conf
sed -i -e 's|# DBPassword=|# DBPassword=DBPassword=P@ssWd|g' ${v_path}//zabbix_server.conf
```
