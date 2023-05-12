# Manuals

### NetworkManager service.

```bash
sudo systemctl start NetworkManager
nmcli general status - состояние интерфейсов
nmcli device status
nmcli general hostname - посмотреть имя хоста
nmcli connection show - список доступных подключений

ip addr show
ip -s link show enp4s0 -статистика по интерфейсу
ip route show match 0/0 - посмотреть таблицу маршрутизации

```

```bash
sudo systemctl status NetworkManager.service
sudo systemctl restart NetworkManager.service
```

### PING сразу нескольких машин по очереди.

```bash
for i in {18..19}; do ping -c3 "192.168.1.$i"; done
```

### Повторить какие порты слушает сервер:

```bash
ss -tulpn
```

### Повторить предыдущую команду (ss -tulpn) в bash:

```bash
!ss
или
!!
```

### Скопировать файл по сети на server2 в каталог: root

```bash
sudo scp file1.tar.gz root@server2:/root/
```
### TCPDUMP
https://blog.sedicomm.com/2017/05/30/tcpdump-poleznoe-rukovodstvo-s-primerami/

```bash
sudo tcpdump -i eth0 port '(50001 or 443)'
sudo tcpdump icmp  - конкретный тип трафика
sudo tcpdump dst 192.168.0.100  - чтобы увидеть входящий трафик:
sudo tcpdump net 192.168.0.100/24
sudo tcpdump portrange 8000-9000
sudo tcpdump -vv src mars and not dst port 22
```

## AppArmor в Ubuntu и Debian

```bash
sudo apparmor_status
```
#### Stop apparmor service using systemd  

```bash
sudo systemctl stop apparmor
```

#### Disable apparmor from starting on system boot   

```bash
systemctl disable apparmor
```

#### Remove apparmor package and dependencies using apt (optional)  

```bash
sudo apt remove --assume-yes --purge apparmor
```
