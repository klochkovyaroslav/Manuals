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
Linux_test ansible_host=192.168.56.150 ansible_user=vagrant  ansible_private_key_file=/home/vagrant/.ssh/authorized_keys
``` 
Для работы клиентских Windows серверов файл ./hosts: 

```
[windows_servers]
windows 2012 ansible_host=192.168.56.115  

[windows_servers:vars]  
ansible_user = admin  
ansible_password = p@ss  
ansible_port = 5986  
ansible_connection = winrm  
ansible_winrm_server_cert_validation = ignore  
```


Запустить проверку ping на всех хостах из файла hosts  

```bash
ansible all -m ping
```
