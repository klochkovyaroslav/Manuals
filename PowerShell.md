# PowerShell - Примеры

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
