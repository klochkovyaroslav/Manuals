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
2>/dev/null используется, чтобы не показывать ошибки (например, если нет доступа к файлу)  

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
sudo find / -name acrocmd

```

#### Найти файлы типу:

```bash
find ~ -type f -name "*test*"
find ~ -type d -name "*test*"
find /etc -type l -name "*conf"
sudo find / -type d -name "CommandLineTool"
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


#### Найти файлы новее чем к какой-то указанный файл:
```bash
find . -type f -newer test1.raw  
```

#### Найти файлы НЕ новее чем к какой-то указанный файл:
```bash
find . -type f !-newer test1.raw  
```

#### Найти файлы с глубиной вложенности 1:
```bash
find ~ -maxdepth 1
```

#### Найти файлы по владельцу:
```bash
find . -type f -user admin 
```

#### Найти файлы по группе владения:
```bash
find /usr/bin -group root
```

#### Найти файлы по атрибутам:
```bash
find /boot -perm 600
```

#### Найти файлы с установлеными SUID и SGIG битами:
```bash
sudo find / -nount -type f -perm /u+s,g+s -ls
```

#### Найти файлы по расширению по пользователю и назначить им права записи на файл для всех

```bash
find /home/user1 -type f -name "*.txt" -user user1 -exec chmod a+w {} \;
```
" {} \; "  :означает что имя каждого найденного файла подставляется в виполняемую команду на место " {} ", а  " \; " :показывает что по ОДНОМУ найденному имени нужно передовать команде в качестве аргумента перед ее выполнением.  

```bash
find ~ -type f -name "*.txt" -exec cat {} + > one_big.file
```
{} +  :означает что все найденные файлы будут переданы команде одной кучей аргументов.  

----

# GREP

#### Найти файлы по содержимому:

```bash
grep -rin --include="*.conf" "servers" ./
grep "mail" /etc/passwd
grep "^mail" /etc/passwd
grep "mail$" /etc/passwd
```

----

# PIRGREP

#### Найти файлы по содержимому:

```bash
rg -i "servers" ./
```

# Silver Searcher (лучше чем GREP-ы)

#### Найти файлы по содержимому:

```bash
sudo apt install silversearcher-ag
```
> sudo ag "192.168.56.0/21" /  
чтобы поисковый запрос рассматривался именно как строка, используйте опцию -Q  
> sudo ag -Q "if [ -r /etc/default/locale ]" /  

