# Как работают ключи SSH  
## Авторизация по ключу SSH

Каждая пара ключей состоит из открытого и закрытого ключа. **Секретный ключ** сохраняется на стороне **клиента** и не должен быть доступен кому-либо еще. 
Утечка ключа позволит злоумышленнику войти на сервер, если не была настроена дополнительная аутентификация по паролю.  

Открытый ключ используется для шифрования сообщений, которые можно расшифровать только закрытым ключом. Это свойство и используется для аутентификации с помощью пары ключей. 
Открытый ключ загружается на удаленный сервер, к которому необходимо получить доступ. 
Его нужно добавить в специальный файл **~/.ssh/authorized_keys**  

### Генерация ключей ssh выполняется командой:  
Запустить команду нужно на локальном ПК/Сервере с которого будет осуществляться вход на удаленный сервер.  

``` bash 
ssh-keygen 
```

#### RSA-ключ с длиной 4096 бит указываем именя ключа, путь к месту хранения ключа, пароль(passphrase) в явном виде (-N «P@sswd»):
``` bash 
ssh-keygen -b 4096 -t rsa -C "My_ssh_key-name" -N "P@sswd" -f /users/admin/my_ssh_key
```

#### RSA-ключ с длиной 4096 бит указываем именя ключа, путь к месту хранения ключа, без указания пароля(passphrase) (-N ""):
``` bash 
ssh-keygen -b 4096 -t rsa -C "My_ssh_key-name" -N "" -f /users/admin/my_ssh_key
```
 
 Секретный ключ будет называться **id_rsa**, а публичный **id_rsa.pub**.  
 Можно создать ключи с произвольным именем, для этого нужно ввести путь и имя ключа  
 В строку "Enter file in which to save the file(/home/user/.ssh/id_rsa):" /home/user/.ssh/my_own_key  
 
 #### Загрузка ключа на удаленный сервер
 Скопировать ключ с локального на удаленный сервер - это использовать утилиту ssh-copy-id  в файл **~/.ssh/authorized_keys**  
 В результате содержимое файла с публичным ключом id_rsa.pub будет скопировано в файл **~/.ssh/authorized_keys**  
 
``` bash  
ssh-copy-id username@remote_host
```
или вручную командой: 

``` bash 
cat ~/.ssh/id_rsa.pub | ssh username@remote_host "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"  
```

или
``` bash 
cat ~/.ssh/id_rsa.pub | ssh root@ip-адрес-сервера 'cat >> ~/.ssh/authorized_keys'
sudo cat /home/zabbix/for_brocade_ssh_key.pub | ssh user@192.168.2.24 'cat >> ~/.ssh/authorized_keys' 
```
или
```bash
cat >> .ssh/authorized_keys
```
Нажать Enter  
> Вставить ssh public key в виде текста

Нажать:  
```bash
CTRL+d
```


Если возникла ошибка:  

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:nIr5Cr+I+hWAggSRBd3CFHQZMweQN4XM+ul4rLLkog0.
Please contact your system administrator.  
```

#### Копирование ключа на сервер Windows
``` bash 
type $env:USERPROFILE\.ssh\id_rsa.pub | ssh user@server "cat >> .ssh/authorized_keys"
```

#### Очистить от старых записей на локальном ПК

``` bash
ssh-keygen -R 192.168.56.150
```
#### Выполнить на удаленном сервере

``` bash
chmod 0644 ~/.ssh/authorized_keys
```

#### Можно подключаться к удаленному серверу

``` bash
ssh -i my_own_key username@remote_host
ssh username@remote_host
Выполнить команду удаленно
ssh -i my_own_key username@remote_host "ip a"
ssh -i ~/.ssh/temp_ssh/for_brocade_ssh_key user@192.168.2.24
ssh user@192.168.2.24 -i /home/user/.ssh/temp_ssh/for_brocade_ssh_key
```

#### Можно подключаться к удаленному серверу с ключем без запроса парольной фразы
Нужно использовать ssh агента  

``` bash
eval `ssh-agent -s`
```
#### Добавить приватный ключ в агента

```bash
ssh-add my_own_key
```
Ввести парольную фразу к ключу  

#### Проверить как добавился приватный ключ в агента

```bash
ssh-add -l
```

#### Очистить кэш агента

```bash
ssh-add -D
```

#### Отключение проверки пароля

``` bash
sudo vi /etc/ssh/sshd_config
```

_**PasswordAuthentication**_ **yes**  заменить на **no**  

или   

``` bash
sudo sed -i '65s/#PasswordAuthentication yes/PasswordAuthentication no/g' /etc/ssh/sshd_config
```

Теперь сохраните файл и перезапустите службу ssh:  

``` bash
sudo service ssh restart  
 ```
 ``` bash
sudo systemctl restart sshd
 ```

#### SSH config (port, time)
```bash
sed -i 's/#Port 22/Port 6982/' /etc/ssh/sshd_config
sed -i 's/#LoginGraceTime 2m/LoginGraceTime 10s/' /etc/ssh/sshd_config
```

# Автоматическое подключение по SSH с паролем: 

``` bash
sudo apt install sshpass
export SSHPASS='5BdbWO0oufYXrHCazp4Awn'
sshpass -e ssh sshro@192.168.1.77
sshpass -e ssh sshro@192.168.1.77 'show ip ospf neigh' | grep FULL | awk ' {print $7}' | wc -l
RESULT=$(sshpass -e ssh sshro@192.168.1.77 'show ip ospf neigh' | grep FULL | awk ' {print $7}' | wc -l)
echo $RESULT
sshpass -f ~/.ssh/.sshf ssh sshro@192.168.1.77 'show ip ospf neigh'
 ```

https://ru.linux-console.net/?p=1697  
https://linux-notes.org/zapustit-komandy-cherez-ssh-v-unix-linux/  
 
----

# SCP - Копировать файлы по SSH

#### Копировать файлы с удалённого сервера на локальный компьютер

```bash
scp user@192.168.1.28:/var/lib/vz/template/iso/installation-1.7.5.16-06.02.24_14.21.iso /home/admin/Documents
```
> Файл installation-1.7.5.16-06.02.24_14.21.iso будет загружен с сервера 192.168.1.28 на локальный компьютер в папку "/home/admin/Documents"  

#### Копировать файл по SSH с локальной машины на удалённый сервер

```bash
sudo scp file1.tar.gz root@server2:/root/
```
> Файл file1.tar.gz будет скопирован на хост server2 в директорию "/root"  

#### Копировать файл по SSH с одного удалённого сервера на другой
##### Подключитесь по SSH к серверу, на котором расположен файл. Затем выполните команду:

```bash
scp /home/file1.tar.gz root@192.168.1.28:/home/admin/Documents
```
> Файл file1.tar.gz будет скопирован на хост 192.168.1.28 в директорию "/home/admin/Documents"  



----

# Windows

#### Создать пару ключей

id_rsa.pub перенести на Linux server/Router  

```powershell
ssh-keygen -t rsa -b 2048
```
```powershell
ssh-keygen -t ed25519
```
```powershell
ssh-keygen -t ecdsa -b 521
```

Ключи генеряться по умолчанию:  
C:\Users\%username%\.ssh  


#### OpenSSH Authentication Agent
```powershell
Get-Service ssh-agent
```

```powershell
Set-Service -Name "ssh-agent" -StartupType Automatic
```

<img width="530" height="104" alt="image" src="https://github.com/user-attachments/assets/7c74839b-8ef5-4b6b-936c-53fdb6d04846" />

```powershell
Start-Service -Name "ssh-agent"
```

<img width="408" height="85" lt="image" src="https://github.com/user-attachments/assets/d9015500-0044-4285-ae90-ac10d5bdf132" />


#### Добавить приватный ключ
```powershell
ssh-add 'C:\Users\yklochkov\Documents\ssh_keys\snr_sw-01-dis'
```
#### Посмотреть добавленный приватный ключ в агенте
```powershell
ssh-add -l
```

#### Удалить приватный ключ из агента
```powershell
ssh-add -D
```


#### Можно подключаться
```powershell
ssh yklochkov@192.168.20.5 -o PreferredAuthentications=publickey -o PubkeyAcceptedKeyTypes=ssh-rsa -o MACs=hmac-sha1 -i C:\Users\yklochkov\Documents\ssh_keys\snr_sw-01-dis
```
```powershell
ssh yklochkov@192.168.20.5 -o PreferredAuthentications=publickey -o PubkeyAcceptedKeyTypes=ssh-rsa -o MACs=hmac-sha1 -i C:\Users\yklochkov\.ssh\id_rsa -o FingerprintHash=sha256
```

#### Можно подключаться без указания приватного ключа

```powershell
ssh yklochkov@192.168.20.5 -o PreferredAuthentications=publickey -o PubkeyAcceptedKeyTypes=ssh-rsa -o MACs=hmac-sha1
```

