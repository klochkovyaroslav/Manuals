# FIND

#### Найти файлы, которые занимают больше 1Gb

```bash
find . -mount -type f -size +1G 2>/dev/null
```
2>/dev/null используется, чтобы не показывать ошибки (например, если нет доступа к файлу)  

#### Найти файлы по имени:

```bash
find ./ -name "nginx.conf"
find ./ -name "nginx.*"
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
