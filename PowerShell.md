# PowerShell - Примеры
## Автозавершение команд
> Tab  
> CTRL-Пробел - выведет список  

## PSScriptAnalyzer — это статическая проверка кода для модулей и скриптов PowerShell

```bash
Install-Module -Name PSScriptAnalyzer
Invoke-ScriptAnalyzer C:\ps_scripts\sql_restore_check_db\Auto_Restore_DB_Check_DB.ps1
```

## Оптимизация времени выполнения команд PowerShell

#### Запрос списка пользователей из AD с помощью команды Get-ADUser

```bash
Get-ADUser -Filter {Enabled -eq "true"} -работает быстрее
Get-ADUser | Where-Object {$_.enabled -eq $true} -работает медленее

Время выполнения первой команды намного меньше, т.к. все выборка выполняется на стороне сервера. Кроме, того ваш компьютер получит меньший объем данных, т.к. сервер вернет только запрошенные объекты.
Вторая команда возвращает с контроллера домена все объекты и только после этого фильтрует результат.

Аналогичный результат можно увидеть при запросе списка событий из журналов Event Viewer с помощью Get-WinEvent.
Сформируем список ошибок из Event Viewer за 10 дней с помощью Where-Object и FilterHashtable. Сравним скорость выполнения этих двух команд PowerShell с помощью Measure-Command:

$StartDate = (Get-Date).AddDays(-10)

Проверим скорость выполнения команды при использовании Where-Object:
(Measure-Command {Get-WinEvent System | Where-Object {($_.LevelDisplayName -eq "Error") -and ($_.TimeCreated -ge $StartDate )}}).TotalMilliseconds

Теперь воспользуемся возможность фильтрации на стороне службы Event Viewer с помощью параметра FilterHashtable:
(Measure-Command {Get-WinEvent -FilterHashtable @{LogName = 'System'; Level =3; StartTime=$StartDate }}).TotalMilliseconds

```

## Разное

Простая проверка if  
Проверку на несоответствие значению $null обычно выполняют с помощью простой инструкции if() без сравнения.  

```bash
if ( $value )
{
    Do-Something
}
```

Если значение равно $null, то результатом будет $false. Это выглядит просто, но только на первый взгляд. Я понимаю эту строку кода как:  
Если $value имеет значение.  
Но это еще не все. На самом деле строка значит следующее:  
Если $value не имеет значение $null, 0 или $false либо не равен пустой строке или пустому массиву.  
Ниже приведен более полный пример этой инструкции.  

```bash
if ( $null -ne $value -and
        $value -ne 0 -and
        $value -ne '' -and
        ($value -isnot [array] -or $value.Length -ne 0) -and
        $value -ne $false )
{
    Do-Something
}
```
Вполне допустимо использовать базовую проверку if, помня не только о значении переменной, но и о том, что другие значения считаются $false.  


### ОС - Windows

#### Для регистрации текущего сеанса PowerShell используется командлет Start-Transcript.

```bash
Start-Transcript -Append C:\Scripts\PS-Script-Log.txt
    #код вашего скрипта PowerShell
Start-Sleep -Seconds 10
Stop-Transcript
```
### Дата - Время в Powershell

[PowerShell — Дата и Время](https://sawfriendship.wordpress.com/2017/07/17/powershell-date-and-time)

### Измерить время выполнения скрипта PowerShell
[PowerShell — Дата и Время](https://vmblog.ru/vremya-vypolneniya-skripta-powershell)

#### PowerShell. Время запуска и окончания команды содержится в атрибутах StartExecutionTime и EndExecutionTime

```bash
Get-History | select StartExecutionTime,EndExecutionTime,CommandLine
```

#### Получить время выполнения последней команды:

```bash
$lastcommand=Get-History | select -Last 1 -Property *
$lastcommand.EndExecutionTime - $lastcommand.StartExecutionTime
```

### Вычисляем скорость выполнения PowerShell кода

#### Net класс StopWatch работает как секундомер:

```bash
$watch = [System.Diagnostics.Stopwatch]::StartNew()
$watch.Start() #Запуск таймера
    #код вашего скрипта PowerShell
$watch.Stop() #Остановка таймера
Write-Host $watch.Elapsed #Время выполнения скрипта

'Время выполнения: '+ (($watch.Elapsed).ToString()).Split('.')[0] | out-file -Filepath $rezult_FO -append
#Write-Host 'Время выполнения: '(($watch.Elapsed).ToString()).Split('.')[0] | out-file -Filepath $rezult_FO -append
$watch.IsRunning #запущен ли сейчас секундомер
```

#### Подсчет разницы в [datetime]:

```bash
$time_po_start = (Get-Date)
    Start-Sleep -Seconds 10
    #код вашего скрипта PowerShell
$time_po_stop = (Get-Date)
$time_po_total = $time_po_stop-$time_po_start

$TotalTime
$TotalTime.ToString()

$time_po_total=($time_po_stop-$time_po_start).ToString().Split('.')[0]
'Время выполнения: '+ $time_po_total | out-file -Filepath $rezult_FO -append

'{0:mm} min {0:ss} sec' -f $time_po_total
```
#### Для получения суммарного времени, затраченного на выполнение всех команд в блоке кода PowerShell:
#### Командлет "Measure-Command"

```bash
(Measure-Command {
#код вашего скрипта PowerShell
}).TotalMilliseconds
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
