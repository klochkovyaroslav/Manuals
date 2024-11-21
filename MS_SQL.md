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

## T_SQL запросы.

#### Определить статус ноды в SQL-AlwaysOn

```sql
SELECT a.role_desc
FROM sys.dm_hadr_availability_replica_states AS a
JOIN sys.availability_replicas AS b
ON b.replica_id = a.replica_id
WHERE b.replica_server_name = @@ServerName
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
