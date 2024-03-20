# Подключение диска lvm к другому компьютеру
[Источник](https://itproffi.ru/podklyuchenie-diska-lvm-k-drugomu-kompyuteru)
[Настройка и управление LVM разделами](https://winitpro.ru/index.php/2019/10/28/nastrojka-lvm-razdelov-v-linux/)
### Установка утилиты lvm2

```bash
apt install lvm2
yum install lvm2
```

#### Смотрим разделы lvm

```bash
lvscan
```
На подключенном диске lvm разделы не активны, Активируем:

#### Активируем lvm разделы
```bash
vgchange -ay
```
Если получаем ошибку что устройство занято:  
_device-mapper: create ioctl on centos-swap LVM-XdEJP3Hsfbd211xy3MtwxKFDy7bgVOKqlBvYSL2wKZ0AnQyMeDzu6UNWJ9PlLL09 failed: Устройство или ресурс занято_  
Для начала нужно переименовать подключенный том в другое имя.  

#### Переименовываем lvm том

```bash
vgdisplay
```
Берем UUID нужного тома

```bash
vgrename SiwSUw-BqIR-I3EB-S7Kq-HopK-b8Ln-Zg2T7U lvm-root_new_name

```
#### Активируем lvm разделы

```bash
vgchange -ay
lvscan
```
Нужный раздел должен быть: ACTIVE  

#### Монтирование LVM разделов

```bash
mkdir /mnt/disk_lvm_new
mount /dev/centos_old/lvm-root_new_name /mnt/disk_lvm_new
df -h
```
