# LINUX

## Создание таргета iSCSI

#### Установить пакет targetcli RPM

```bash
dnf install targetcli
```
#### DEB
```bash
sudo apt install targetcli-fb
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
![image](https://github.com/user-attachments/assets/1a394873-432a-4eae-a442-3a6715d6b721)


#### Перейдите в раздел block:

```bash
cd backstores/block
```
![image](https://github.com/user-attachments/assets/09933fba-ca70-4bb0-aadf-a859be599cf7)


#### Создайте фоновое хранилище:

```bash
create name=lun0 dev=/dev/vg0-iscsi/lv-iscsi
ls
```
![image](https://github.com/user-attachments/assets/e55326d5-ec67-4f88-ad90-5d124719efb4)


#### Создать ISCSI цель(target)

```bash
cd //
cd /iscsi
create
ls
```
![image](https://github.com/user-attachments/assets/c295dd86-2a9a-4629-87b7-27b6de29de38)


#### Перейти в созданную портальную группу(tpg1)
```bash
iqn.2003-01.org.linux-iscsi.iscsi-nfs.x8664:sn.0374d190fbd4/tpg1/
```

#### Добавить созданное ранее фотовое хранилище в портальную группу

```bash
cd luns
create /backstores/block/lun0
```

#### Настроить ACL портальной группы(tpg1), добавив iqn инициатора(клиента)

```bash
cd acls
create iqn.2005-09.com.redhat:566eb276ae12
ls
```
![image](https://github.com/user-attachments/assets/dff2e610-31ef-4ca7-aef1-aa90f413387f)


#### Сохранить конфигурацию

```bash
saveconfig 
```

#### Выйти

```bash
exit 
```

> iqn — имя таргета.  
> tpg1 (Target Portal Group) – список IP-адресов и TCP портов, которые будет слушать этот таргет.  
> acls – список адресов, с которых можно будет подключиться к таргету.  
> luns – список адресов дискового устройства в сетях хранения.  
> portals – список IP-адресов и портов, которые будет слушать таргет.  


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
