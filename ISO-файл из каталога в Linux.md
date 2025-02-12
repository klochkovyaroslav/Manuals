# Как создать ISO-файл из каталога в Linux

#### Установка программы genisoimage в Linux
```bash
sudo apt install genisoimage
```
#### Создание ISO-файла из каталога
```bash
genisoimage -o ~/ExtraDrivers-4.2-553.6.1.iso /home/klochkov/Downloads/ExtraDrivers-4.2-553.6.1/
```
#### Смонтируйте ISO-файл
```bash
sudo mkdir /mnt/iso
```
```bash
mount -o loop ~/ExtraDrivers-4.2-553.6.1.iso /mnt/iso
