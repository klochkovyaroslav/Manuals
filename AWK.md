# Вывести только второй столбец из файла

```bash
awk '{print $2}' dp_routs1.txt
```

# Перенаправление в тот же файл (с временным файлом):
```bash
awk '{print $2}' dp_routs1.txt > temp.txt && mv temp.txt dp_routs1.txt
```

# Выводит общее количество подключенных мониторов в Linux 
(awk -F ': ' '{print $2}' — Делит строку по двоеточию с пробелом)  
```bash
xrandr --listmonitors | grep "Monitors:" | awk -F ': ' '{print $2}'
```
