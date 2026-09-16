# FirewallD

#### Проверить статус firewalld Убедитесь, что firewalld запущен:
```bash
sudo systemctl status firewalld
```

## IPTABLES

```bash
sudo iptables -v -n -L --line-numbers
```


#### Выводит подробный список правил в цепочке PREROUTING таблицы NAT
```bash
sudo iptables -t nat -vnL PREROUTING --line-numbers
```

#### Добавить правило в Input
разрешить доступ только конкретному серверу 10.111.109.10  
```bash
sudo iptables -A INPUT -p tcp -s 10.111.109.10 --dport 9102 -j ACCEPT
```


### Как сохранить правила после перезагрузки

```bash
sudo apt install iptables-persistent
```
```bash
sudo netfilter-persistent save
```

#### Или вручную перезапишите файлы конфигурации:
```bash
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```


---

## NFTABLES


#### Посмотреть вообще все правила на вашем сервере:
```bash
sudo nft list ruleset
```

#### Вывод с номерами дескрипторов (handles)
```bash
sudo nft -a list ruleset
```


#### Выводит подробный список правил NAT
```bash
sudo nft list table ip nat
```

#### Посмотреть только список существующих таблиц
```bash
sudo nft -a list table ip nat
```
