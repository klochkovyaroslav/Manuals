# Диагностика кластера Hyper-V

## Журналы кластера
#### 1. Запустить команду сбора журналов кластера
```powershell
Get-ClusterLog -UseLocalTime
```

##### Собрать журналы кластера за последние 10 мин
```powershell
Get-ClusterLog -TimeSpan 10 -UseLocalTime
```

##### Собрать журналы кластера за последние 10 мин и Показывает 10 строк контекста до и после
```powershell
Get-ClusterLog -TimeSpan 10 -UseLocalTime | Select-String "Heartbeat state 'Unknown'" -Context 10
```
> Отчеты лежат на каждом хосте кластера в "C:\Windows\Cluster\Reports"

##### Парсинг логов
```powershell
Select-String -Path .\Cluster.log -Pattern "Cluster has lost the UDP connection"
Get-ChildItem -Recurse | ?{$_.FullName -match ".*log$"} | %{Select-String -Path $_.Fullname -Pattern "2025/03/28.*Cluster has lost the UDP connection"}
Get-ChildItem -Recurse | ? FullName -match '\.log$' | % { sls -Path $_ -Pattern '2025/03/28.*Cluster has lost the UDP connection'; "`n`n" }
ls -r *.log | %{sls "2025/03/28.*Cluster has lost the UDP connection" $_; "`n`n"}
ls -r *.log | %{if($r=sls "2025/03/28.*Cluster has lost the UDP connection" $_){$r; "`n" * 2}}

```
## 2. Запустить команду для отображения состояние Cluster Shared Volumes (CSV)
##### Получает информацию о состоянии общих томов кластера
```powershell
Get-ClusterSharedVolume
Get-ClusterSharedVolumeState | Format-Table -AutoSize
```
##### Состояние Cluster Shared Volumes (CSV) у которых состояние не Direct
```powershell
Get-ClusterSharedVolumeState | ?{$_.StateInfo -ne "direct"} | sort node | ft -AutoSize
```

##### Состояние Cluster Shared Volumes (CSV) по конкреной ноде
```powershell
Get-ClusterSharedVolumeState | ?{$_.Node -eq "TCC-NTC-02"} | sort node | ft -AutoSize
```

##### Состояние Cluster Shared Volumes (CSV) по ноде у которых состояние CSV не Direct
```powershell
#Получаем узлы с состоянием не "direct"
$clusternodes_not_direct = Get-ClusterSharedVolumeState | 
    Where-Object {$_.StateInfo -ne "direct"} | 
    Select-Object -ExpandProperty Node -Unique

# Для каждого такого узла выводим информацию
$clusternodes_not_direct | ForEach-Object {
    $node = $_
    Get-ClusterSharedVolumeState | 
        Where-Object {$_.Node -eq $node} |
        Format-Table -AutoSize
}
```
##### Состояние Cluster Shared Volumes (CSV) по каждой ноде у которых состояние CSV Direct
```powershell
#Получаем узлы с состоянием "direct"
$clusternodes_not_direct = Get-ClusterSharedVolumeState | 
    Where-Object {$_.StateInfo -eq "direct"} | 
    Select-Object -ExpandProperty Node -Unique

# Для каждого такого узла выводим информацию
$clusternodes_not_direct | ForEach-Object {
    $node = $_
    Get-ClusterSharedVolumeState | 
        Where-Object {$_.Node -eq $node} |
        Format-Table -AutoSize
}
```




## 3. Состояние узлов кластера
```powershell
Get-ClusterNode
```

## Полезные команды для диагностики:

#### Информация о SVC томах
```powershell
Get-ClusterSharedVolume  | Select-Object * | ft -AutoSize
```

#### Проверка владельца CSV для конкретного узла кластера
```powershell
$NodeName = "TCC-GVC-01"
Get-ClusterSharedVolume | ForEach-Object {
    [PSCustomObject]@{
        "CSV Name" = $_.Name
        "Owner Node" = $_.OwnerNode.Name
        "State" = $_.State
    }
} | Where-Object { $_.'Owner Node' -eq $NodeName } | Format-Table -AutoSize
```


#### Посмотреть Quorum в кластере Hyper-v
```powershell
Get-ClusterResource | ?{($_.ResourceType -eq "File Share Witness") -or ($_.ResourceType -eq "Physical Disk") }
```

#### Запретить узлу кластера быть владельцем определенного svc тома
```powershell
$CSV = Get-ClusterSharedVolume #-Name "data6"
$CurrentOwnerNodes = $CSV | Get-ClusterOwnerNode
$CurrentOwnerNodes | Format-Table
# Удалить нежелательный узел (например, "HV-Node2")
$NewOwnerNodes = $CurrentOwnerNodes | Where-Object { $_ -ne "HV-Node2" }
# Применить изменения
$CSV | Set-ClusterOwnerNode -Owners $NewOwnerNodes
```

#### Просмотр все CSV и их владельцев
```powershell
Get-ClusterSharedVolume | ForEach-Object {
    [PSCustomObject]@{
        "CSV Name" = $_.Name
        "Owner Node" = $_.OwnerNode.Name
        "State" = $_.State
    }
} | Format-Table -AutoSize
```

#### Посмотреть по Узлу кластера какими SVC он владеет
```powershell
$a=1
while($a -ne 200)
{
	Start-Sleep -Seconds 10
    $a++
    $NodeName = "Nerpa-GVC-01"
    Get-ClusterSharedVolume | ForEach-Object {
        [PSCustomObject]@{
            "CSV Name" = $_.Name
            "Owner Node" = $_.OwnerNode.Name
            "State" = $_.State
        }
    } | Where-Object { $_.'Owner Node' -eq $NodeName } | Format-Table -AutoSize
}
```

#### Определить имя узла кластера по его номеру. Список всех узлов кластера с их ID
```powershell
Get-ClusterNode | Select-Object Name, Id
```

#### Состояние сетевых интерфейсов в Кластере
```powershell
Get-ClusterNetwork | Format-Table -AutoSize  # Сети кластера
Get-ClusterNetworkInterface -Node (Get-ClusterNode).Name | Format-Table -AutoSize  # Интерфейсы узлов
```

----

# Проверка событий (Event Logs) с хостов кластера
## События кластера
```powershell
Get-WinEvent -LogName "Microsoft-Windows-FailoverClustering/Operational" -MaxEvents 50 | Format-Table -AutoSize
```
## События Hyper-V
```powershell
Get-WinEvent -LogName "Microsoft-Windows-Hyper-V-VMMS-Admin" -MaxEvents 50 | Format-Table -AutoSize
```

## Журнал Миграции - событий Hyper-V
```powershell
Get-WinEvent -LogName "Microsoft-Windows-Hyper-V-VMMS-Admin" | 
    Where-Object { $_.Message -like "*live migration*" } | 
    Select-Object TimeCreated, Message -First 50
```


----
# Набор инструментов TroubleShootingScript (TSS) 
[Общие сведения о наборе инструментов TSS](https://learn.microsoft.com/ru-ru/troubleshoot/windows-client/windows-tss/introduction-to-troubleshootingscript-toolset-tss#how-to-start-the-tss-toolset).  
Скачать здесь в виде ZIP-файла (TSS.zip)  - [Download](https://aka.ms/getTSS)  

## Собрать подробный лог с хостов кластера Hyper-V

#### Краткая инструкция:
* Скопировать "TSS.zip" на любой хост кластера
* Распаковать в C:\tss и перейти в каталог
* Открыть Powershell из под админа
* Set-ExecutionPolicy -scope Process -ExecutionPolicy RemoteSigned -Force (Политика выполнения скрипта должна быть RemoteSigned)
или
* Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
  
#### Запуск скрипта:
```powershell
.\TSS.ps1 -SDP HyperV
```

> нажать: "А, Yes to All"  
> Отчет будет лежать в "C:\MS_DATA"







