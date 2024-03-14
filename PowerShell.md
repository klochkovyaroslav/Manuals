# PowerShell - Примеры

## Разное

### ОС - Windows

#### Для регистрации текущего сеанса PowerShell используется командлет Start-Transcript.

```bash
Start-Transcript -Append C:\Scripts\PS-Script-Log.txt
Start-Sleep -Seconds 10
Stop-Transcript
```

#### PowerShell. Время запуска и окончания команды содержится в атрибутах StartExecutionTime и EndExecutionTime

```bash
Get-History | select StartExecutionTime,EndExecutionTime,CommandLine
```

#### Получить время выполнения последней команды:

```bash
$lastcommand=Get-History | select -Last 1 -Property *
$lastcommand.EndExecutionTime - $lastcommand.StartExecutionTime
```


#### Создать файл произвольного размера(10Gb)

```bash
$file = New-Object -TypeName System.IO.FileStream -ArgumentList D:\File_random_size.txt,Create,ReadWrite
$file.SetLength(10240Mb)
$file.Close()
```


#### Логи со всех участников кластера за последние 10 часов.

```bash
Get-ClusterLog –TimeSpan 360 –UseLocalTime –Destination "C:\Temp"
```

#### Логи ноды кластера за последний 1 час.

```bash
Get-ClusterLog -Node SV07 -TimeSpan 60 -UseLocalTime -Destination "C:\Temp"
```

#### По списку из файла c:\scripts\Computers.txt выведет имя компьютера и версию офиса.

```bash
Get-Content -Path c:\scripts\Computers.txt |
ForEach-Object {
    Write $_
    Get-WmiObject Win32_Product -Filter "Name like '%Office%'" -ComputerName $_ | Select Name, Version
}
```
#### Выведет выведет имя компьютера и версию офиса локального ПК

```bash
Write $env:COMPUTERNAME
Get-WmiObject Win32_Product -Filter "Name like '%Office%'" -ComputerName $env:COMPUTERNAME | Select Name, Version
```


## Hyper-V

### Включить поддержку вложенной виртуализации для процессора:
#### На железном хосте ВМ Hyper-V выполнить в PS для нужных ВМ:

```bash
Set-VMProcessor -VMName 'SQL-TEST2' -ExposeVirtualizationExtensions $true
```

#### Для корректной работы сети, нужно включить MAC spoofing:

```bash
Get-VMNetworkAdapter -VMName 'SQL-TEST2' | Set-VMNetworkAdapter -MacAddressSpoofing On
```

#### Получить список виртуальных машин и их статус:

```bash
Get-ClusterResource | ? {$_.ResourceType -eq “Virtual Machine”}|ft Name, State -AutoSize
```

#### Создать новую виртуальных машину:

```bash
New-VM -VMName testVM01 -ComputerName PDC-VHOST01 -MemoryStartupBytes 1024Mb -SwitchName Datacenter -NewVHDPath C:\ClusterStorage\Volume2\TestVM01\testVM01.vhdx -NewVHDSizeBytes 127Gb -Path C:\ClusterStorage\Volume2\TestVM01 -Generation 2 -Verbose
```


#### Добавить ВМ в кластер запустив локально на исходном узле:

```bash
Add-ClusterVirtualMachineRole -VMName testVM01
```

#### Удаление кластерной виртуальной машины

```bash
Remove-ClusterGroup -VMId (Get-VM -Name testVM01).VMId -RemoveResources
```

#### Возобновление кластерных служб на узле

```bash
Suspend-ClusterNode -Name HV11 -Drain
```

#### Список томов CSV:

```bash
Get-ClusterSharedVolume
```

#### Информация о томе CSV:

```bash
Get-ClusterSharedVolumeState -Name "Cluster Disk 1"
```

#### Добавить диск в CSV можно командой:

```bash
Add-ClusterSharedVolume -Cluster Cluster1 -Name "Cluster Disk 1"
```

#### Удалить диск CSV можно командой:

```bash
Remove-ClusterSharedVolume -Cluster Cluster1 -Name "Cluster Disk 1"
```

#### Перевести CSV диск в режим перенаправления (File System Redirected mode) можно командой:

```bash
Suspend-ClusterResource -Name "Cluster Disk 1" -RedirectedAccess -Force
```

#### Выключить режима перенаправления (File System Redirected mode) можно командой:

```bash
Resume-ClusterResource -Name "Cluster Disk 1"
```

#### Получить сведения о дисках-свидетелях

 ```bash
Get-ClusterResource -Name "*Cluster Quorum*"
```

#### Live миграция в рамках PowerShell:

```bash
Move-ClusterVirtualMachineRole testVM01 -Node HV11
```

#### Быстрая миграция в рамках PowerShell:

```bash
Move-ClusterVirtualMachineRole -MigrationType Quick testVM01 -Node HV11
```

#### Старт ВМ
```bash
Start-VM -Name testVM01
```

### Внесение обновлений в свойства ВМ

#### Выделить 8 процессоров
```bash
Set-VMProcessor testVM01 -Count 8
```

#### Выделить динамическую память процессоров
```bash
Set-VMMemory testVM01 -DynamicMemoryEnabled $true -MinimumBytes 512MB -StartupBytes 2048MB -MaximumBytes 8GB
```

#### Добавить DVD-дисковод как SCSI-контроллер
```bash
Add-VMDvdDrive -VMName testVM01
```


## Снимки (Checkpoint): Стандартные и Производственные в Hyper-V

#### Включить стандартные снимки на ВМ:
```bash
Set-VM -Name testVM01 -CheckpointType Standard
```

#### Включить производственные снимки на ВМ:
```bash
Set-VM -Name testVM01 -CheckpointType ProductionOnly
```

#### Включить производственные снимки на ВМ с возможностью создания стандартных:
```bash
Set-VM -Name testVM01 -CheckpointType Production
```


#### Сделать стандартный снимок на ВМ:
```bash
Checkpoint-VM -Name testVM01 -CheckpointName Standard_Checkpoint
```

#### Применить стандартный снимок на ВМ:
```bash
Restore-VMCheckpoint -VMName testVM01 -Name Standard_Checkpoint -Confirm:$false
```

#### Сделать стандартный снимок на ВМ:
```bash
Checkpoint-VM -Name testVM01 -CheckpointName Production_Checkpoint
```

#### Слить снимок или список снимков ВМ с Диском :
```bash
Merge-VHD -Path C:\ClusterStorage\Volume13\TEST-SQL2\TEST_SQL2_C_779FE71D-DC72-46EA-B874-2349FDB4D021.avhdx -DestinationPath C:\ClusterStorage\Volume13\TEST-SQL2\TEST_SQL2_C.vhdx
```


## СЕТЬ

#### Мониторинг открытых TCP/IP подключений
```bash
Get-NetTCPConnection -State Listen
```

#### Порты, которые слушаются (открыты) 
```bash
Get-NetTCPConnection -State Listen | Select-Object -Property LocalAddress, LocalPort, RemoteAddress, RemotePort, State | Sort-Object LocalPort |ft
```

#### Вывести только внешние (Интернет) подключения:

```bash
Get-NetTCPConnection -AppliedSetting Internet
```

#### Скрипт выполнил разрешение всех IP адресов хостов в DNS имена, и для каждого соединения указал имя процесса, который его использует:
```bash
Get-NetTCPConnection -State Established |Select-Object -Property LocalAddress, LocalPort,@{name='RemoteHostName';expression={(Resolve-DnsName $_.RemoteAddress).NameHost}},RemoteAddress, RemotePort, State,@{name='ProcessName';expression={(Get-Process -Id $_.OwningProcess). Path}},OffloadState,CreationTime |ft
```


#### По имени PID родительского процесса можно вывести список связанных имен служб Windows, которые используют сеть:

```bash
Get-WmiObject Win32_Service | Where-Object -Property ProcessId -In (Get-NetTCPConnection).OwningProcess | Where-Object -Property State -eq Running | Format-Table ProcessId, Name, Caption, StartMode, State, Status, PathName
```


#### Можно вывести только сетевые подключения, которые инициированы определенным процессом.:

```bash
$TrackProcessName = “*chrome*”
$EstablishedConnections = Get-NetTCPConnection -State Established |Select-Object -Property LocalAddress, LocalPort,@{name='RemoteHostName';expression={(Resolve-DnsName $_.RemoteAddress).NameHost}},RemoteAddress, RemotePort, State,@{name='ProcessName';expression={(Get-Process -Id $_.OwningProcess). Path}}, OffloadState,CreationTime
Foreach ($Connection in $EstablishedConnections)
{
If ($Connection.ProcessName -like $TrackProcessName)
{
$Connection|ft
}
```
}
