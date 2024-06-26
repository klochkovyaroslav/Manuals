# Create Systemd-Units

## Create Mount
### Собственные модули типа mount следует располагать в каталоге /etc/systemd/system
#### Если в пути к точке монтирования есть служебные символы, то для генерации имени модуля существует специальная команда: 

```bash
systemd-escape -p "/home/user/*disk-sata" 
```
#### Для создания нового модуля:

```bash
nano /etc/systemd/system/home-user-disk1.mount
или
systemctl --force --full edit opt-data-log.mount
```
содержимое:  
```bash
[Unit]
Description=mount /opt/data/log

[Mount]
What=UUID="20d38d22-d469-42ac-a649-c2f76646f61b"
Where=/opt/data/log
Type=ext2

Options=defaults

[Install]
WantedBy=multi-user.target
```


```bash
systemctl cat opt-data-log.mount
```

## Create Service

```bash
systemctl --force --full edit nginx.service
или
touch /etc/systemd/system/nginx.service
nano /etc/systemd/system/nginx.service
```
содержимое:

```
[Unit]
Description=NGINX WEB SERVER

[Service]
Type=forking
ExecStart=/usr/local/nginx/sbin/nginx
ExecStop=/usr/local/nginx/sbin/nginx -s stop
ExecStop=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s reopen
ExecStop=/usr/local/nginx/sbin/nginx -s quit

[Install]
WantedBy=multi-user.target
```
```bash
systemctl daemon-reload
```
```
systemctl start nginx.service
systemctl status nginx.service
systemctl restart nginx.service
systemctl stop nginx.service
```

