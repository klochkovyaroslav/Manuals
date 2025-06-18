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

### Добавляем Proxmox VE репозиторий:

```bash
echo "deb [arch=amd64] http://download.proxmox.com/debian/pve bookworm pve-no-subscription" > /etc/apt/sources.list.d/pve-install-repo.list
```

#### Можно проверить что репозиторий добавлен:

```bash
cat /etc/apt/sources.list.d/pve-install-repo.list
```

### Добавляем ключ

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

## Устанавливаем ядро Proxmox VE

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

### Нужно удалить родное debian ядро:

```bash
apt remove linux-image-amd64 'linux-image-6.1*'
```

#### Обновить файл конфигурации GRUB:

```bash
update-grub
```

#### Удалить пакет os-prober:

os-prober — это утилита, которая сканирует разделы диска на наличие операционных систем и перечисляет их в записи GRUB. Однако сканируемые разделы могут также включать разделы, назначенные виртуальным машинам. Вы, конечно, не захотите добавлять их в загрузочную запись. Чтобы этого не произошло, удалите пакет os-prober:

```bash
apt remote os-prober -y
```

### Перезагружаемся
```bash
systemctl reboot
```

#### Проверить что Proxmox слушает на 8006 порту 

```bash
sudo ss -tunelp | grep 8006
```

### Создать network bridge

В WEB интерфейсе в разделе network создать bridge с именем "vmbr0"  


#### ProxMox отключить проверку к подписке сервера к платной ТП:

```bash
su -
curl --proto '=https' --tlsv1.2 -sSf https://raw.githubusercontent.com/rickycodes/pve-no-subscription/main/no-subscription-warning.sh | sh
```

----

# Конвертация виртуального диска Hyper-V (VHDX) в KVM (QCOW2)  
## LINUX

![image](https://github.com/user-attachments/assets/f9c10c05-4439-4bc3-94a6-0249c9a2b204)

#### Необходимо установить пакет "qemu-img", с его помощью которого можно выполнять манипуляции с виртуальными дисками
```bash
yum install -y qemu-img
```
#### Перед запуском процесса конвертации можно запустить проверку диска "vhdx"
```bash
qemu-img check -r all VM_test_C.vhdx
```

#### Запустить процесс конвертации диска "vhdx" в "qcow2"
```bash
qemu-img convert -O qcow2 VM_test_C.vhdx VM_test_sys.qcow2
```

## WINDOWS  
### Конвертируем диски с помощью этих утилит:

- _disk2vhd_ [Скачать disk2vhd можно здесь](https://download.sysinternals.com/files/Disk2vhd.zip)
- _qemu-img_ [Скачать qemu-img можно здесь](https://cloudbase.it/downloads/qemu-img-win-x64-2_3_0.zip)

#### Disk2vhd


----
# Windows VirtIO Drivers

[Windows VirtIO Drivers](https://pve.proxmox.com/wiki/Windows_VirtIO_Drivers#Installation)

