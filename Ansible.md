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
Запустить проверку ping на всех хостах из файла hosts:  
Запускаем Ansible:  

```bash
ansible all -m ping
```





---

## Для работы клиентских Windows серверов файл ./hosts: 

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
На серверах windows нужно один раз предварительно зарустить **от АДМИНИСТРАТОРА** скрипт: _ConfigureRemotingForAnsible.ps1_  

Для проверки доступности сервера:  
Запускаем Ansible:  

```bash
ansible windows_servers -m win_ping
```
Что бы не указывать пароль в открытом виде в ./hosts:  

Запускаем Ansible:
```bash
ansible windows_servers -m win_ping --ask-pass
```  
