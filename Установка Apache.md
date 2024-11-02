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

```bash
sudo firewall-cmd --list-all
```
```bash
sudo firewall-cmd --add-service=http
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
```bash
#### установить пакет policycoreutils-python-utils
```
```bash
sudo install policycoreutils-python-utils
```
```bash
sudo semanage port -a -t http_port_t -p tcp 8028
```
```bash
sudo semanage port -a -t http_port_t -p tcp 4043
```
```bash
sudo semanage port -l | grep http_port_t
```
```bash
sudo mkdir -p /webserver
```
```bash
echo "<h1>Klochkov Yaroslav, 192.168.56.10</h1>" | sudo tee /webserver/index.html
```


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



```bash
sudo systemctl restart httpd.service
```
```bash
sudo systemctl status httpd.service
```


```bash
sudo tail -f /var/log/audit/audit.log
```
или
```bash
audit2why < /var/log/audit/audit.log
```
еще утилита для траблшутинга selinux

```bash
sudo yum install setroubleshoot-server
```
```bash
sudo sealert -a /var/log/audit/audit.log
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




sudo yum install mod_ssl -y
