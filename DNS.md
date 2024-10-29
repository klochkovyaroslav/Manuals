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
#### Добвить в автозагрузку
```bash
sudo systemctl enable --now named.service
```
#### Основной конфигурационный файл
```bash
sudo vi /etc/named.conf
```

```
acl my_local_net { 192.168.56.0/24; };
options {
        listen-on port 53 { 192.168.56.10; };
        listen-on-v6 port 53 { none; };
        forwarders { 77.88.8.8; 77.88.8.1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        secroots-file   "/var/named/data/named.secroots";
        recursing-file  "/var/named/data/named.recursing";
        allow-query     { localhost; my_local_net; };
        recursion yes;
        dnssec-validation yes;
        managed-keys-directory "/var/named/dynamic";
        geoip-directory "/usr/share/GeoIP";
        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";

        include "/etc/crypto-policies/back-ends/bind.config";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

zone "klochkov.int" IN {
        type master;
        file "klochkov.int.zone";
};

zone "0.168.192.in-addr.arpa" IN {
        type master;
        file "klochkov.int.rev.zone";
};

zone "." IN {
        type hint;
        file "named.ca";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
```

```bash
sudo grep -Pv "^(#|$)" /etc/named.conf
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
```bash
sudo firewall-cmd --list-all
```
#### Добавление правила файервола для dns BIND
```bash
sudo firewall-cmd --add-service=dns
sudo firewall-cmd --add-service=dns --permanent
```
#### Посмотреть какие запросы были на разрешение имен
```bash
sudo less /var/named/data/named.run
```
----
### Конфигурирование доменных зон
#### Создаем новый файл зоны прямого просмотра копированием файла: /var/named/named.localhost в новый файл: /var/named/klochkov.int.zone
```bash
sudo cp /var/named/named.localhost /var/named/klochkov.int.zone
```

#### Нужно сменить владельца группы для нового файла
```bash
sudo chgrp named /var/named/klochkov.int.zone
```

#### Конфигурация файла зоны прямого просмотра
```bash
sudo vi /var/named/klochkov.int.zone
```
```
$TTL 86400
@       IN SOA  server1.klochkov.int. klochkovyaroslav.gmail.com. (
                                        2024102904      ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
; name servers - NS records - определяем имена DNS-серверов
        IN      NS      server1.klochkov.int.

server1.klochkov.int.   IN      A       192.168.56.10
server2.klochkov.int.   IN      A       192.168.56.11
server3.klochkov.int.   IN      A       192.168.56.12
server4.klochkov.int.   IN      A       192.168.56.13
```
#### Проверка корректности синтаксиса конфигурации
```bash
sudo named-checkzone klochkov.int /var/named/klochkov.int.zone
```

#### Конфигурация файла зоны обратного просмотра
```bash
sudo vi /var/named/klochkov.int.rev.zone
```

#### Нужно сменить владельца группы для нового файла
```bash
sudo chgrp named /var/named/klochkov.int.rev.zone
```
```
$TTL 86400
@       IN SOA  klochkov.int. klochkovyaroslav.gmail.com. (
                                        2024102904      ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        IN      NS      server1.klochkov.int.

10      IN      PTR     server1.klochkov.int.
11      IN      PTR     server2.klochkov.int.
12      IN      PTR     server3.klochkov.int.
13      IN      PTR     server4.klochkov.int.
```

#### Проверка корректности синтаксиса конфигурации
```bash
sudo named-checkzone 0.168.192.in-addr.arpa /var/named/klochkov.int.rev.zone
```
#### Перечитываем конфигурацию зон в BIND 9
```bash
sudo rndc reload
sudo kill -HUP `ps aux | grep named | grep -v grep | awk '{print $2}'`
sudo systemctl reload named.service
sudo systemctl status named.service
```
#### Для проверки наличия SOA-записи с другого хоста в сети 
```bash
dig @192.168.56.10 -t SOA klochkov.int
```

