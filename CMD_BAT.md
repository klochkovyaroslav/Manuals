# CMD Команды

#### Удалить файлы старше 60 дней из указанной папки

```
ForFiles /p "X:\path" /s /c "cmd /c del @file /f /q" /d -60
```

#### Удалить файлы, созданные сегодня из указанной папки

```
ForFiles /p "X:\path" /s /c "cmd /c del @file /f /q" /d -0
```

#### Получить список файлов, потому что они старше указанной даты

```
ForFiles /p "X:\path" /s /c "cmd /c echo @path @file" /d -5
```

#### Создать файл проивольного размера
#### Утилита MS "fsutil"

```
fsutil file createnew d:\1gb-file.txt 10737418240
```

### Чтобы удалить LCU после установки объединенного пакета SSU и LCU:

DISM/Remove-Package "Имя пакета"

#### Имя пакета можно найти с помощью следующей команды:

DISM /online /get-packages
