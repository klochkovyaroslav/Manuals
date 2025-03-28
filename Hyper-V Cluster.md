# Диагностика кластера Hyper-V

##### Журналы кластера
```powershell
Get-ClusterLog -UseLocalTime
```
> Отчеты лежат на каждом хосте кластера в "C:\Windows\Cluster\Reports"

##### Получает информацию о состоянии общих томов кластера
```powershell
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
