# MS_SQL.

## Хранимые процедуры

#### Справочные сведения обо всех расширенных хранимых процедурах
```sql
USE master;  
GO  
EXEC sp_helpextendedproc;  
GO
```

#### Справочные сведения об одной расширенной хранимой процедуре: xp_cmdshell
```sql
USE master;  
GO  
EXEC sp_helpextendedproc xp_cmdshell;  
GO
```

#### Добавление на SQL Server расширенной хранимой процедуры: xp_StoreOnce

```sql
sp_addextendedproc 'xp_StoreOnce','C:\Program Files\HPE\StoreOnce\isvsupport\sql\bin\XP_HPStoreOnceForMSSQL.dll'
```

#### Удаление с SQL Server расширенной хранимой процедуры: 'xp_StoreOnce

```sql
sp_dropextendedproc 'xp_StoreOnce';
```

## SQL CMD.
#### В командной строке CMD от администратора
#### Должны получить ответ: 1>

```sql
sqlcmd
```


#### Версия SQL сервера
#### go - команда на выполнение sql запроса
```sql
select @@version
go
```


```sql
select Name
from sys.databases
go
```

```sql
sqlcmd -S localhost -E -i "C:\SQLCMD\Query.txt"

sqlcmd -S localhost -E -i "C:\SQLCMD\Query.txt" -o "C:\Users\yaroslav.klochkov\Desktop\SQLCMD\rezult.txt"

sqlcmd -S localhost -E -i "C:\SQLCMD\Query.txt" -v TableName=sys.databases ColumnName=Name -o "C:\Users\yaroslav.klochkov\Desktop\SQLCMD\rezult.txt"
```

# T_SQL запросы.

#### Проверить модель восстановления:
```sql
SELECT name, recovery_model_desc 
FROM sys.databases 
WHERE name = 'TEST_BD';
```

#### Для возможности резервного копирования журнала нужно переключить на полную модель:
```sql
ALTER DATABASE [TEST_BD] 
SET RECOVERY FULL;
```

#### Выборка в определенном диапазоне дат
```sql
SELECT * FROM orders WHERE customer_id = 321 AND order_date BETWEEN '2025-01-01' AND '20245-12-31';
```

#### Проверить группу доступности AO
```sql
SELECT name
FROM sys.availability_groups;
```

#### Проверить является ли сервер частью группы доступности AO
```sql
SELECT replica_server_name
FROM sys.availability_replicas;
```
#### Информация о настройках реплики
```sql
SELECT replica_id, replica_server_name, availability_mode_desc
FROM sys.availability_replicas;
```

#### Информация о текущем состоянии реплики
```sql
SELECT replica_id, role_desc, connected_state_desc
FROM sys.dm_hadr_availability_replica_states;
```

#### Информация о состоянии баз данных в репликах
```sql
SELECT replica_id, database_id, synchronization_state_desc
FROM sys.dm_hadr_database_replica_states;
```

#### Чтобы получить полную информацию о репликах и их состоянии
```sql
SELECT 
    ar.replica_server_name,
    ars.role_desc,
    ars.connected_state_desc,
    ars.synchronization_health_desc
FROM 
    sys.dm_hadr_availability_replica_states AS ars
JOIN 
    sys.availability_replicas AS ar
ON 
    ars.replica_id = ar.replica_id;
```

#### Найти replica_id текущего сервера
```sql
SELECT replica_id, replica_server_name
FROM sys.availability_replicas
WHERE replica_server_name = UPPER(@@SERVERNAME);
```

#### Получить информацию о роли и состоянии синхронизации AO
```sql
SELECT 
    ag.name AS availability_group_name,
    ar.replica_server_name,
    ars.role_desc,
    ars.synchronization_health_desc
FROM 
    sys.dm_hadr_availability_replica_states AS ars
JOIN 
    sys.availability_replicas AS ar
ON 
    ars.replica_id = ar.replica_id
JOIN 
    sys.availability_groups AS ag
ON 
    ars.group_id = ag.group_id;
```

### Переименовать SQL сервер
#### Текущее имя сервера
```sql
SELECT @@SERVERNAME AS CurrentServerName;
```

#### Удалите текущее имя сервера
```sql
EXEC sp_dropserver 'TESTSQL1';
или
EXEC sp_dropserver 'TESTSQL1\InstanceName';
```

#### Добавьте новое имя сервера
```sql
EXEC sp_addserver 'SQL-TST-DB1', 'local';
или
EXEC sp_addserver 'SQL-TST-DB1\InstanceName', 'local';
```


#### Определить статус ноды в SQL-AlwaysOn

```sql
SELECT a.role_desc
FROM sys.dm_hadr_availability_replica_states AS a
JOIN sys.availability_replicas AS b
ON b.replica_id = a.replica_id
WHERE UPPER(b.replica_server_name) = UPPER(@@SERVERNAME)
```

#### max/min server memory — это минимальное и максимальное количество оперативной памяти, потребляемое экземпляром Microsoft SQL Server
#### Посмотреть установленное значение можно следующим SQL запросом:

```sql
SELECT c.value, c.value_in_use
FROM sys.configurations c WHERE c.[name] = 'max server memory (MB)'
GO
```
#### Установить значение max/min server memory

```sql
sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
sp_configure 'max server memory', 4096;
GO
RECONFIGURE;
GO
```

#### Установить значение: max degree of parallelism

```sql
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE WITH OVERRIDE;
GO
EXEC sp_configure 'max degree of parallelism', 16;
GO
RECONFIGURE WITH OVERRIDE;
GO
```


#### Создать новую БД 

```sql
create database TestDB;
```

#### Использоватиь нужную БД. 

```sql
use TestDB;
```

```sql
use TestDB
go
create table mtable1
	(
	name char (1000)
	)
go
insert
into mtable1 (name)
values ('testdfghfghdfhgdghdgfhdsgfhdf')
```
## SQL Always On AG 

### Как сделать базу данных SQL Server доступной в состоянии RESTORING без восстановления резервных копий
#### Если база данных застряла в состоянии восстановления и у вас нет дополнительных резервных копий для восстановления, вы можете восстановить базу данных с помощью следующей команды:
Сделать базу данных SQL Server доступной в состоянии RESTORING - [ссылка](https://blog.it-kb.ru/2021/10/14/sql-server-database-stuck-in-restoring-state/#more-23442).


```sql
RESTORE DATABASE [TestDB] WITH RECOVERY
```

#### При добавление в Always On Listener - возникает ошибка:

_**Create Failed for Availability Group Listener" SQL Error: 19471**_  

![](https://github.com/klochkovyaroslav/Manuals/blob/main/availabilitygroups_createlistenererror.png?raw=true)

Как исправить - [ссылка](https://www.youtube.com/watch?v=5-roDqL9Sms).
```
1. Добавить в DNS A-запись для будущего Listener-a (например "sql-Listener1")
2. Создать в AD объект "Компьютер" с именем таким какое имя будет у "Listener"-a (например "sql-Listener1")
3. В свойствах получившегося объекта "Компьютер" в вкладке "Security" добавить объект "Компьютер" и именем Failover Cluster-a и дать полный доступ
```

## Как удалить лог ошибок SQL Server. Где хранится ErrorLog MS SQL

Основной файл ERRORLOG нельзя так просто удалить т.к. его использует SQL Server, но его можно "отключить" от системы с помощью следующей команды.  

```sql
EXEC sp_cycle_errorlog;
GO
```
После этого логи циклически сдвинутся и файлы errorlog.N можно будет удалить.  

> При каждом запуске SQL Server текущий журнал ошибок переименовывается errorlog.1 в ; errorlog.1 становится, errorlog.2 становится errorlog.2 errorlog.3 и т. д. sp_cycle_errorlog позволяет циклировать файлы журнала ошибок без остановки и запуска сервера.



## Ошибка в PowerShell
```
Invoke-Sqlcmd : A parameter cannot be found that matches parameter name 'TrustServerCertificate'.
At line:5 char:118
+ ... Query -SuppressProviderContextWarning -TrustServerCertificate -Verbos ...
+                                           ~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (:) [Invoke-Sqlcmd], ParentContainsErrorRecordException
    + FullyQualifiedErrorId : NamedParameterNotFound,Microsoft.SqlServer.Management.PowerShell.GetScriptCommand
```
#### Проверка версии модуля
```powershell
Get-Module -ListAvailable
Get-Module -ListAvailable SqlServer, SQLPS
```
> Если используется модуль SQLPS, рекомендуется перейти на SqlServer, так как SQLPS устарел.

#### Установить модуль SqlServer в PS
```powershell
Install-Module -Name SqlServer -Force -AllowClobber
```

#### Проверить текущую версию модуля SqlServer
```powershell
Get-Module -ListAvailable SqlServer
```

#### Обновить модуль SqlServer
```powershell
Update-Module -Name SqlServer -Force
```

