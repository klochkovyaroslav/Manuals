# Подключение Linux к домену AD с помощью SSSD и realmd

## Предварительные настроки

#### FQDN в качестве имени хоста(обязательно для DEB систем)
```bash
sudo hostnamectl set-hostname ts-admin-linux.mak.lo
```

#### Для корректного обновления PTR-записи нужно в файле /etc/hosts изменить:
> 127.0.1.1 ts-admin-linux.mak.lo ts-admin-linux
на адрес одного из сетевых интерфейсов
> 192.250.2.224 ts-admin-linux.mak.lo ts-admin-linux  


#### Установить/настроить сонхронизацию NTP клиента с серверами контроллеров домена 
```bash
sudo apt install chrony -y
```
##### Отредактировать файл с настройками
```bash
nano /etc/chrony/chrony.conf
```
```
server 192.250.2.70 iburst
server 192.250.2.80 iburst
```
![image](https://github.com/user-attachments/assets/828f238f-708b-4c25-8755-f08062678246)

```bash
systemctl restart chronyd
```
```bash
timedatectl status && chronyc sources
```
