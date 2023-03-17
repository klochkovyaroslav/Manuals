# Commands

```bash
sudo nano ansible.cfg  
```

```
[defaults]
host_key_checking = falle #Отключить в ssh проверку thingerprint
inventory = ./hosts
```  

```bash
sudo nano ./hosts  
```
```
[servers]
192.168.56.150 ansible_user=vagrant  ansible_private_key_file=/home/vagrant/.ssh/authorized_keys
```  
Запустить проверку ping на всех хостах из файла hosts  

```bash
ansible all -m ping
```
