# LINUX

#### Установить пакет
```bash
sudo apt install open-iscsi
```

#### Все найденные target'ы хранятся в /etc/iscsi/
```bash
/etc/iscsi/
```

#### IQN инициатора
```bash
cat /etc/iscsi/initiatorname.iscsi
```


----

# WINDOWS
## Настройка



## Диагностика

#### Пинг до iSCSI-таргета:
```powershell
ping 192.168.1.200
```

#### Проверить открыт ли порт 3260:
```powershell
Test-NetConnection 192.168.1.200 -Port 3260
```

#### Проверить MTU (должен совпадать на всех узлах):
```powershell
netsh interface ipv4 show subinterfaces
```

#### Проверить сессии iSCSI
```powershell
Get-IscsiSession | Format-Table -AutoSize
```

#### Проверить, отвечает ли iSCSI-таргет
```powershell
iscsicli ListTargets 192.168.1.200
```

#### Перезапустить службу iSCSI
```powershell
Restart-Service MSiSCSI
```
