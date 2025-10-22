# Редактирование файла Sudoers

Команда sudo настраивается с помощью файла, расположенного в каталоге /etc/sudoers  

```bash
sudo visudo
```

Строки пользовательских привилегий, что означают различные поля:  

**root** ALL=(ALL:ALL) ALL Первое поле показывает имя пользователя, которое правило будет применять к (root).  
root **ALL**=(ALL:ALL) ALL Первое “ALL” означает, что данное правило применяется ко всем хостам.  
root ALL=(**ALL**:ALL) ALL Второе “ALL” означает, что пользователь root может запускать команды от лица всех пользователей.  
root ALL=(ALL:**ALL**) ALL Третье “ALL” означает, что пользователь root может запускать команды от лица всех групп.  
root ALL=(ALL:ALL) **ALL** Последнее “ALL” означает, что данные правила применяются всем командам.  

### Пример
#### WinSCP с правами root:

Найти файл sftp-server:  
```bash
whereis sftp-server | awk ' {print $2}'
```
```bash
sudo visudo
```
В конец файла вставить:  
```bash
user ALL=NOPASSWD:/usr/lib/sftp-server
```
```bash
zabbix ALL = NOPASSWD: /usr/sbin/asterisk
```

#### В настройках приложения WinSCP

Кнопка: **Еще**, в пункте меню: **Среда -> SFTP**  
**Сервер SFTP:** sudo /usr/lib/sftp-server 

---

# sudo command not found» Debian, Ubuntu и CentOS
```bash
su -
```

```bash
usermod -aG root user
Перелогинется: (Ctrl+d)
```

```bash
apt install sudo -y
```

```bash
usermod -aG sudo user
Перелогинется: (Ctrl+d)
```

```bash
id
Проверить группы пользователя user
```

```bash
sudo -l
Просмотр полномочий
```

