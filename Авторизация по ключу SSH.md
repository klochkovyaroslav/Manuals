# Как работают ключи SSH  

Каждая пара ключей состоит из открытого и закрытого ключа. **Секретный ключ** сохраняется на стороне **клиента** и не должен быть доступен кому-либо еще. 
Утечка ключа позволит злоумышленнику войти на сервер, если не была настроена дополнительная аутентификация по паролю.  

Открытый ключ используется для шифрования сообщений, которые можно расшифровать только закрытым ключом. Это свойство и используется для аутентификации с помощью пары ключей. 
Открытый ключ загружается на удаленный сервер, к которому необходимо получить доступ. 
Его нужно добавить в специальный файл **~/.ssh/authorized_keys**  

#### Генерация ключей ssh выполняется командой:  
Запустить команду нужно на локальном ПК/Сервере с которого будет осуществляться вход на удаленный сервер.  

``` bash 
ssh-keygen 
```
 
 Секретный ключ будет называться **id_rsa**, а публичный **id_rsa.pub**.  
 Можно создать ключи с произвольным именем, для этого нужно ввести путь и имя ключа  
 В строку "Enter file in which to save the file(/home/user/.ssh/id_rsa):" /home/user/.ssh/my_own_key  
 
 #### Загрузка ключа на удаленный сервер
 Скопировать ключ с локального на удаленный сервер - это использовать утилиту ssh-copy-id  в файл **~/.ssh/authorized_keys**
 
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
#### Очистить от старых записей на локальном ПК

``` bash
ssh-keygen -f "~/.ssh/known_hosts" -R "192.168.56.150"
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

```

#### Отключение проверки пароля

``` bash
sudo vi /etc/ssh/sshd_config
```

_**PasswordAuthentication**_ **yes**  заменить на **no**  

или   

``` bash
sed -i '65s/PasswordAuthentication yes/PasswordAuthentication no/g' /etc/ssh/sshd_config  
```

Теперь сохраните файл и перезапустите службу ssh:  

``` bash
sudo service ssh restart  
 ```
 ``` bash
sudo systemctl restart sshd
 ```



 
 
