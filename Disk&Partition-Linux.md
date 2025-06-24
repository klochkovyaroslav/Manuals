# Работа с дисками и разделами в Linux

# Parted
## Для создания раздела на новом диске с использованием

```bash
sudo parted /dev/sdb
```

```bash
    print
```

```bash
    mklabel gpt
```

```bash
    mkpart primary 0% 100%  # Создание раздела на всю доступную область
    или
    mkpart primary 0% 10GB # Создание раздела на 10Гб
```

#### Установка типа раздела Если раздел 1 - LVM
```bash
    set 1 lvm on  
```

```bash
    quit
```

#### Если не для LVM далее создаем файловую систему

```bash
sudo mkfs.ext4 /dev/sdb1
```

#### Можно создать раздел и отформатировать его, не входя в оболочку parted, например:
```bash
parted -a opt/dev/sdb mkpart primary ext4 0% 100% && mkfs.ext4 /dev/sdb1
```
