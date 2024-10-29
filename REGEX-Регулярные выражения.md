# REGEX (Регулярные выражения)

#### Вывести все незакомментированниые и не пустые строки

```bash
sudo grep -Pv "^(#|$)" /etc/zabbix/zabbix_server.conf
или
sudo grep ^[[:alnum:]] /etc/zabbix/zabbix_server.conf
```

#### процессы с именем файлов: test+первая цифра: 2-цать или 3-цать, вторая цифра от 0 до 9
```bash
watch -n 5 "ps -x | grep 'test[2-3][09]'"
```
#### файлы с именем: test+первая цифра: 2-цать или 3-цать, вторая цифра от 0 до 9 и убрать первую строку из вывода команды
```bash
watch -n 5 "ls -sh /mnt | grep 'test[2-3][09]' | sed '1d'"
```

#### файлы с именем: test9 и test10
```bash
watch -n 5 "ls -sh /mnt | grep 'test[9]|1[0]'"
```
