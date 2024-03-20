# Установка Proxmox на Debian 12

```bash
sudo nano /etc/hosts
```

Комментируем первую строку:  
_#127.0.0.1  localhost_  
Меняем вторую строку с: _127.0.1.1  proxmox.home.local  proxmox_  на _192.168.88.28  proxmox.home.local  proxmox_  

#### Логинемся под root

```bash
su -
```

#### Добавляем Proxmox VE репозиторий:

```bash
echo "deb [arch=amd64] http://download.proxmox.com/debian/pve bookworm pve-no-subscription" > /etc/apt/sources.list.d/pve-install-repo.list
```

#### Можно проверить что репозиторий добавлен:

```bash
cat /etc/apt/sources.list.d/pve-install-repo.list
```

#### Добавляем ключ

```bash
wget https://enterprise.proxmox.com/debian/proxmox-release-bookworm.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bookworm.gpg 
```

#### Проверить что добавлен ключ:

```bash
ls -l /etc/apt/trusted.gpg.d/ | grep -i proxmox
```

#### Обновляем свой репозиторий в системе

```bash
apt update && apt full-upgrade
```

#### Устанавливаем ядро Proxmox VE

```bash
apt install proxmox-default-kernel
```

### Перезагружаемся
```bash
systemctl reboot
```

#### Устанавливаем пакеты:

```bash
apt install proxmox-ve postfix open-iscsi chrony
```

#### Нужно удалить родное debian ядро:

```bash
apt remove linux-image-amd64 'linux-image-6.1*'
```

#### Проверить что Proxmox слушает на 8006 порту 

```bash
sudo ss -tunelp | grep 8006
```
