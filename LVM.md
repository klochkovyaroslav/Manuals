# Подключение диска lvm к другому компьютеру
[Источник](https://itproffi.ru/podklyuchenie-diska-lvm-k-drugomu-kompyuteru)  
[Еще- Настройка и управление LVM разделами](https://winitpro.ru/index.php/2019/10/28/nastrojka-lvm-razdelov-v-linux/)  
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

# Увеличение LVM диска в виртуальной машине
## Шаги:
1. Увеличение размера диска виртуальной машины:
2. Изменение размера раздела с помощью fdisk или parted:
3. Обновление информации о физическом томе LVM:
4. Расширение логического тома:
5. Изменение размера файловой системы:
--
1. Увеличение размера диска виртуальной машины:
Необходимо увеличить размер диска виртуальной машины в системе виртуализации

3. Изменение размера раздела с помощью fdisk или parted:
На гостевой ВМ  
```bash
df -h
parted /dev/vda
```

Посмотрим размер физического диска и всех логических разделов:
```bash
print
```

2_1. Увеличим extended partition
```bash
resizepart 2 100%
```
или указать требуемый размер
```bash
resizepart 2
```
Указываем конечный размер раздела(Доступный размер посмотреть в строке "Disk /dev/sda: 10000GB")
10000GB

2_2. Увеличим logical partition
```bash
resizepart 2 100%
```
или указать требуемый размер
```bash
resizepart 5
```
10000GB
```bash
q
```
3. Обновление информации о физическом томе LVM:
3_1. Нужно увеличить размер физического диска в lvm  
```bash
pvs
```
смотрим "PFree"
```bash
partprobe -обновить информацию о разделах
pvresize /dev/vda5
```
```bash
pvs
```
смотрим "PFree" должен быть равен кол-ву добавленного простанства на диске  

4. Расширение логического тома:
4_1. Узнаем название logical volume:
```bash
lvdisplay
```
или
```bash
lvscan
```

4_2. Увеличиваем размер логического диска в lvm  
Расширяем logical volume до размера physical volume:  
```bash
lvextend /dev/vg-01/home -l +100%FREE
```
или  
4.2_1 Расширяем logical volume до требуемого размера от physical volume:  
```bash
lvextend /dev/vg-01/home -l +50%FREE
и оставшееся свободное простанство 
lvextend /dev/vg-01/var -l +100%FREE
```
```bash
lvs
```

5. Изменение размера файловой системы:
```bash
resize2fs /dev/vg-01/home
```

Если используется файловая система XFS, на CentOS 7 по умолчанию, то вместо resize2fs команда xfs_growfs:
```bash
xfs_growfs /dev/vg-01/home
```
```bash
df -h
```
