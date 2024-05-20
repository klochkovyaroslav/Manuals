# FIND

#### Найти файлы, которые занимают больше 1Gb

```bash
find . -mount -type f -size +1G 2>/dev/null
```
2>/dev/null используется, чтобы не показывать ошибки (например, если нет доступа к файлу)  

#### Найти файлы по имени:

```bash
find some_file
find some_file*
find -iname SOME_fiLe*
find ./ -name "nginx.conf"
find ./ -name "nginx.*"
find some_files? - искать символ в конце
find some_files?? - искать 2 символа в конце
find some_files?.*
```

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
