# Wireguard

--- 


# Annezia WG



```bash
sudo bash /root/awg/manage_amneziawg.sh add win10
```


```bash
scp -P 6982 -o ProxyCommand="nc -X 5 -x 192.168.21.4:1080 %h %p" root@95.81.74.60:/root/awg/win10.conf ~/
```
