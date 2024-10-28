# Установка и настройка NTP сервера Chrony

```bash
sudo yum install -y chrony
```
> sudo vi /etc/chrony.conf

```bash
sudo systemctl enable --now chronyd.service
```
```bash
sudo systemctl is-active chronyd.service
```
```bash
sudo timedatectl set-timezone Europe/Moscow
```
```bash
sudo firewall-cmd --add-service=ntp --permanent
```
```bash
sudo firewall-cmd --reload
```
