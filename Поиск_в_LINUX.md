# FIND

#### Найти файлы, которые занимают больше 1Gb:

```bash
find . -mount -type f -size +1G 2>/dev/null
```

#### Найти файлы, которые занимают меньше 2Gb:
```bash
find . -mount -type f -size -2G 2>/dev/null
```

#### Найти файлы, которые занимают от 1Gb до 2Gb:
```bash
find . -mount -type f -size -2G -a -type f -size -1G 2>/dev/null
```

#### Найти файлы и посчитать их размер:
```bash
find . -type f -exec du -sh {} \;
find . -type f -exec du -sh {} \; | sort -h
find . -type f -exec du -sh {} \; | sort -h | head -3
find . -type f -exec du -sh {} \; | sort -h | tail -3
```

#### Найти файлы и вывести их содержимое:
```bash
find . -type f -exec cat {} \;
find . -type f -exec ls -l {} \; -exec cat {} \;
find . -type f -exec ls -l {} \; -exec cat {} \; -exec echo "----" \; 
```

#### Найти файлы и удалить их:
```bash
find . -type f -name "*txt" -print
find . -type f -name "*txt" -delete
```



2>/dev/null используется, чтобы не показывать ошибки (например, если нет доступа к файлу)  

#### Найти файлы по имени:

```bash
find
find some_file
find some_file*
find -iname SOME_fiLe*
find . -name "*json"
find ./ -name "nginx.conf"
find ./ -name "nginx.*"
find some_files? - искать символ в конце
find some_files?? - искать 2 символа в конце
find some_files?.*
find . -type f -name "*.log" -o -type f -name "*.conf"
```

#### Найти файлы типу:

```bash
find ~ -type f -name "*test*"
find ~ -type d -name "*test*"
```


#### Найти файлы исключая какую нибудь дерикторию:

```bash
find . -type d -path ./test -prune -o -name "*.conf" -print
find . -type d -path ./test -prune -o -name "*.conf" -path ./test2 -prune -o -name "*.conf" -print
find . -type d -name "*test*" -prune -o -type d -name "*test2*" -prune -o -name "*.conf" -print
```
-o :или  
-prune :исключить  


### Найти файлы по метаданным
#### Найти файлы которые читались за последние сутки:
```bash
find . -type f atime 0  
```

#### Найти файлы которые изменялись за последние сутки:
```bash
find . -type f mtime 0  
```

#### Найти файлы к которым получали доступ позднее чем к какому-то указанному файлу:
```bash
find . -type f -anewer test1.raw  
```

#### Найти файлы которые были изменены позднее чем к какой-то указанный файл:
```bash
find . -type f -cnewer test1.raw  
```
-anewer :access  
-cnewer :change  

#### Найти файлы с глубиной вложенности 1:
```bash
find ~ -maxdepth 1
```


# GREP

#### Найти файлы по содержимому:

```bash
grep -rin --include="*.conf" "servers" ./
```


# PIRGREP

#### Найти файлы по содержимому:

```bash
rg -i "servers" ./
```
