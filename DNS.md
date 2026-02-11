# DNS

### Как посмотреть DNS
```bash
sudo cat /etc/resolv.conf
sudo cat /etc/systemd/resolved.conf
sudo nmcli device show | grep DNS
sudo systemd-resolve --status
sudo resolvectl status
```

## Утилиты

```bash
apt install bind9utils
yum install bind-utils
```

#### Утилитa host
```bash
host github.com
host -v github.com
```

#### Утилитa dig
```bash
dig github.com
```
```bash
dig NS github.com
dig MX github.com
dig SOA github.com
dig TXT github.com
```

#### Утилитa delv

#### Утилитa dril

#### Утилитa nslookup
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
### Основные файлы конфигурации
##### Centos/RHEL
```bash
sudo vi /etc/named.conf
```
##### Debian13

```bash
nano /etc/bind/named.conf
```
Содержание файла:  
```
// This is the primary configuration file for the BIND DNS server named.
//
// Please read /usr/share/doc/bind9/README.Debian for information on the
// structure of BIND configuration files in Debian, *BEFORE* you customize
// this configuration file.
//
// If you are just adding zones, please do that in /etc/bind/named.conf.local

include "/etc/bind/named.conf.options";
include "/etc/bind/named.conf.local";
include "/etc/bind/named.conf.root-hints";
include "/etc/bind/named.conf.internal-zones"; // Insert My own file zone
```

##### Centos/RHEL
```bash
sudo vi /etc/named.conf
```
Содержание файла:  
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
##### Еще пример конфигурационного файла в Debian13
```bash
sudo nano /etc/bind/named.conf.options
```
```bash
acl trusted_hosts {
        10.100.1.1; //esxi host: ESXI-01
        10.100.1.2; //esxi host: ESXI-02
        10.100.4.99; //vm host: RDS-01
        10.100.4.100; //vm host: RDS-02
        10.100.4.244; //vm: DNS1
        10.100.4.199; //vm: GW-01
        10.100.4.200; //vm: GW-02
};

acl internal-network {
        10.100.1.0/24;
        10.100.4.0/24;
        //127.0.0.0/8;
};

options {
        listen-on port 53 { localhost; 10.250.21.244; }; // IP-адреса интерфейсов, которые слушает BIND
        listen-on-v6 port 53 { none; };
        allow-query { localhost; internal-network; }; // Кто может отправлять обычные DNS-запросы
        allow-query-cache { localhost; internal-network; }; // Кто может запрашивать данные из кэша
        recursion yes; // Включить рекурсивные запросы для клиентов
        allow-recursion { localhost; internal-network; }; // Кому разрешена рекурсия
        forwarders { 77.88.8.8; 77.88.8.1; };
        directory "/var/cache/bind";
        allow-transfer { localhost; };
        dnssec-validation auto;
        version none;
        auth-nxdomain no;

        // Блокировать запросы с подозрительных или зарезервированных адресов
        // blackhole { 0.0.0.0/0; ::/0; };
};

logging {
        // Общий канал для всех запросов в DNS
        channel default_ch {
                file "/var/cache/bind/logs/named.log" versions 4 size 100k;
                severity info;

                // Стандартные параметры:
                print-time yes;          // Время события
                print-category yes;      // Категория (queries, security и т.д.)
                print-severity yes;      // Уровень серьезности (info, warning и т.д.)

               // Для отладки:
               // severity debug 5;     // Детальный уровень отладки
               };

        channel query_log {
                file "/var/cache/bind/logs/query.log" versions 4 size 100k;
                severity info;
                print-time yes;
                print-category yes;
                };

        // Канал для упрощенных запросов
        channel lame_ch {
                file "/var/cache/bind/logs/lame_servers.log" versions 4 size 100k;
                severity info;
                print-time yes;
                print-category yes;
                };

        // Канал безопасности
        channel security_ch {
                file "/var/cache/bind/logs/security.log" versions 4 size 100k;
                severity info;
                print-severity yes;
                print-time yes;
                print-category yes;
                };

        // Канал передачи зон между серверами
        channel xfer_ch {
                file "/var/cache/bind/logs/xfer.log" versions 4 size 100k;
                severity info;
                print-time yes;
                print-category yes;
                };

        // Канал отладки обновлений зон
        channel update_debug {
                file "/var/cache/bind/logs/update_debug.log" versions 4 size 100k;
                severity debug 3;
                print-category yes;
                print-severity yes;
                print-time     yes;
                };

        // Канал обновлений зон
        channel update_log {
                file "/var/cache/bind/logs/update.log" versions 4 size 100k;
                severity dynamic;
                print-category yes;
                print-severity yes;
                print-time     yes;
                };


    category default { default_ch; };
    category lame-servers { lame_ch; };
    category queries { query_log; };
    category security { security_ch; };
    category update-security { update_debug; };
    category xfer-in  { xfer_ch; };
    category xfer-out { xfer_ch; };
    category update   { update_debug; update_log; };

    // Для отладки кэша можно использовать эти категории:
    category resolver { query_log; };  # Разрешение имен (включая кэш)
    category security { query_log; };  # Запросы, заблокированные из-за политик
};

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

// "logging {"

// Назначение: Начало блока конфигурации логирования
// Объявляет, что следующие параметры относятся к настройке системы логирования BIND

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

// "channel query_log {"

// Назначение: Определение канала логирования с именем query_log
// channel - ключевое слово для создания канала логирования
// query_log - произвольное имя канала (можно использовать любое: my_log, dns_queries и т.д.)
// Канал определяет КУДА и КАК писать логи

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

// file "/var/log/bind/query.log" versions 3 size 5m;
// Назначение: Настройка файла для записи логов
// Описание по частям:
// "file" - указывает, что вывод идет в файл (альтернативы: syslog, stderr, null)
// "/var/log/bind/query.log" - абсолютный путь к файлу логов
// "versions 3" - ротация логов: хранить 3 предыдущие версии файла
// "size" 5m - максимальный размер файла: 5 мегабайт

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

// "severity info;"

// Назначение: Уровень детализации логирования
//Описание: Какие сообщения записывать в лог. Уровни (от наиболее детального к наименее):
// dynamic - динамический уровень (меняется через rndc)
//debug [уровень] - отладка (1-99), где 99 - максимальная детализация
// info - информационные сообщения (стандартный уровень)
// notice - уведомления
// warning - предупреждения
// error - ошибки
// critical - критические ошибки
// fatal - фатальные ошибки

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////


// "print-time yes;"
// Назначение: Включает вывод временной метки в лог

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

// "print-category yes;"
// Назначение: Включает вывод категории сообщения

/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

// Дополнительные параметры:
// Для syslog:
// syslog daemon;        // Вместо file - писать в syslog с facility daemon
// syslog local0;        // Другие facility: auth, user, local0-local7

```
```
Все доступные категории в BIND:
bind
logging {
    channel my_log {
        file "/var/log/bind/all.log" versions 3 size 5m;
        severity info;
        print-time yes;
        print-category yes;
    };
    
    // Основные категории для мониторинга работы:
    category default { my_log; };      // Общие сообщения
    category general { my_log; };      // Общие информационные сообщения
    category queries { my_log; };      // DNS запросы
    category client { my_log; };       // Обработка клиентских запросов
    category resolver { my_log; };     // Рекурсивное разрешение (кэш)
    category security { my_log; };     // Сообщения безопасности
    category dnssec { my_log; };       // DNSSEC-сообщения
    category update { my_log; };       // Динамические обновления
    category xfer-in { my_log; };      // Входящие трансферы зон
    category xfer-out { my_log; };     // Исходящие трансферы зон
    category notify { my_log; };       // NOTIFY-сообщения
    category lame-servers { my_log; }; // "Хромые" серверы
    category cname { my_log; };        // CNAME-записи
    category dispatch { my_log; };     // Диспетчеризация пакетов
    category network { my_log; };      // Сетевые операции
    category config { my_log; };       // Конфигурация
};
```

#### Создать файлы логов в (Debian 13)
```bash
mkdir -p /var/cache/bind/logs

touch /var/cache/bind/logs/lame_servers.log \
          /var/cache/bind/logs/security.log \
          /var/cache/bind/logs/xfer.log \
          /var/cache/bind/logs/update_debug.log \
          /var/cache/bind/logs/update.log \
	  /var/cache/bind/logs/named.log \
          /var/cache/bind/logs/query.log

chown bind:bind /var/cache/bind/logs/bind/*.log
chmod 644 /var/cache/bind/logs/bind/*.log
```

#### Посмотреть конфигурационный файл без комментариев
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
---

### Конфигурирование доменных зон

#### Конфигурационный Файл зон
#### Debian
```bash
sudo nano /etc/bind/named.conf.internal-zones
```
Содержание файла:  
```
zone "esxi.localdomain" {
        type master;
        file "/var/cache/bind/esxi.local.zone";
};

zone "4.100.10.in-addr.arpa" {
        type master;
        file "/var/cache/bind/esxi.local.rev.zone";
};

zone "1.100.10.in-addr.arpa" {
        type master;
        file "/var/cache/bind/esxi.local.host.rev.zone";
};

```

### Зона прямого просмотра

#### Centos/RHEL
#### Создаем новый файл зоны прямого просмотра копированием файла: /var/named/named.localhost в новый файл: /var/named/klochkov.int.zone
```bash
sudo cp /var/named/named.localhost /var/named/klochkov.int.zone
```
#### DEBIAN
#### Создаем новый файл зоны прямого просмотра вручную
```bash
sudo touch /var/cache/bind/esxi.local.zone
```
#### Centos/RHEL
#### Нужно сменить владельца группы для нового файла
```bash
sudo chgrp named /var/named/klochkov.int.zone
```
#### DEBIAN
#### Нужно сменить владельца группы для нового файла
```bash
sudo chown bind:bind /var/cache/bind/esxi.local.zone
```

#### Конфигурация файла зоны прямого просмотра
```bash
sudo vi /var/named/klochkov.int.zone
```
Сожержание файла:  
```
$TTL 86400
@       IN SOA  server1.klochkov.int. klochkovyaroslav.mail.com. (
                                        2024102904      ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
; name servers - NS records - определяем имена DNS-серверов
@	IN	NS	server1.klochkov.int.

server1   IN      A       192.168.56.10
server2   IN      A       192.168.56.11
server3   IN      A       192.168.56.12
server4   IN      A       192.168.56.13
```
Еще пример Конфигурация файла зоны прямого просмотра  
```bash
nano /var/cache/bind/esxi.local.zone
```
Сожержание файла:  
```
$TTL 86400
@       IN SOA DNS-01.esxi.localdomain. yaroslav.klochkov.soft.com. (
                                2026020501      ; serial
                                1D      ; refresh
                                1H      ; retry
                                1W      ; expire
                                3H )    ; minimum
; name servers - NS records
@       IN      NS      DNS-01.esxi.localdomain.

ESXI-01         IN      A       10.100.1.1
ESXI-02         IN      A       10.100.1.2
vcs-01          IN      A       10.100.1.100
RDS-01          IN      A       10.100.4.99
RDS-02          IN      A       10.100.4.100
GW-01           IN      A       10.100.4.199
GW-02           IN      A       10.100.4.200
DNS-01          IN      A       10.100.4.244
```

#### Проверка корректности синтаксиса конфигурации
```bash
sudo named-checkzone klochkov.int /var/named/klochkov.int.zone
```
```bash
sudo named-checkzone esxi.localdomain /var/cache/bind/esxi.local.zone
```
### Зона обратного просмотра
#### Centos/RHEL
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
@       IN SOA  klochkov.int. klochkovyaroslav.mail.com. (
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
#### DEBIAN 13
#### Конфигурация файла зоны обратного просмотра

```bash
 nano /var/cache/bind/esxi.local.rev.zone
```
Содержание файла:  

```bash
$TTL 86400
@       IN SOA DNS-01.esxi.localdomain. yaroslav.klochkov.soft.com. (
                                2026020501      ; serial
                                1D      ; refresh
                                1H      ; retry
                                1W      ; expire
                                3H )    ; minimum
@       IN       NS     DNS-01.esxi.localdomain.

99      IN      PTR     RDS-01.esxi.localdomain.
100     IN      PTR     RDS-02.esxi.localdomain.
199     IN      PTR     GW-01.esxi.localdomain.
200     IN      PTR     GW-02.esxi.localdomain.
244     IN      PTR     DNS-01.esxi.localdomain.
```
#### DEBIAN
#### Нужно сменить владельца группы для нового файла
```bash
sudo chown bind:bind /var/cache/bind/esxi.local.rev.zone
```

Если Хосты и ВМ разных сетях, создать еще один файл для зоны обратного просмотра  

```bash
nano /var/cache/bind/esxi.local.host.rev.zone
```

Содержание файла:  

```bash
$TTL 86400
@       IN SOA DNS-01.esxi.localdomain. yaroslav.klochkov.soft.com. (
                                2026020501      ; serial
                                1D      ; refresh
                                1H      ; retry
                                1W      ; expire
                                3H )    ; minimum
        IN NS DNS-01.esxi.localdomain.

1       IN      PTR     ESXI-01.esxi.localdomain.
2       IN      PTR     ESXI-02.esxi.localdomain.
100     IN      PTR     vcs-01.esxi.localdomain.
```
#### Нужно сменить владельца группы для нового файла
```bash
sudo chown bind:bind /var/cache/bind/esxi.local.host.rev.zone
```


#### Проверка корректности синтаксиса конфигурации
```bash
sudo named-checkzone 0.168.192.in-addr.arpa /var/named/klochkov.int.rev.zone
```
```bash
sudo named-checkzone 4.100.10.in-addr.arpa /var/cache/bind/esxi.local.rev.zone
```
```bash
sudo named-checkzone 1.100.10.in-addr.arpa /var/cache/bind/esxi.local.host.rev.zone
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
```bash
dig @127.0.0.1 google.com
```

```bash
nslookup vcs.esxi.localdomain 10.100.4.244
```

# 🐳 Docker Monitoring Stack — cAdvisor + Prometheus + Grafana
Проект для мониторинга Docker-контейнеров в домашней или тестовой среде. Позволяет отслеживать загрузку CPU, RAM, дисков, сети и состояние каждого контейнера.
---
## 🚀 Компоненты проекта
| Компонент | Назначение |
|----------|------------|
|**cAdvisor**| Сбор метрик Docker-контейнеров |
|**Prometheus**| Хранение и сбор метрик |
|**Grafana**| Визуализация метрик |
|**Node-exporter**| Сбор метрик хостовой ВМ |
---
🐳
