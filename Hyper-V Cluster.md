# Диагностика кластера Hyper-V

##### Журналы кластера
```powershell
Get-ClusterLog -UseLocalTime
```
##### Собрать журналы кластера за последние 10 мин
```powershell
Get-ClusterLog -TimeSpan 10 -UseLocalTime
```
##### Парсинг логов
```powershell
Select-String -Path .\Cluster.log -Pattern "Cluster has lost the UDP connection"
Get-ChildItem -Recurse | ?{$_.FullName -match ".*log$"} | %{Select-String -Path $_.Fullname -Pattern "2025/03/28.*Cluster has lost the UDP connection"}
Get-ChildItem -Recurse | ? FullName -match '\.log$' | % { sls -Path $_ -Pattern '2025/03/28.*Cluster has lost the UDP connection'; "`n`n" }
ls -r *.log | %{sls "2025/03/28.*Cluster has lost the UDP connection" $_; "`n`n"}
ls -r *.log | %{if($r=sls "2025/03/28.*Cluster has lost the UDP connection" $_){$r; "`n" * 2}}

```


##### Собрать журналы кластера за последние 10 мин и Показывает 10 строк контекста до и после
```powershell
Get-ClusterLog -TimeSpan 10 -UseLocalTime | Select-String "Heartbeat state 'Unknown'" -Context 10
```
> Отчеты лежат на каждом хосте кластера в "C:\Windows\Cluster\Reports"

##### Получает информацию о состоянии общих томов кластера
```powershell
Get-ClusterSharedVolume
Get-ClusterSharedVolumeState | Format-Table -AutoSize
```
## Полезные команды для диагностики:
#### Проверить все CSV:
```powershell
Get-ClusterSharedVolume
```
#### Состояние узлов кластера
```powershell
Get-ClusterNode
```

#### Определить имя узла кластера по его номеру. Список всех узлов кластера с их ID
```powershell
Get-ClusterNode | Select-Object Name, Id
```
