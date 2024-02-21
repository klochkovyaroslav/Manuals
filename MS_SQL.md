# MS_SQL.

## Хранимые процедуры

#### Справочные сведения обо всех расширенных хранимых процедурах
```bash
USE master;  
GO  
EXEC sp_helpextendedproc;  
GO
```

#### Справочные сведения об одной расширенной хранимой процедуре: xp_cmdshell
```bash
USE master;  
GO  
EXEC sp_helpextendedproc xp_cmdshell;  
GO
```

#### Добавление на SQL Server расширенной хранимой процедуры: xp_StoreOnce

```bash
sp_addextendedproc 'xp_StoreOnce','C:\Program Files\HPE\StoreOnce\isvsupport\sql\bin\XP_HPStoreOnceForMSSQL.dll'
```

#### Удаление с SQL Server расширенной хранимой процедуры: 'xp_StoreOnce

```bash
sp_dropextendedproc 'xp_StoreOnce';
```

## SQL CMD.
#### В командной строке CMD от администратора
#### Должны получить ответ: 1>

```bash
sqlcmd
```


#### Версия SQL сервера
#### go - команда на выполнение sql запроса
```bash
select @@version
go
```


```bash
select Name
from sys.databases
go
```

```bash
sqlcmd -S localhost -E -i "C:\SQLCMD\Query.txt"

sqlcmd -S localhost -E -i "C:\SQLCMD\Query.txt" -o "C:\Users\yaroslav.klochkov\Desktop\SQLCMD\rezult.txt"

sqlcmd -S localhost -E -i "C:\SQLCMD\Query.txt" -v TableName=sys.databases ColumnName=Name -o "C:\Users\yaroslav.klochkov\Desktop\SQLCMD\rezult.txt"
```

## T_SQL запросы.

#### max/min server memory — это минимальное и максимальное количество оперативной памяти, потребляемое экземпляром Microsoft SQL Server
#### Посмотреть установленное значение можно следующим SQL запросом:

```bash
SELECT c.value, c.value_in_use
FROM sys.configurations c WHERE c.[name] = 'max server memory (MB)'
GO
```
#### Установить значение max/min server memory

```bash
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

```bash
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

```bash
create database TestDB;
```

#### Использоватиь нужную БД. 

```bash
use TestDB;
```

```bash
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


```bash
RESTORE DATABASE [TestDB] WITH RECOVERY
```

#### При добавление в Always On Listener - возникает ошибка:

_**Create Failed for Availability Group Listener" SQL Error: 19471**_  

![](//placehold.it/200x100)

Как исправить - [ссылка](https://www.youtube.com/watch?v=5-roDqL9Sms).
```
1. Добавить в DNS A-запись для будущего Listener-a (например "sql-Listener1")
2. Создать в AD объект "Компьютер" с именем таким какое имя будет у "Listener"-a (например "sql-Listener1")
3. В свойствах получившегося объекта "Компьютер" в вкладке "Security" добавить объект "Компьютер" и именем Failover Cluster-a и дать полный доступ
```

