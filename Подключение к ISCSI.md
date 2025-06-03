# LINUX

## Создание таргета iSCSI

#### Установить пакет targetcli:

```bash
dnf install targetcli
```

#### Добавить службу target в автозагрузку и запустить:

```bash
systemctl enable target --now
```

#### Откройте targetcli:

```bash
targetcli
```
```bash
ls
```


#### Перейдите в раздел block:

```bash
cd backstores/block
```

#### Создайте фоновое хранилище:

```bash
create name=lun0 dev=/dev/sdc
ls
```

#### Создать ISCSI цель(target)

```bash
> iscsi/ create
```




----

#### Обнаружить iSCSI-target.
```bash
iscsiadm -m discovery -t st -p san.zvirt.test
```

### Восстановить конфигурацию
#### 1. Перезагрузите службу
```bash
sudo systemctl restart targetctl
```

#### 2.Восстановить конфигурацию
```bash
targetctl restore
```


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

### Журнал событий

#### Поиск событий связанных с источником iScsiPrt
```powershell
Get-WinEvent -LogName "System" | 
    Where-Object { $_.ProviderName -in ("iScsiPrt") } | 
    Select-Object TimeCreated, Message -First 55 | Sort-Object TimeCreated -Descending
```

#### Поиск событий связанных с источником iScsiPrt за указанный период времени
```powershell
$startDate = [datetime]"2025-04-26"
$endDate = [datetime]"2025-04-27"

Get-WinEvent -FilterHashtable @{
    LogName = "System"
    ProviderName = "iScsiPrt"
    StartTime = $startDate
    EndTime = $endDate
} -ErrorAction SilentlyContinue | 
    Select-Object TimeCreated, Message | 
    Sort-Object TimeCreated -Descending | 
    Select-Object -First 55
```

#### Поиск событий связанных с источником iScsiPrt за указанный период времени
```powershell
Get-EventLog -LogName "System" -Source "iScsiPrt" -After "2025-04-26" -Before "2025-04-27" | 
    Select-Object TimeGenerated, Message | 
    Sort-Object TimeGenerated -Descending
```
