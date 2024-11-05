# Установка и настройка WEB сервера Apache

#### Установить Apache
```bash
sudo yum search httpd
sudo yum install httpd -y
```
#### Добавить сервис httpd в автозагрузку
```bash
sudo systemctl enable --now httpd.service; sudo systemctl status httpd.service
```

#### Добавить в конфиг для использования нестандартных портов
```bash
sudo vi /etc/httpd/conf/httpd.conf
```
> Listen 8028  
> Listen 4043

#### Перезагружаем службу httpd
```bash
sudo systemctl restart httpd.service; sudo systemctl status httpd.service
```
##### Получаем ошибку:
> Job for httpd.service failed because the control process exited with error code.  
> See "systemctl status httpd.service" and "journalctl -xeu httpd.service" for details.

----
## Траблшутинг Selinux
#### Смотрим log 
```bash
sudo tail -f /var/log/audit/audit.log
```
или
#### Установить Утилиты основных средств SELinux - Пакет: policycoreutils-python-utils
```bash
sudo yum install policycoreutils-python-utils
```
#### Запускаем утилиту: audit2why - из: policycoreutils-python-utils
```bash
audit2why < /var/log/audit/audit.log
```
или еще утилита для траблшутинга Selinux
#### Инструменты SELinux для разработки модулей политики:
```bash
sudo yum install setroubleshoot-server
```
Перезагрузите или перезапустите auditd после установки.

#### Запускаем утилиту: sealert - из: setroubleshoot-server
```bash
sudo sealert -a /var/log/audit/audit.log
```
Любым из перечисленных выше способов выясняем, что в контексте безопасности: **http_port_t** НЕ разрешены нестандарные порты

----
## Открытие сетевых портов для разрешения использовать http-запросы в SELinux:
```bash
sudo semanage port -a -t http_port_t -p tcp 8028; sudo semanage port -a -t http_port_t -p tcp 4043
```
#### Посмотреть список портов для контекста безопасности: http_port_t
```bash
sudo semanage port -l | grep http_port_t
```
----
## Настроить firewall, добавляем правила для открытия портов
```bash
sudo firewall-cmd --list-all
```
```bash
sudo firewall-cmd --add-service=http
sudo firewall-cmd --add-service=http --permanent
```
```bash
sudo firewall-cmd --add-service=https
sudo firewall-cmd --add-service=https --permanent
```
```bash
sudo firewall-cmd --add-port=8028/tcp
sudo firewall-cmd --add-port=8028/tcp  --permanent
```
```bash
sudo firewall-cmd --add-port=4043/tcp
sudo firewall-cmd --add-port=4043/tcp  --permanent
```
----
## Создать файл конфигурации виртуального хоста для сайта.
#### Есть два варианта:
- создавать в каталоге по умолчанию: _/var/www/_
- создавать в собственном каталоге
  
#### Создать новую директорию
```bash
sudo mkdir -p /webserver
```
#### Создаем  файл index.html
```bash
echo "<h1>Klochkov Yaroslav, 192.168.56.10</h1>" | sudo tee /webserver/index.html
```
#### Измените конфигурацию Apache

#### Создать конфигурационный файл виртуального хоста вашего сайта.
```bash
sudo vi /etc/httpd/conf.d/webserver.conf
```
> _<VirtualHost *:8028>_
>    _ServerName webserver_  
>    _ServerAlias webserver_  
>    _DocumentRoot /webserver_  
>
>    _<Directory /webserver>_  
>        _AllowOverride All_  
>        _Require all granted_  
>    _</Directory>_  
>
>    _ErrorLog /var/log/httpd/webserver_8028-error.log_  
>    _CustomLog /var/log/httpd/webserver_8028-access.log combined_  
> _</VirtualHost>_  
>
> _<VirtualHost *:4043>_  
>  _ServerName webserver_  
>    _ServerAlias webserver_  
>    _DocumentRoot /webserver_  
>
>    _<Directory /webserver>_  
>        _AllowOverride All_  
>        _Require all granted_  
>    _</Directory>_  
>
>    _ErrorLog /var/log/httpd/webserver_4043-error.log_  
>    _CustomLog /var/log/httpd/webserver_4043-access.log combined_  
> _</VirtualHost>_


#### Перечитываем конфигурацию apache
```bash
sudo systemctl reload httpd.service; sudo systemctl status httpd.service
```
Проверяем, наш сайт НЕ открывается на портах 8028 и 4043

## Еще раз траблшутим Selinux

#### Запускаем утилиту: audit2why
```bash
audit2why < /var/log/audit/audit.log
```
Выясняем что файл _/webserver/index.html_ в дефолтном контекстке: "default_t" в Selinux
#### Смотрим контекст безопасности SELinux, связанный с файлами и каталогами
```bash
sudo ls -Z /webserver/index.html
```
> unconfined_u:object_r:default_t:s0 /webserver/index.html

#### Смотрим контекст безопасности SELinux, связанный с файлами и каталогами по умолчанию в apache
```bash
sudo ls -Z /var/www/my_test_site/html/index.html
```
> unconfined_u:object_r:httpd_sys_content_t:s0 /var/www/my_test_site/html/index.html

Выясняем что файл _/var/www/my_test_site/html/index.html_ в контекстке: "httpd_sys_content_t" в Selinux  

Есть несколько вариантов решения проблемы
#### 1. Вариант менее предпочтительный, для быстрой проверки - Меняем тип в контексте каталога
```bash
sudo chcon -v -R -t httpd_sys_content_t /webserver/index.html
```
#### Проверяем еще раз контекст безопасности для каталога
```bash
sudo ls -Z /webserver/index.html
```
> unconfined_u:object_r:httpd_sys_content_t:s0 /webserver/index.html  
После изменений файл _/webserver/index.html_ в контекстке: "httpd_sys_content_t" в Selinux

#### Восстанавливаем контекст каталога
```bash
sudo restorecon -vR /webserver/index.html
```
> unconfined_u:object_r:default_t:s0 /webserver/index.html  
файл _/webserver/index.html_ снова в дефолтном контекстке: "default_t" в Selinux

#### 2. Вариант более предпочтительный - Назначаем контекст безопасности "httpd_sys_content_t" для всех файлов и каталогов в каталоге "/webserver"
```bash	
sudo semanage fcontext -a -t httpd_sys_content_t "/webserver(/.*)?"
```
```bash
sudo semanage port -l | grep http_port_t
```
#### Восстанавливаем контекст каталога по умолчанию
```bash
sudo restorecon -vR /webserver/index.html
```
> unconfined_u:object_r:httpd_sys_content_t:s0 /webserver/index.html


#### 3. Вариант - Создать свой модуль для разрешения работы Apache на нестандартном порту
##### Ставим утилиту для работы с политиками SELinux
```bash
sudo yum install setools-console
```
#### создание нового модуля для разрешения nginx на нестандартном порту
> В лабе не делать это для информации
```bash
sudo ausearch -c 'nginx' --raw | audit2allow -M my_module_for_nginx
Установка нового модуля selinux: my_module_for_nginx в ядро
sudo semodule -i my_module_for_nginx.pp
Посмотреть установился ли модуль
sudo semodule -l | grep my_module_for_nginx
Посмотреть все установленные модули
sudo semodule -l | grep less
```

## Настроить работы Apache c SSL сертификатами
#### Установить модуль для работы c SSL сертификатами
```bash
sudo yum install mod_ssl -y
```

#### Генерируем закрытый ключ(private key)
```bash
sudo openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out my_site.key
```

#### На основе закрытого ключа генерируем запрос на выдачу сертификата
```bash
sudo openssl req -new -key my_site.key -out my_site.csr
```
#### Выдать самоподписанный (ключом my_site.key)сертификат
```bash
sudo openssl x509 -req -days 365 -in my_site.csr -signkey my_site.key -out my_site.crt
```
#### Скопировать сертификат и закрытый ключ в соответствующие директории
```bash
sudo cp my_site.crt /etc/pki/tls/certs
sudo cp my_site.key /etc/pki/tls/private/
```
#### Изменить конфиг файл, для порта: 4043
```bash
sudo vi /etc/httpd/conf.d/webserver.conf
```

> _<VirtualHost *:8028>_  
>    _ServerName webserver_  
>    _ServerAlias webserver_  
>    _DocumentRoot /webserver_  
>
>    _<Directory /webserver>_  
>        _AllowOverride All_  
>        _Require all granted_  
>    _</Directory>_  
>
>    _ErrorLog /var/log/httpd/webserver_8028-error.log_  
>    _CustomLog /var/log/httpd/webserver_8028-access.log combined_  
> _</VirtualHost>_  
>
> _<VirtualHost *:4043>_  
>  _ServerName webserver_  
>    _ServerAlias webserver_  
>    _DocumentRoot /webserver_  
>    _SSLEngine on_
>        _SSLCertificateFile /etc/pki/tls/certs/my_site.crt_  
>        _SSLCertificateKeyFile /etc/pki/tls/private/my_site.key_  
>
>    _<Directory /webserver>_  
>        _AllowOverride All_  
>        _Require all granted_  
>    _</Directory>_  
>
>    _ErrorLog /var/log/httpd/webserver_4043-error.log_  
>    _CustomLog /var/log/httpd/webserver_4043-access.log combined_  
> _</VirtualHost>_


#### Проверка конфигурации
```bash
sudo apachectl configtest
```
#### Перечитать конфигурацию apache
```bash
sudo systemctl reload httpd.service; sudo systemctl status httpd.service
```
