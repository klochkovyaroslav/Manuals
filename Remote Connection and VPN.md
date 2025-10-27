# AnyDesk

#### Add the AnyDesk GPG key
```bash
sudo apt update
```
```bash
sudo apt install ca-certificates curl apt-transport-https
```
```bash
sudo install -m 0755 -d /etc/apt/keyrings
```
```bash
sudo curl -fsSL https://keys.anydesk.com/repos/DEB-GPG-KEY -o /etc/apt/keyrings/keys.anydesk.com.asc
```
```bash
sudo chmod a+r /etc/apt/keyrings/keys.anydesk.com.asc
```

#### Add the AnyDesk apt repository
```bash
echo "deb [signed-by=/etc/apt/keyrings/keys.anydesk.com.asc] https://deb.anydesk.com all main" | sudo tee /etc/apt/sources.list.d/anydesk-stable.list > /dev/null
```
#### Update apt caches and install the AnyDesk client
```bash
sudo apt update
```
```bash
sudo apt install anydesk
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


#### Если запрашивается авторизация для выполнения политик при удаленном входе
![image](https://github.com/user-attachments/assets/cf6cdc53-91bb-4a9f-a085-397b3ecf9061)

#### Вариант 1
#### Можно настроить управление запросами аутентификации, появляющимися при удаленном подключении к системе.

```bash
sudo nano /usr/share/polkit-1/actions/org.freedesktop.color.policy
```

> Для поиска по файлу нажать CTRL+W ввести:  
> _Authentication is required to create a color managed device_

Modify <allow_any> settings
```
<defaults>
      <allow_any>yes</allow_any>
      <allow_inactive>no</allow_inactive>
      <allow_active>yes</allow_active>
</defaults>
```
![image](https://github.com/user-attachments/assets/3f02e57d-eaee-4e83-aed4-016a5df0cabf)

#### Вариант 2
#### Создать переопределение Polkit, которое позволяет выполнять действия с цветовым профилем без запроса пароля

##### Изменить файл "45-allow-colord.pkla" или создать при его отсутсвии
```bash
sudo nano /etc/polkit-1/localauthority/50-local.d/45-allow-colord.pkla
```
##### Вставить содержимое
```
[Allow Colord All Users]
Identity=unix-user:*
Action=org.freedesktop.color-manager.create-device;org.freedesktop.color-manager.create-profile;org.freedesktop.color-manager.delete-device;org.freedesktop.color-manager.delete-profile;org.freedesktop.color-manager.modify-device;org.freedesktop.color-manager.modify-profile
ResultAny=no
ResultInactive=no
ResultActive=yes
```

#### Перезапустить службу
```bash
sudo systemctl restart polkit
```

#### Конфигурацию сервиса xrdp-sesman, который отвечает за управление сессиями удаленного рабочего стола
```bash
sudo nano /etc/xrdp/sesman.ini
```


---


# Установка оболочки и VNC-сервера

#### Установить элементы графического интерфейса для VNC
```bash
sudo apt install xfce4 xfce4-goodies
```
#### Установка VNC сервера TightVNC
```bash
sudo apt install tightvncserver
```

#### Чтобы обеспечить доступ к VNC-серверу, обеспечьте зависимость dbus-x11.
```bash
sudo apt install dbus-x11
```

#### Запустить для создания первоначальной конфигурации
```bash
vncserver
```
> Будет предложено ввести пароль с его подтверждением, который далее будет использоваться для взаимодействия с удаленной системой  
> После ввода обоих паролей утилита самостоятельно сгенерирует файл конфигурации "~/.vnc/xstartup"  

### Настройка сервера VNC

#### Перед началом настройки нужно установить сервер VNC
```bash
vncserver -kill :1
```

#### Сделать копию файла "xstartup"
```bash
mv ~/.vnc/xstartup ~/.vnc/xstartup.bak
```

#### Конфигурационный файл
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

#### Запустить vncserver
```bash
vncserver -geometry 1280x1080
```
или в режиме SSH-туннелирования для безопасного подключения

```bash
vncserver -localhost -geometry 1280x1080
```

### Запуск VNC как системной службы
```bash
sudo nano /etc/systemd/system/vncserver@.service
```
> Содержимое файла "vncserver@.service"

```
[Unit]
Description=Start TightVNC server at startup
After=syslog.target network.target
 
[Service]
Type=forking
User=my_vnc_user
Group=my_vnc_user
WorkingDirectory=/home/my_vnc_user
 
PIDFile=/home/username/.vnc/%H:%i.pid
ExecStartPre=-/usr/bin/vncserver -kill :%i > /dev/null 2>&1
ExecStart=/usr/bin/vncserver -depth 24 -geometry 1280x800 :%i
ExecStop=/usr/bin/vncserver -kill :%i
 
[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
```
#### Включить файл модуля
```bash
sudo systemctl start vncserver@1
```

> Цифра 1, указаная после @, означает номер дисплея, на котором требуется активация службы  


Проверка работоспособности сервера VNC:
```bash
sudo systemctl status vncserver@1
```

## Чтобы сменить пароль на VNC (TightVNC) в Linux, достаточно ввести команду:
```bash
vncpasswd
```

---

# OpenConnect (Cisco AnyConnect)

#### Установить клиент OpenConnect
```bash
sudo apt-get install openconnect 
```
#### Посмотреть версию
```bash
openconnect --version
```

#### Для интеграции между Network Manager и OpenConnect
```bash
sudo apt-get install network-manager-openconnect-gnome
```

#### Перезапустить Network Manager, чтобы изменения были сохранены:
```bash
sudo systemctl restart NetworkManager
```



<img width="1257" height="440" alt="image" src="https://github.com/user-attachments/assets/287c064f-e96a-42b2-97f7-5bc4ab47d3a2" />
<img width="354" height="355" alt="image" src="https://github.com/user-attachments/assets/ed123a12-d408-45b1-9fc3-5a9394db01d7" />
<img width="582" height="785" alt="image" src="https://github.com/user-attachments/assets/3fa17b81-425e-4414-aad0-e9dd2094f003" />

