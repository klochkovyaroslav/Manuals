```powershell
Get-ClusterLog -UseLocalTime
```
> Отчеты лежат на каждом хосте кластера в "C:\Windows\Cluster\Reports"

##### получает информацию о состоянии общих томов кластера
```powershell
Get-ClusterSharedVolumeState | Format-Table -AutoSize
```
