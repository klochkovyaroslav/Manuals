# Создание локального репозитория из ISO-образа в RedHat подобной ОС

#### Отключите онлайн репозитории:
```bash
dnf config-manager --disable "*"
```

#### Для организации локальных репозиториев с помощью ISO-образа, хранящегося в репозиториях zVirt, выполните следующие действия:
#### 1. Монтирование ISO-образа
```bash
mkdir /mnt/repo
mount -o loop ./repos_zvirt_42_202412290019.iso /mnt/repo
```
> -o loop – опция, которая указывает, что нужно использовать loop-устройство (виртуальный блочный девайс) для монтирования файла как блочного носителя.

#### 2. Создание директории для локального репозитория
```bash
mkdir -p /var/zvirt-repo
ln -s /mnt/repo /var/zvirt-repo
```

#### 3. Копирование содержимого ISO в локальный репозиторий
```bash
cp -r /mnt/repo/* /var/zvirt-repo/
```

#### 4. Настройка локального репозитория
##### Создайте файл описания offline-репозитория /etc/yum.repos.d/offline_zvirt.repo со следующим содержимым
```bash
nano /etc/yum.repos.d/offline_zvirt.repo
```
```
[offline_zvirt_main]
name=zvirt 4.2 main
baseurl=file:///var/zvirt-repo/repo/zvirt-4/4.2/
gpgcheck=0
enabled=1

[offline_zvirt_extras]
name=zvirt 4.2 extras
baseurl=file:///var/zvirt-repo/repo/zvirt-4/extras-4.2/
gpgcheck=0
enabled=1
```

#### 5. Обновление кэша YUM/DNF
```bash
yum clean all
yum makecache

dnf clean all
dnf makecache
```

#### 6. Проверка локального репозитория

```bash
yum repolist

dnf repolist
```
#### 7. Очистите кэш
```bash
dnf clean all
```
#### 8. Очистите блокировку версий
```bash
dnf versionlock clear
```
#### 9.  Обновите пакеты
```bash
dnf update -y
```
#### 10. Добавление дополнительных репозиториев (опционально)
##### Если у вас есть дополнительные репозитории (например, EPEL), вы можете скачать их и добавить в локальный репозиторий:

```bash
sudo mkdir -p /var/repo/local/epel
sudo rsync -avz rsync://mirror.example.com/epel/7/x86_64/ /var/repo/local/epel/
```

-------
# FTP Repository

#### Посмотреть текущий список репозиториев в Debian
```bash
sudo ls -l /etc/apt/sources.list.d/
```

#### Посмотреть текущий список репозиториев в Centos
```bash
sudo ls -l /etc/yum.repos.d/
```

```bash
apt update
apt install vsftpd 
systemctl status vsftpd.service 
nano /etc/vsftpd.conf 
systemctl reload vsftpd.service 
systemctl restart vsftpd.service
mkdir -p /srv/ftp/my_as_repo/conf
touch /srv/ftp/my_as_repo/conf/distributions
nano /srv/ftp/my_as_repo/conf/distributions
содержание:

Origin: Debian
Codename: 1.7_x86-64
Suite: 1.7.3
Architectures: amd64
Components: main contrib non-free
SignWith: yes

gpg --gen-key (имя ключа as_173)
apt install reprepro

reprepro --ask-passphrase -b /srv/ftp/my_as_repo export
reprepro -b /srv/ftp/my_as_repo/ includedeb 1.7_x86-64 /tmp/zoom_amd64.deb

gpg --armor --output repo_pgp.key --export as_173

apt-key add repo_pgp.key

apt edit-sources
содержание:
deb ftp://localhost/my_as_repo 1.7_x86-64 main contrib non-free

apt update 
apt install zoom  
```
---

### Добавить диск CD/DVD в список репозитариев

```bash
sudo apt-cdrom add
``` 
