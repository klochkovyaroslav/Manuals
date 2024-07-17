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

## Настройка аутентификации на базе ключа а zabbix

### Перенос парофили пользователя в "стандарное место" /home/zabbix из CentOS папка /var/lib/zabbix, для Debian она /var/run/zabbix.
#### Для изменения этой настройки у аккаунта zabbix пользователя все работающие процессы, которые его используют должны быть остановлены:

```bash
sudo systemctl stop zabbix-server.service
sudo systemctl stop zabbix-agent.service
```

#### Изменения размещения домашней папки

```bash
test -d /home/zabbix || mkdir /home/zabbix
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

#### Скопировать публичный ключ на тот сервер к кототому будет подключаться zabbix
для Linux  

```bash
sudo -u zabbix ssh-copy-id root@192.168.1.100
```

для CISCO NEXUX 9XXX  
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
