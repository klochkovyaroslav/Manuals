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


#### Установка типа раздела
```bash
set 1 lvm on  # Если раздел 1 - LVM, установите флаг
```



