# Manuals

### PING сразу нескольких машин по очереди.

```bash
for i in {18..19}; do ping -c3 "192.168.1.$i"; done
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
