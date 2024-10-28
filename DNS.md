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

## Установка сервера DNS BIND

```bash
apt install bind9 bind9utils dnsutils
```
##### Конфиг файл
> /etc/bind/named.conf

```bash
yum install bind bind-utils
```
##### Конфиг файл
> /etc/named.conf

### Утилиты

```bash
named-checkconf /etc/named.conf
named-checkzone /vagrant/zone_example.txt
```
