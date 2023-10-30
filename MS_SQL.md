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

## T_SQL запросы.

#### Создать новую БД 

```bash
create database TestDB;
```

#### Использоватиь нужную БД. 

```bash
use TestDB;
```
