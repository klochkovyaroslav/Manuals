# FirewallD

#### Проверить статус firewalld Убедитесь, что firewalld запущен:
```bash
sudo systemctl status firewalld
```

## IPTABLES


#### Выводит подробный список правил в цепочке PREROUTING таблицы NAT
```bash
sudo iptables -t nat -vnL PREROUTING --line-numbers
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
