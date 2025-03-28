# Ansible
 
----
 
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
## Файл Inventory или hosts -  Простой вариант написания

```bash
sudo nano ./hosts  
```

#### Можно указать одиночные сервера без группы:  
```
192.168.56.10  
192.168.56.21  
```

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

[windows_servers]
win_test2 ansible_host=192.168.56.200
win_test2 ansible_host=192.168.56.201

[windows_servers:vars]
ansible_user=Admin
ansible_password=P@ssw0rd
ansible_port=5986
ansible_connection=winrm
ansible_winrm_servers_cert_validation_ignore
```
#### Посмотреть список всех хостов в inventory:  

```bash
ansible-inventory --list  
ansible-inventory --graph  
```


## Файл Inventory или hosts -  !!! БОЛЕЕ КОРРЕКТНЫЙ вариант написания  !!!

В корне проекта создать каталог: **group_vars** и все переменные по возможности вынести в другие файлы  
Имя файла должно быть равно "имея группы.yml" из файла "hosts"  
В файле ./hosts **НЕ** должно остаться строк вида: _[servers_vars]  
!!! В виде исключения можно указать для одного хоста из группы: _[servers]_ 
```
[servers]  
Linux_test2 ansible_host=192.168.56.150    password = P@ssw0rd
```


```bash
nano group_vars/staging_DB.yml  
```

```
---
ansible_user             : vagrant  
ansible_private_key_file : /home/vagrant/.ssh/authorized_keys  
db_endpoint              : 192.168.56.50:4151  
owner                    : vagrant  
Location                 : Moscow
```



#### Запустить проверку ping на всех хостах из файла hosts:  
---
## Ansible Ad-Hoc Команды

```bash
ansible all -m ping # Проверка доступности  

ansible all -m setup # Информация о хосте  

ansible all -m shell -a "cat /etc/*release" # Выполнить команду через shell  

ansible all -m command -a "pwd" # Выполнить команду  

ansible servers -m copy -a "src=nginx.conf dest= /etc/nginx" # Копировать файл на хосты для группы servers

ansible all -m copy -a "src=nginx.conf dest= /etc/nginx mode 755" -b # Копировать файл на все хосты с заменой разрешений  от sudo  

ansible all -m file -a "path=/etc/nginx/nginx.conf state=absent" -b # Удалить файл со всех хостов  

ansible all -m get_url -a "url=https://github.com/klochkovyaroslav/Manuals.git dest=/tmp" -b # Скачать с URL  

ansible all -m yum -a "name-stress state=installed" -b # Установить пакет stress на все хосты  

ansible all -m apt -a "name-stress state=installed" -b # Установить пакет stress на все хосты  

ansible all -m yum -a "name-stress state=removed" -b # Удалить пакет stress со всех хостов  

ansible all -m uri -a "url=https://mail.ru" # Проверка доступности сайта по URL  

ansible all -m yum -a "name=httpd state=latest" -b  Установить пакет httpd на все хосты  

ansible all -m service -a "name=httpd state=started enabled=yes" -b  #Запускать сервис автоматически при старте системы  

ansible all -m shell -a "ls /var" -v # Выполнить команду через shell с verbose (v, vv, vvv, vvvv)  
```



---

## Для работы клиентских Windows серверов  

На Ansible сервере Нужно установить библиотеку **winrm**  

```bash
sudo pip install "pywinrm>=0.2.2"
```
```bash
sudo apt install python3-winrm 
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

## PlayBooks

### Пример 1  

Базовый playbook, работа с переменными секция (vars), использование модуля "**handlers**", вызов handler это модуль: "**notify**".  
Можно создать отдельный playbook файл для секции handlers и далее вызвать в нужном playbook.  

```
---
- name: Install web server
  hosts: all
  become: yes
  
  vars:
    sourse_file: /tmp/nginx.conf
    dest_file: /etc/nginx/
  
  tasks:
  - name: Install Nginx
    yum:
      name=nginx
      state=latest
     
  - name: Copy nginx.conf file    
    copy: src={{  sourse_file }} dest={{ dest_file }} mode=0555
    notify: Restart nginx     #Вызвать handlers "Restarted nginx" для того что бы рестартануть уже запущенный сервис nginx
     
  - name: Start and Enable service
    service: 
      name=nginx
      state=started enabled=yes
     
  handlers:
  - name: Restart nginx
    service:
      name=nginx
      state=restarted
```

#### Пример 2  
Работа с переменными, вывод на экран модуль **debug**, соединенние переменных: модули **set_facts**, записать stdout bash команды в переменную: модуль **register**

```
---
- name: Variables example
  hosts: all
  become: yes
  
  vars:
    var1: Test1
    var2: Test2
    
  tasks:
  - name Print var1 variable
    debug:
      var: var1
      
  - debug:
    msg: "Vivesti na ekran: {{ var2 }}"
    
  - debug:
  - set_fact: Soedinyaem_peremennie="{{ var1 }} {{ var2 }}"   #соединенние переменных
  - debug:
      var: Soedinyaem_peremennie
     
- shell: uptime
  register: rezult
- debug: 
  var: rezult.cmd
```


### Пример 3  
Работа с Блоками: **block** и Условиями: **when**

```
---
- name: Bloki and Usloviya 
  hosts: all
  become:yes
  
  vars:
    sourse_file: /tmp/nginx.conf
    dest_file: /etc/nginx/
  
  tasks: 
#############################################################################################  

  - block: # -----Block for RPM------
  
      - name: Install epel-release
        yum:
        name: epel-release
        state: latest  
    
      - name: Install Nginx for Centos
        yum:
        name: nginx
        state: latest
      
      - name: Copy nginx.conf file    
        copy: src={{  sourse_file }} dest={{ dest_file }} mode=0555
        notify: Restart nginx Centos
        
      - name: Start and Enable service
        service: 
          name: nginx
          state: started 
          enabled: yes
        
    when: 
      ansible_os_family == "RedHat"
      
###########################################################################################          
  
  - block: # -----Block for DEB------
      - name: Install Nginx for Centos
        apt:
        update_cache: yes
        name: nginx
        state: latest
      
      - name: Copy nginx.conf file    
        copy: src={{  sourse_file }} dest={{ dest_file }} mode=0555
        
        notify: Restart nginx Debian
        
      - name: Start and Enable service
        service: 
          name: nginx
          state: started
          enabled: yes
        
    when: 
      ansible_os_family == "Debian"
      
###########################################################################################

handlers:
  - name: Restart nginx Centos
    service:
      name: nginx
      state: restarted
      
  - name: Restart nginx Debian
    service:
      name: nginx
      state: restarted       
      
```



### Пример 4  
Работа с Циклами: **Loop, With_Items, Until, With_Fileglob**  

#### 4_1 (Loop, With_Items)

Loop и With_Items - одна и так же команда для разных версий Ansible  
**With_Items** - работает до версии Ansible 2.4.    
**Loop** - работает от версии Ansible 2.5 и выше.  

```
---
- name: Loop Playboojk 
  hosts: all
  become:yes
  
  tasks: 
  - name: Managers of my Project
    debug: msg="Sales manager {{ items }}"
    #with_items:
    loop:
      - "Ivan"
      - "Sergey"
      - "Maxim"
      - "Olga"
      
      
      
   - name: Install some softs
     yum:
     name: {{ items }}
     state: installed
     with_items:
     #loop:
       - mc
       - nano
       - curl
       - wget     
```

#### 4_2 Until

```
---
- name: Loop Playboojk 
  hosts: all
  become:yes
  
  tasks: 
  - name: Until example
    shell: echo -n Z >> 1.txt && cat 1.txt  # -n не переводить на новую строку.
    register: otvet
    dalay: 2       # Делать с задержкой 2 сек
    retries: 10    # Повторять 10 раз
    until: otvet.stdout.find("ZZZZ")== false   # Писать символ: Z в файл до того пока в stdout не будет найдено ZZZZ
    
  - name: Print output
    debug: 
      var: otver.stdout
```

#### 4_3 With_Fileglob

```
---
- name: With_Fileglob 
  hosts: all
  become:yes
  
  vars:
    sourse_folder: /tmp/test
    dest_folder: /etc/nginx/
  
  tasks: 
#############################################################################################  

  - block: # -----Block for RPM------
  
      - name: Install epel-release
        yum:
        name: epel-release
        state: latest  
    
      - name: Install Nginx for Centos
        yum:
        name: nginx
        state: latest
              
      - name: Start and Enable service
        service: 
          name: nginx
          state: started 
          enabled: yes
        
    when: 
      ansible_os_family == "RedHat"
      
###########################################################################################          
  
  - block: # -----Block for DEB------
      - name: Install Nginx for Centos
        apt:
        update_cache: yes
        name: nginx
        state: latest
              
      - name: Start and Enable service
        service: 
          name: nginx
          state: started
          enabled: yes
        
    when: 
      ansible_os_family == "Debian"  
      
###########################################################################################      
      
      
    - name: Copy folder: tmp/test/  
    # Два способа скопировать все файлы из папки: /tmp/test/
    # Первый способ через: loop
      copy: src={{ sourse_folder }}/{{ item }} dest={{ dest_folder}} mode 0555
      loop:
        - "Index.html"
        - "1.file"
        - "2.file"
        - "3.file"
        - "4.file"
      notify:
        - Restart nginx Centos
        - Restart nginx Debian
        
        
    # Второй способ через: with_fileglob  
      copy: src={{ item }} dest={{ dest_folder}} mode=0555
      with_fileglob: 
        "{{ sourse_folder }}/*.*"
      notify:
        - Restart nginx Centos
        - Restart nginx Debian            


handlers:
  - name: Restart nginx Centos
    service:
      name: nginx
      state: restarted
    when: 
        ansible_os_family == "RedHat"
      
  - name: Restart nginx Debian
    service:
      name: nginx
      state: restarted
    when: 
        ansible_os_family == "Debian"      

```


### Пример 5

#### Работа с шаблонами  
Что бы создать шаблон **.j2** копируем уже существующий **nginx.conf** в **nginx.j2**  

В файле **nginx.j2** подставляем требуемые переменые в формате:  
_My Server IP : {{ ansible_default_ipv4.address }}_  
_My Server Hostname : {{ ansible_hostname }}_  


```
---

- name: With_Fileglob 
  hosts: all
  become:yes
  
  vars:
    sourse_folder: /tmp/test
    dest_folder: /etc/nginx/
  
  tasks: 
#############################################################################################  

  - block: # -----Block for RPM------
  
      - name: Install epel-release
        yum:
        name: epel-release
        state: latest  
    
      - name: Install Nginx for Centos
        yum:
        name: nginx
        state: latest
              
      - name: Start and Enable service
        service: 
          name: nginx
          state: started 
          enabled: yes
        
    when: 
      ansible_os_family == "RedHat"
      
###########################################################################################          
  
  - block: # -----Block for DEB------
      - name: Install Nginx for Centos
        apt:
        update_cache: yes
        name: nginx
        state: latest
              
      - name: Start and Enable service
        service: 
          name: nginx
          state: started
          enabled: yes
        
    when: 
      ansible_os_family == "Debian"  
      
###########################################################################################      
      
 
    - name: Copy folder: tmp/test/  
      copy: src={{ item }} dest={{ dest_folder}} mode=0555
      with_fileglob: 
        "{{ sourse_folder }}/*.*"
      notify:
        - Restart nginx Centos
        - Restart nginx Debian 
        
    - name: Genetate NGINX.CONF file
      template: sec={{ source_folder }}/nginx.j2 dest={{ dest_folder}}/nginx.conf mode=0555
      notify:
        - Restart nginx Centos
        - Restart nginx Debian            


handlers:
  - name: Restart nginx Centos
    service:
      name: nginx
      state: restarted
    when: 
        ansible_os_family == "RedHat"
      
  - name: Restart nginx Debian
    service:
      name: nginx
      state: restarted
    when: 
        ansible_os_family == "Debian"
```

### Пример 6

### Создание Ролей - Roles
В корне проекта создать каталог: **roles**  

Перейти в каталог: **roles** и выполнить команду:  
Создастся директория с именем: "install_nginx_web_server"содержащая подкаталоги с файлами .yml  
Подкаталог test можно удалить.  

```bash
ansible-galaxy init install_nginx_web_server
```
Что бы потом запустить плейбук с ролями нужно в файле playbook.yml добавить роли 
```
- name: Install nginx web server
  hosts: all
  become: yes
  
  roles:
    - install_nginx_web_server
    или так
    - {role: install_nginx_web_server, when: ansible_system == 'Linux' }
 ```
Запускаем плейбук    
    
```bash
ansible-playbook playbook.yml
```

### Пример 7

### Внешние переменные - extra-vars
Переменные extra-vars имеют наивысший приоритет  

В файле плебука нужно указать переменную в скобках "{{}}"  
Например:  

```
- name: Install nginx web server
  hosts: "{{ MYHOSTS }}"
  become: yes
  
  roles:
    - {role: install_nginx_web_server, when: ansible_system == 'Linux' }
 ```

Запускаем плейбук    
    
```bash
ansible-playbook playbook.yml --extra-vars "MYHOSTS=prod_DB"
```
Переменных можно указывать несколько, через "пробел", например:  

```bash
ansible-playbook playbook.yml --extra-vars "MYHOSTS=prod_DB owner=Petya"
```

### Пример 8

### Использование Import и Include.

Отличие **Import** от **Include** в том что при запуске плейбука ansible читает файл главного плейбука и:  
При **Import** сразу же меняет содержимое того что написано в дерективе Import на содержмое файла импорта.  
При **Include** меняет содержимое того что написано в дерективе Include на содержмое файла с Include только тогда когда доходит до выполнения директивы Include.  

#### Пример файла основного playbook

```
- name: Example Import Include
  hosts: all
  become: yes
  
  vars:
    mytext: "Example for Import & Include"
    tasks:
    - name: Ping test
      ping:
      
    - name: Create Folders # Эту строку можно не писать вообще.
      import: create_folders.yml
    - name: Create Files # Эту строку можно не писать вообще.
      include: create_files.yml  mytext: "Example only Include"  # Можно переопределить переменную "mytext" на 'лету'.   
 ```

#### Пример файлов create_files.yml и create_folders.yml
Все вложенные каталоги создадутся автоматически при их отсутствии.  

#### Создать файл "create_folders.yml" с содержимым:    

```
---
- name: Create folder1
  file:
    path: /tmp/test/test_folder1 
    state: directory
    mode: 0755
  
- name: Create folder2
  file:
    path: /tmp/test/test_folder2
    state: directory
    mode: 0755
```


#### Создать файл "create_files.yml" с содержимым:  

```
---
- name: Create file1
  copy:
    dest: /tmp/test/test_folder1/file_test1.txt 
    content: |
      Some test text1, in file_test1.txt
      Some test text2, in file_test1.txt
      Some test text3, {{ mytext }}
  
- name: Create file2
  copy:
    dest: /tmp/test/test_folder1/file_test2.txt 
    content: |
      Some other test text1, in file_test2.txt
      Some other test text2, in file_test2.txt
      Some other test text3, {{ mytext }}
```

## Ansible Vault

```bash
ansible-vault create mysecret.txt
```
```bash
ansible-vault view mysecret.txt
```
```bash
ansible-vault edit mysecret.txt
```
```bash
ansible-vault rekey mysecret.txt
```
```bash
ansible-playbook playbook11_vault.yml --ask-vault-pass
```
```bash
ansible-playbook playbook11_vault.yml --vault-password-file mypass.txt # Пароль в файле "mypass.txt"
```
```bash
ansible-vault encrypt_string # Можно зашифровать текст а не весь файл
```
или так  
```bash
echo -n "DDrrGGvvHH" | ansible-vault encrypt_string  # Внести эту зашифрованную строку вместо переменной в виде
```

```bash
---
- name: Ansible Vault
  hosts: rpm
  become: yes

  vars: 
    admin_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          30636639613264373633643262353631376133626137623365306335333936663535313338363234
          3438323461373333376265313133346538633863333836330a653437356530623063666464616236
          63303736653764653739653265613132346136333463386263333762356430616462633138626366
          6236393265373836300a316537373637653462306337323962663436383334616236333333646566
          3536

  tasks:
  - name: Create confi file
    copy:
      dest: "/home/myconfig.conf"
      content: |
          port = 8080
          log = 1month
          home = /opt/nginx/config
          user = admin
          admin_password = {{ admin_password }}
```



## Если при запуске playbook ошибка: Ansible – “sudo: a password is required”

1 Способ использовать опцию: --ask-become-pass  

```bash
ansible-playbook playbook.yml --ask-become-pass  
```

2 Способ использовать опцию: ansible_become_password   

```bash
ansible-playbook playbook.yml -e "ansible_become_password=p@$$w0rd"
```

3 Способ использовать: Ansible Valt  

Create a new file named password.yml with the encrypted sudo password:  
```bash
ansible-vault edit password.yml
```
After providing a password for the Vault, the tool will open the password.yml file in a text editor where you can put your ansible_become_password:  
```bash
ansible_become_password: p@$$w0rd
```
Save and exit. Next create a vault.txt file with the password that you used while creating the password.yml file, e.g:
```bash
echo "vaultPassw0rd" > vault.txt
```
Ensure permissions on vault.txt are such that no one else can access it and do not add this file to a source control:
```bash
chmod 600 vault.txt
echo "vault.txt" >> .gitignore
```
Finally run your playbook as follows:
```bash
ansible-playbook playbook.yml -e '@password.yml'--vault-password-file=vault.txt
```

