# Linux Network Configuration

## SUSE Linux15

### GUI Configuration

```bash
 sudo yast2
```

### CLi Terminal Configuration

#### IPv4 Configuration

```bash
 sudo cat /etc/sysconfig/network/ifcfg-eth0
```

#### Default Gateway

```bash
sudo cat /etc/sysconfig/network/ifroute-eth0
```

#### DNS Configuration

```bash
sed -i 's/NETCONFIG_DNS_STATIC_SERVERS=""/NETCONFIG_DNS_STATIC_SERVERS="192.168.0.167 10.0.0.10"/' /etc/sysconfig/network/config
sed -i 's/NETCONFIG_DNS_STATIC_SEARCHLIST=""/NETCONFIG_DNS_STATIC_SEARCHLIST="test.org"/' /etc/sysconfig/network/config
netconfig update –f
```


## Debian 11


```bash
lshw -C network
```

```bash
ip link set enp0s3 down
ip link set enp0s3 up
```


#### IPv4 Configuration

```bash
 sudo nano /etc/network/interfaces
```


> *#iface enp4s0 inet dhcp  
> iface enp4s0 inet static  
> address 192.168.88.20  
> netmask 255.255.255.0  
> gateway 192.168.88.1* 
> *#dns-servers 192.168.88.1*

```bash
 sudo systemctl restart networking.service
 ifdown eth0
 ifup eth0

```


#### DNS

```bash
sudo nano /etc/resolv.conf
sudo nano /etc/resolvconf/resolv.conf.d/head
sudo systemctl restart resolvconf.service
```

#### Проверить имя сервиса

```bash
sudo systemctl list-unit-files | grep -i network
```

#### Рестарт сетевого сервиса

```bash
systemctl restart networking.service
```
----
# NetworkManager
###  NMTUI - Утилита с псевдографикой

```bash
sudo nmtui
```

### NMCLI - Утилита

```bash
sudo nmcli
sudo nmcli device status
sudo nmcli device show
sudo nmcli connection show --active (список активных профилей)
sudo nmcli connection show System\ eth1
```

#### Сделать настройку сети

```bash
sudo nmcli connection modify Wired\ connection\ 1 ipv4.method 'manual' ipv4.addresses '192.168.1.40/24' ipv4.gateway '192.168.1.1'  ipv4.dns '192.168.1.1'
```
#### Добавить DNS адрес
```bash
sudo nmcli connection modify Wired\ connection\ 1 +ipv4.dns '192.168.1.1'
```

#### Настройка VLAN на физической сетевой карте
```bash
sudo nmcli connection add type vlan con-name vlan100 ifname eth0.100 dev eth0 id 100
sudo nmcli connection modify vlan100 +ipv4.dns 8.8.8.8 +ipv4.addresses 192.168.1.10/24 +ivp4.gateway 192.168.1.1
```

### Создания и настройки интерфейса BOND
#### 1. Создание bond-интерфейса
```bash
sudo nmcli connection add type bond con-name bond0 ifname bond0 bond.options "mode=802.3ad,miimon=100"
```
#### 2. Включение автоматического подключения slave-интерфейсов(физ. интерфейсов)
```bash
sudo nmcli connection modify bond0 connection.autoconnect-slaves yes
```
#### 3. Отключает использование IPv4/IPv6 для bond интерфейса
```bash
sudo nmcli connection modify bond0 ipv4.method disabled
sudo nmcli connection modify bond0 ipv6.method disabled
```
#### 4. Добавляет физический интерфейс eno49 и eno50 в bond-интерфейс bond0 с именами подключения bond0-port1 и bond0-port1 соответсвенно
```bash
nmcli connection add type ethernet slave-type bond con-name bond0-port1 ifname eno49 master bond0
nmcli connection add type ethernet slave-type bond con-name bond0-port2 ifname eno50 master bond0
```
### Создания и настройки интерфейса VLAN поверх Bond:
#### 1. Создание VLAN-интерфейса
```bash
nmcli connection add type vlan con-name bond0.21 ifname bond0.21 vlan.parent bond0 vlan.id 21
```
#### 2. Настройка IP-адреса и шлюза для VLAN-интерфейса и включает автоматическое подключение интерфейса при загрузке системы
```bash
nmcli connection modify bond0.21 ipv4.addresses 10.250.21.231/24 ipv4.gateway 10.250.21.250 ipv4.method manual autoconnect yes
```
#### 3. Активация VLAN-интерфейса
```bash
nmcli connection up bond0.21
```

#### Применить конфигурацию:  
```bash
sudo systemctl restart NetworkManager
sudo nmcli connection up Wired\ connection\ 1
```
