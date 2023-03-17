# Commands
## ansible.cfg

```bash
sudo nano ansible.cfg  
```
```
[defaults]
#Отключить в ssh проверку thingerprint  
host_key_checking = false 
inventory = ./hosts
```  
---
## Файл Inventory или hosts

```bash
sudo nano ./hosts  
```

#### Можно указать одиночные сервера без группы:  

192.168.56.10  
192.168.56.21  

или так:
```
Linux_pg ansible_host=192.168.56.250  
Linux_ngx ansible_host=192.168.56.251  
DEB_srv_ubuntu ansible_host=192.168.56.252  
RPM_srv_centos ansible_host=192.168.56.253  
```

#### Можно создать группы серверов:
```
[staging_DB]  
192.168.56.50  
192.168.56.51  

[staging_WEB]  
192.168.56.100  
192.168.56.101  

[staging_APP]  
192.168.56.200  
192.168.56.201  

[prod_DB]  
192.168.56.160  
192.168.56.161 
```

#### Можно создать новую группу серверов из уже существующих групп:
```
[staging_ALL:children]  
staging_DB  
staging_WEB  
staging_APP 
```
или так:
```
[DB_ALL:children]  
staging_DB  
prod_DB  
```
```
[servers]
Linux_test2 ansible_host=192.168.56.150 ansible_user=vagrant  ansible_private_key_file=/home/vagrant/.ssh/authorized_keys
Linux_test2 ansible_host=192.168.56.155 ansible_user=vagrant  ansible_private_key_file=/home/vagrant/.ssh/authorized_keys
``` 

Если в одной группе серверов есть одинаковые значения можно/НУЖНО объединять:  
Будет выглядеть так:  

```
[servers]  
Linux_test2 ansible_host=192.168.56.150
Linux_test2 ansible_host=192.168.56.155

[servers:vars]  
ansible_user=vagrant  
ansible_private_key_file=/home/vagrant/.ssh/authorized_keys  
```
#### Посмотреть список всех хостов в inventory:  

```bash
ansible-inventory --list  
ansible-inventory --graph  
```

#### Запустить проверку ping на всех хостах из файла hosts:  
Запускаем Ansible:  

```bash
ansible all -m ping
```





---

## Для работы клиентских Windows серверов  

На Ansible сервере Нужно установить библиотеку **winrm**  

```bash
sudo pip install "pywinrm>=0.2.2"
```

файл ./hosts: 

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
