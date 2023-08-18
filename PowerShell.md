# PowerShell - Примеры

## Разное

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

#### Применить стандартный снимок на ВМ:
```bash
Restore-VMCheckpoint -VMName testVM01 -Name Production_Checkpoint -Confirm:$false
```
