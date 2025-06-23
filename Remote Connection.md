# AnyDesk

#### - add repository key to Trusted software providers list  

```bash
wget -qO - https://keys.anydesk.com/repos/DEB-GPG-KEY | apt-key add -  
```

#### - add the repository:  

```bash
echo "deb http://deb.anydesk.com/ all main" > /etc/apt/sources.list.d/anydesk-stable.list  
```

#### - update apt cache:  

```bash
apt update  
```

#### - install anydesk:  

```bash
apt install anydesk  
```

### Fix AnyDesk of Display_Server_Not_Supported Error for

```bash
sudo nano /etc/gdm3/daemon.conf  
```

Раскоментировать:

_WaylandEnable=false_  
_AutomaticLoginEnable = true_  
_AutomaticLogin = $USERNAME_  

```bash
/etc/init.d/gdm3 restart  
```


----

# Установка xrdp-сервера

#### Перейти в другой аккаунт (root)
```bash
su -
```
#### Обновить систему
```bash
apt update && sudo apt upgrade -y
```

#### Установка the Desktop Environment
```bash
apt install xfce4 xfce4-goodies xorg xorgxrdp dbus-x11 x11-xserver-utils pulseaudio-module-xrdp -y
```

#### Установка the XRDP 
```bash
apt install xrdp -y
```

#### Запуск xrdp-сервера и добавление автозагрузку
```bash
systemctl enable xrdp.service --now
```
```bash
systemctl status xrdp.service
```

#### Cоздать отдельного пользователя для xRDP и выдать доступ к генерации TLS-сертификатов
```bash
adduser xrdp ssl-cert
или
adduser xrdp
usermod -aG ssl-cert xrdp
usermod -a -G sudo xrdp
```

#### Перезапустить сервис xrdp
```bash
systemctl restart xrdp.service
```

### Настроить подключение к xrdp-серверу при активной локальной сессии пользователя
#### Для устранения данной ошибки в скрипте /usr/libexec/xrdp/startwm.sh в функции wm_start добавьте строку:
```bash
export $(dbus-launch)
```

![image](https://github.com/user-attachments/assets/9608bddf-281c-4038-b27b-c1a988c96c5b)

## Подключится по RDP из Windows
![image](https://github.com/user-attachments/assets/0ede98aa-eec1-4f03-82ac-ecbed7a8375b)

> Ввести логин + пароль от локальной  

![image](https://github.com/user-attachments/assets/a6484f27-2bda-44bc-9fd6-6c36d715d5f3)


---


# Установка оболочки и VNC-сервера

#### Установить элементы графического интерфейса для VNC
```bash
sudo apt install xfce4 xfce4-goodies
```
#### Установка сервера TightVNS
```bash
sudo apt install tightvncserver
```

#### Чтобы обеспечить доступ к VNC-серверу, обеспечьте зависимость dbus-x11.
```bash
sudo apt install dbus-x11
```
#### Запустить vncserver
```bash
vncserver
```

> Настроить пароль при подключении к VNC-серверу  


#### конфигурационный файл
```bash
nano ~/.vnc/xstartup
```

#### Добавить строки
```bash
#!/bin/bash
xrdb $HOME/.Xresources
startxfce4 &
```

#### Дальше нужно назначить обновлённый файл исполняемым. 
```bash
sudo chmod +x ~/.vnc/xstartup
```

## Чтобы сменить пароль на VNC (TightVNC) в Linux, достаточно ввести команду:
```bash
vncpasswd
```





