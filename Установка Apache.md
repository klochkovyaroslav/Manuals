# Установка Apache

```bash
sudo yum search httpd
```
```bash
sudo yum install httpd -y
```
```bash
sudo systemctl status httpd.service
```
```bash
sudo systemctl enable --now httpd.service
```
```bash
sudo systemctl status httpd.service
```
#### Добавляем в конфиг для использования нестандартных портов
```bash
sudo vi /etc/httpd/conf/httpd.conf
```
> Lister 8028  
> Lister 4043

```bash
sudo systemctl restart httpd.service; sudo systemctl status httpd.service
```
##### Получаем ошибку:
> Job for httpd.service failed because the control process exited with error code.  
> See "systemctl status httpd.service" and "journalctl -xeu httpd.service" for details.


### Траблшутинг Selinux

#### Смотрим log 
```bash
sudo tail -f /var/log/audit/audit.log
```
или
#### Установить Утилиты основных средств SELinux - Пакет: policycoreutils-python-utils
```bash
sudo yum install policycoreutils-python-utils
```
#### Запускаем утилиту: audit2why
```bash
audit2why < /var/log/audit/audit.log
```
или еще утилита для траблшутинга Selinux
#### Инструменты SELinux для разработки модулей политики:
```bash
sudo yum install setroubleshoot-server
```
Перезагрузите или перезапустите auditd после установки.

#### Запускаем утилиту: sealert
```bash
sudo sealert -a /var/log/audit/audit.log
```
Любым из перечисленных выше способов выясняем, что в контексте безопасности: **http_port_t** НЕ разрешены нестандарные порты

#### Открытие сетевых портов для разрешения использовать http-запросы в SELinux:
```bash
sudo semanage port -a -t http_port_t -p tcp 8028; sudo semanage port -a -t http_port_t -p tcp 4043
```
#### Посмотреть список портов для контекста безопасности: http_port_t
```bash
sudo semanage port -l | grep http_port_t
```

```bash
sudo ls -Z /webserver/index.html
```
> unconfined_u:object_r:default_t:s0 /webserver/index.html
```bash
sudo ls -Z /var/www/my_test_site/html/index.html
```
> unconfined_u:object_r:httpd_sys_content_t:s0 /var/www/my_test_site/html/index.htm

#### Меняем тип в контексте каталога
```bash
sudo chcon -v -R -t httpd_sys_content_t /webserver/index.html
```

#### Проверяем контекст каталога
```bash
sudo ls -Z /webserver/index.html
```
> unconfined_u:object_r:httpd_sys_content_t:s0 /webserver/index.html


#### Восстанавливаем контекст каталога
```bash
sudo restorecon -vR /webserver/index.html
```
> unconfined_u:object_r:default_t:s0 /webserver/index.html
```bash	
sudo semanage fcontext -a -t httpd_sys_content_t "/webserver(/.*)?"
```
```bash
sudo semanage port -l | grep http_port_t
```
```bash
sudo yum install setools-console
```





#### Настроить firewall, добавляем правила для открытия портов
```bash
sudo firewall-cmd --list-all
```
```bash
sudo firewall-cmd --add-service=http --permanent
```
```bash
sudo firewall-cmd --permanent --add-port=8028/tcp
```
```bash
sudo firewall-cmd --permanent --add-port=4043/tcp
```

### Создать файл конфигурации виртуального хоста для сайта.
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

#### создание нового модуля для разрешения nginx на нестандартном порту
> В лабе не делать это для инфы
```bash
sudo ausearch -c 'nginx' --raw | audit2allow -M my_module_for_nginx
Установка нового модуля selinux: my_module_for_nginx в ядро
sudo semodule -i my_module_for_nginx.pp
Посмотреть установился ли модуль
sudo semodule -l | grep my_module_for_nginx
Посмотреть все установленные модули
sudo semodule -l | grep less
```






sudo yum install mod_ssl -y
