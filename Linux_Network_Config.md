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
ip link set enp0s3 down
ip link set enp0s3 up
```


#### IPv4 Configuration

```bash
 sudo nano /etc/network/interfaces
```

iface enp4s0 inet static  
        address 10.xxx.xxx.5$  
        netmask 255.255.255.0  
        gateway 10.xxx.xxx.1  
        dns-nameservers x.x.x.x, x.x.x.x  
        dns-domain pve.example.com  
        dns-search pve.example.com  


#### IPv4 Configuration

```bash
 sudo nano /etc/sysconfig/network/ifcfg-eth0
```

#### Default Gateway

```bash
sudo nano /etc/sysconfig/network/ifroute-eth0
```

