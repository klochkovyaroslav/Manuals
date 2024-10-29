# DNS
## Утилиты

```bash
apt install bind9utils
yum install bind-utils
```

#### host
```bash
host github.com
host -v github.com
```

#### dig
```bash
dig github.com
```
```bash
dig NS github.com
dig MX github.com
dig SOA github.com
dig TXT github.com
```

#### delv

#### dril

#### nslookup
```bash
nslookup github.com
```
----
## Установка сервера DNS BIND

#### Установить bind в Debian/Ubuntu
```bash
sudo apt install bind9 dnsutils
```

#### Установить bind в Centos/RHEL
```bash
sudo yum install bind bind-utils -y
```
```bash
sudo systemctl enable --now named.service
```
#### Основной конфигурационный файл
```bash
sudo vi /etc/named.conf
```

> acl my_local_net { 192.168.56.0/21; };  
> options {  
> logging {  
> zone "." IN {  
> include "/etc/named.rfc1912.zones";  
> include "/etc/named.root.key";


```bash
sudo grep ^[[:alnum:]] /etc/named.conf
```
#### Проверка корректности синтаксиса конфигурации
```bash
sudo named-checkconf
```
#### Проверка на наличие ошибок
```bash
echo $?
```
#### Проверка правил файервола
sudo firewall-cmd --list-all
```
#### Добавление правила файервола для dns BIND
```bash
sudo firewall-cmd --add-service=dns --permanent
```
#### Посмотреть какие запросы были на разрешение имен
```bash
sudo less /var/named/data/named.run
```

### Утилиты

```bash
named-checkconf /etc/named.conf
named-checkzone mydomain.local /vagrant/zone_example.txt
```
