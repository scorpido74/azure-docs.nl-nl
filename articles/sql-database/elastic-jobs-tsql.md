---
title: Elastische databasetaken maken en beheren met Transact-SQL (T-SQL)
description: Scripts uitvoeren in veel databases met Elastic Database Job-agent met Behulp van Transact-SQL (T-SQL).
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
ms.date: 02/07/2020
ms.openlocfilehash: c228f3d6591cd72845101c00188f3fc4a55be644
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087361"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Transact-SQL (T-SQL) gebruiken om Elastische databasetaken te maken en te beheren

In dit artikel vindt u veel voorbeeldscenario's om aan de slag te gaan met Elastic Jobs met T-SQL.

De voorbeelden maken gebruik van de [opgeslagen procedures](#job-stored-procedures) en [weergaven die](#job-views) beschikbaar zijn in de [*taakdatabase*](sql-database-job-automation-overview.md#job-database).

Transact-SQL (T-SQL) wordt gebruikt voor het maken, configureren, uitvoeren en beheren van taken. Het maken van de elastic job-agent wordt niet ondersteund in T-SQL, dus u moet eerst een *Elastic Job-agent* maken met behulp van de portal of [PowerShell.](elastic-jobs-powershell.md#create-the-elastic-job-agent)


## <a name="create-a-credential-for-job-execution"></a>Een referentie maken voor taakuitvoering

De referentie wordt gebruikt om verbinding te maken met uw doeldatabases voor het uitvoeren van scripts. De referentie heeft de juiste machtigingen nodig, op de databases die door de doelgroep zijn opgegeven, om het script met succes uit te voeren. Bij het gebruik van een server- en/of groeplid van de groep wordt ten zeerste aangeraden om een hoofdreferentie te maken voor gebruik om de referentie te vernieuwen voordat de server en/of de groep wordt uitgebreid op het moment van uitvoering van de taak. De databasescoped referentie wordt gemaakt in de job agent database. Dezelfde referentie moet worden gebruikt om *een aanmelding* te maken en *een gebruiker te maken vanuit Aanmelding om de inlogdatabasemachtigingen* voor de doeldatabases toe te kennen.


```sql
--Connect to the job database specified when creating the job agent

-- Create a db master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create a database scoped credential.  
CREATE DATABASE SCOPED CREDENTIAL myjobcred WITH IDENTITY = 'jobcred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO

-- Create a database scoped credential for the master database of server1.
CREATE DATABASE SCOPED CREDENTIAL mymastercred WITH IDENTITY = 'mastercred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO
```

## <a name="create-a-target-group-servers"></a>Een doelgroep maken (servers)

In het volgende voorbeeld ziet u hoe u een taak uitvoert tegen alle databases in een server.  
Maak verbinding met de [*taakdatabase*](sql-database-job-automation-overview.md#job-database) en voer de volgende opdracht uit:


```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net'

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```


## <a name="exclude-an-individual-database"></a>Een afzonderlijke database uitsluiten

In het volgende voorbeeld ziet u hoe u een taak uitvoert ten opzichte van alle databases in een SQL Database-server, met uitzondering van de database met de naam *MappingDB.*  
Maak verbinding met de [*taakdatabase*](sql-database-job-automation-overview.md#job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name='server2.database.windows.net'
GO

--Exclude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name =N'MappingDB'
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```


## <a name="create-a-target-group-pools"></a>Een doelgroep maken (groepen)

In het volgende voorbeeld ziet u hoe u alle databases in een of meer elastische groepen targeten.  
Maak verbinding met de [*taakdatabase*](sql-database-job-automation-overview.md#job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup'

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net',
@elastic_pool_name='ElasticPool-1'

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```


## <a name="deploy-new-schema-to-many-databases"></a>Nieuw schema implementeren in veel databases

In het volgende voorbeeld ziet u hoe u nieuw schema implementeert in alle databases.  
Maak verbinding met de [*taakdatabase*](sql-database-job-automation-overview.md#job-database) en voer de volgende opdracht uit:


```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name='CreateTableTest', @description='Create Table Test'

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name='CreateTableTest',
@command=N'IF NOT EXISTS (SELECT * FROM sys.tables 
            WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name='myjobcred',
@target_group_name='PoolGroup'
```


## <a name="data-collection-using-built-in-parameters"></a>Gegevensverzameling met ingebouwde parameters

In veel scenario's voor het verzamelen van gegevens kan het handig zijn om een aantal van deze scriptvariabelen op te nemen om de resultaten van de taak na het verwerken te helpen.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Als u bijvoorbeeld alle resultaten van dezelfde taakuitvoering samen wilt groeperen, gebruikt u de *$(job_execution_id)* zoals weergegeven in de volgende opdracht:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Databaseprestaties bewaken

In het volgende voorbeeld wordt een nieuwe taak gemaakt om prestatiegegevens uit meerdere databases te verzamelen.

Standaard maakt de taakagent de uitvoertabel om geretourneerde resultaten op te slaan. Daarom moet de databaseprincipal die aan de uitvoerreferentie is `CREATE TABLE` gekoppeld, ten `ALTER` `SELECT`minste `INSERT` `DELETE` de volgende machtigingen hebben: `SELECT` in de database, , , op de uitvoertabel of het schema en op de catalogusweergave [sys.indexes.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql)

Als u de tabel van tevoren handmatig wilt maken, moet deze de volgende eigenschappen hebben:
1. Kolommen met de juiste naam en gegevenstypen voor de resultaatset.
2. Extra kolom voor internal_execution_id met het gegevenstype uniek id.
3. Een niet-geclusterde index met de naam `IX_<TableName>_Internal_Execution_ID` in de kolom internal_execution_id.
4. Alle hierboven vermelde machtigingen, `CREATE TABLE` behalve toestemming voor de database.

Maak verbinding met de [*taakdatabase*](sql-database-job-automation-overview.md#job-database) en voer de volgende opdrachten uit:

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsJob',
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name='myjobcred',
@target_group_name='PoolGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='<resultsdb>',
@output_table_name='<resutlstable>'
Create a job to monitor pool performance
--Connect to the job database specified when creating the job agent

-- Add a target group containing master database
EXEC jobs.sp_add_target_group 'MasterGroup'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name='MasterGroup',
@target_type='SqlDatabase',
@server_name='server1.database.windows.net',
@database_name='master'

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name='ResultsPoolsJob',
@description='Demo: Collection Performance data from all pools',
@schedule_interval_type='Minutes',
@schedule_interval_count=15

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name='myjobcred',
@target_group_name='MasterGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='resultsdb',
@output_table_name='resutlstable'
```


## <a name="view-job-definitions"></a>Taakdefinities weergeven

In het volgende voorbeeld ziet u hoe u de huidige taakdefinities weergeven.  
Maak verbinding met de [*taakdatabase*](sql-database-job-automation-overview.md#job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j 
  ON j.job_id = js.job_id AND j.job_version = js.job_version

-- View the steps of all versions of all jobs
select * from jobs.jobsteps
```


## <a name="begin-ad-hoc-execution-of-a-job"></a>Ad hoc uitvoering van een taak beginnen

In het volgende voorbeeld ziet u hoe u een taak onmiddellijk starten.  
Maak verbinding met de [*taakdatabase*](sql-database-job-automation-overview.md#job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest'

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output
select @je

select * from jobs.job_executions where job_execution_id = @je

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1
```


## <a name="schedule-execution-of-a-job"></a>Uitvoering van een taak plannen

In het volgende voorbeeld ziet u hoe u een taak inplant voor toekomstige uitvoering.  
Maak verbinding met de [*taakdatabase*](sql-database-job-automation-overview.md#job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Status van taakuitvoering controleren

In het volgende voorbeeld ziet u hoe u de details van de uitvoeringsstatus voor alle taken weergeven.  
Maak verbinding met de [*taakdatabase*](sql-database-job-automation-overview.md#job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named 'ResultsPoolJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named 'ResultsPoolsJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions 
WHERE is_active = 1
ORDER BY start_time DESC
```


## <a name="cancel-a-job"></a>Een taak annuleren

In het volgende voorbeeld ziet u hoe u een taak annuleert.  
Maak verbinding met de [*taakdatabase*](sql-database-job-automation-overview.md#job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions 
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef'
```


## <a name="delete-old-job-history"></a>Oude taakgeschiedenis verwijderen

In het volgende voorbeeld ziet u hoe u de taakgeschiedenis voor een bepaalde datum verwijderen.  
Maak verbinding met de [*taakdatabase*](sql-database-job-automation-overview.md#job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Een taak en al zijn taakgeschiedenis verwijderen

In het volgende voorbeeld ziet u hoe u een taak en alle bijbehorende taakgeschiedenis verwijdert.  
Maak verbinding met de [*taakdatabase*](sql-database-job-automation-overview.md#job-database) en voer de volgende opdracht uit:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Taak opgeslagen procedures

De volgende opgeslagen procedures bevinden zich in de [banendatabase](sql-database-job-automation-overview.md#job-database).



|Opgeslagen procedure  |Beschrijving  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Voegt een nieuwe taak toe.    |
|[sp_update_job](#sp_update_job)    |      Werkt een bestaande taak bij.   |
|[sp_delete_job](#sp_delete_job)     |      Hiermee verwijdert u een bestaande taak.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    Hiermee voegt u een stap toe aan een taak.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Werkt een taakstap bij.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Hiermee verwijdert u een taakstap.    |
|[sp_start_job](#sp_start_job)    |  Begint met het uitvoeren van een taak.       |
|[sp_stop_job](#sp_stop_job)     |     Stopt een taakuitvoering.   |
|[sp_add_target_group](#sp_add_target_group)    |     Voegt een doelgroep toe.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    Hiermee verwijdert u een doelgroep.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    Hiermee voegt u een database of groep databases toe aan een doelgroep.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     Hiermee verwijdert u een doelgroeplid uit een doelgroep.    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    Hiermee verwijdert u de geschiedenisrecords voor een taak.     |





### <a name="sp_add_job"></a><a name="sp_add_job"></a>sp_add_job

Voegt een nieuwe taak toe. 
  
#### <a name="syntax"></a>Syntaxis  
  

```sql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
    [ , [ @job_id = ] job_id OUTPUT ]
```

  
#### <a name="arguments"></a>Argumenten  

[ ** \@job_name =** ] 'job_name'  
De naam van de taak. De naam moet uniek zijn en mag het percentage (%) niet bevatten Teken. job_name is nvarchar(128), zonder standaard.

[ ** \@beschrijving =** ] "beschrijving"  
De beschrijving van de baan. description is nvarchar(512), met een standaard van NULL. Als de beschrijving wordt weggelaten, wordt een lege tekenreeks gebruikt.

[ ** \@ingeschakeld =** ] ingeschakeld  
Of het schema van de taak is ingeschakeld. Ingeschakeld is bit, met een standaard van 0 (uitgeschakeld). Als 0, is de taak niet ingeschakeld en wordt deze niet uitgevoerd volgens de planning; het kan echter handmatig worden uitgevoerd. Als 1, zal de taak volgens zijn planning lopen, en kan ook handmatig worden uitgevoerd.

[ ** \@schedule_interval_type =**] schedule_interval_type  
Waarde geeft aan wanneer de taak moet worden uitgevoerd. schedule_interval_type is nvarchar(50), met een standaard van Eenmaal, en kan een van de volgende waarden:
- 'Eenmaal',
- 'Notulen',
- 'Uren',
- 'Dagen',
- 'Weken',
- 'Maanden'

[ ** \@schedule_interval_count =** ] schedule_interval_count  
Aantal schedule_interval_count perioden die moeten optreden tussen elke uitvoering van de taak. schedule_interval_count is int, met een standaard van 1. De waarde moet groter zijn dan of gelijk zijn aan 1.

[ ** \@schedule_start_time =** ] schedule_start_time  
Datum waarop de uitvoering van de taak kan beginnen. schedule_start_time is DATETIME2, met de standaardinstelling 0001-01 00:00:00.00000000.

[ ** \@schedule_end_time =** ] schedule_end_time  
Datum waarop de uitvoering van taken kan stoppen. schedule_end_time is DATETIME2, met de standaardinstelling 9999-12-31 11:59:59.0000000. 

[ ** \@job_id =** ] job_id OUTPUT  
Het taakidentificatienummer dat aan de taak is toegewezen als deze is gemaakt. job_id is een uitvoervariabele van type uniqueidentifier.

#### <a name="return-code-values"></a>Waarden voor retourcode

0 (succes) of 1 (mislukking)

#### <a name="remarks"></a>Opmerkingen
sp_add_job moet worden uitgevoerd vanuit de database met taakagents die is opgegeven bij het maken van de taakagent.
Nadat sp_add_job is uitgevoerd om een taak toe te voegen, sp_add_jobstep worden gebruikt om stappen toe te voegen die de activiteiten voor de taak uitvoeren. Het oorspronkelijke versienummer van de taak is 0, dat wordt verhoogd naar 1 wanneer de eerste stap wordt toegevoegd.

#### <a name="permissions"></a>Machtigingen
Standaard kunnen leden van de vaste serverrol van sysadmin deze opgeslagen procedure uitvoeren. Ze beperken een gebruiker om alleen taken te kunnen controleren, u de gebruiker de opdracht geven om deel uit te maken van de volgende databaserol in de database met taakagents die is opgegeven bij het maken van de taakagent:

- jobs_reader

Zie de sectie Machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken van taken te bewerken die eigendom zijn van andere gebruikers.

### <a name="sp_update_job"></a><a name="sp_update_job"></a>sp_update_job

Werkt een bestaande taak bij.

#### <a name="syntax"></a>Syntaxis

```sql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
    [ , [ @new_name = ] 'new_name' ]
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
```

#### <a name="arguments"></a>Argumenten
[ ** \@job_name =** ] 'job_name'  
De naam van de taak die moet worden bijgewerkt. job_name is nvarchar(128).

[ ** \@new_name =** ] 'new_name'  
De nieuwe naam van de baan. new_name is nvarchar(128).

[ ** \@beschrijving =** ] "beschrijving"  
De beschrijving van de baan. description is nvarchar(512).

[ ** \@ingeschakeld =** ] ingeschakeld  
Hiermee geeft u op of het schema van de taak is ingeschakeld (1) of niet is ingeschakeld (0). Ingeschakeld is bit.

** \@schedule_interval_type** schedule_interval_type  
Waarde geeft aan wanneer de taak moet worden uitgevoerd. schedule_interval_type is nvarchar(50) en kan een van de volgende waarden zijn:

- 'Eenmaal',
- 'Notulen',
- 'Uren',
- 'Dagen',
- 'Weken',
- 'Maanden'

schedule_interval_count= ] schedule_interval_count ** \@**  
Aantal schedule_interval_count perioden die moeten optreden tussen elke uitvoering van de taak. schedule_interval_count is int, met een standaard van 1. De waarde moet groter zijn dan of gelijk zijn aan 1.

schedule_start_time= ] schedule_start_time ** \@**  
Datum waarop de uitvoering van de taak kan beginnen. schedule_start_time is DATETIME2, met de standaardinstelling 0001-01 00:00:00.00000000.

[ ** \@schedule_end_time=** ] schedule_end_time  
Datum waarop de uitvoering van taken kan stoppen. schedule_end_time is DATETIME2, met de standaardinstelling 9999-12-31 11:59:59.0000000. 

#### <a name="return-code-values"></a>Waarden voor retourcode
0 (succes) of 1 (mislukking)

#### <a name="remarks"></a>Opmerkingen
Nadat sp_add_job is uitgevoerd om een taak toe te voegen, sp_add_jobstep worden gebruikt om stappen toe te voegen die de activiteiten voor de taak uitvoeren. Het oorspronkelijke versienummer van de taak is 0, dat wordt verhoogd naar 1 wanneer de eerste stap wordt toegevoegd.

#### <a name="permissions"></a>Machtigingen
Standaard kunnen leden van de vaste serverrol van sysadmin deze opgeslagen procedure uitvoeren. Ze beperken een gebruiker om alleen taken te kunnen controleren, u de gebruiker de opdracht geven om deel uit te maken van de volgende databaserol in de database met taakagents die is opgegeven bij het maken van de taakagent:
- jobs_reader

Zie de sectie Machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken van taken te bewerken die eigendom zijn van andere gebruikers.



### <a name="sp_delete_job"></a><a name="sp_delete_job"></a>sp_delete_job

Hiermee verwijdert u een bestaande taak.

#### <a name="syntax"></a>Syntaxis

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumenten
[ ** \@job_name =** ] 'job_name'  
De naam van de taak die moet worden verwijderd. job_name is nvarchar(128).

[ ** \@kracht =** ] kracht  
Hiermee geeft u op of de taak uitvoeringen moet uitvoeren en alle lopende uitvoeringen moet annuleren (1) of moet mislukken als er taakuitvoeringen worden uitgevoerd (0). kracht is een beetje.

#### <a name="return-code-values"></a>Waarden voor retourcode
0 (succes) of 1 (mislukking)

#### <a name="remarks"></a>Opmerkingen
Taakgeschiedenis wordt automatisch verwijderd wanneer een taak wordt verwijderd.

#### <a name="permissions"></a>Machtigingen
Standaard kunnen leden van de vaste serverrol van sysadmin deze opgeslagen procedure uitvoeren. Ze beperken een gebruiker om alleen taken te kunnen controleren, u de gebruiker de opdracht geven om deel uit te maken van de volgende databaserol in de database met taakagents die is opgegeven bij het maken van de taakagent:
- jobs_reader

Zie de sectie Machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken van taken te bewerken die eigendom zijn van andere gebruikers.



### <a name="sp_add_jobstep"></a><a name="sp_add_jobstep"></a>sp_add_jobstep

Hiermee voegt u een stap toe aan een taak.

#### <a name="syntax"></a>Syntaxis


```sql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] step_name ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]   
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumenten

[ ** \@job_name =** ] 'job_name'  
De naam van de taak waaraan u de stap moet toevoegen. job_name is nvarchar(128).

[ ** \@step_id =** ] step_id  
Het sequentie-identificatienummer voor de taakstap. Stapidentificatienummers beginnen bij 1 en nemen zonder hiaten toe. Als een bestaande stap deze id al heeft, worden deze stap en alle volgende stappen verhoogd zodat deze nieuwe stap in de reeks kan worden ingevoegd. Als dit niet is opgegeven, wordt de step_id automatisch toegewezen aan de laatste in de reeks stappen. step_id is een int.

[ ** \@step_name =** ] step_name  
De naam van de stap. Moet worden opgegeven, met uitzondering van de eerste stap van een taak die (voor het gemak) een standaardnaam van 'JobStep' heeft. step_name is nvarchar(128).

[ ** \@command_type =** ] "command_type"  
Het type opdracht dat wordt uitgevoerd door deze taakstap. command_type is nvarchar(50), met een standaardwaarde van TSql, wat betekent dat de waarde van de @command_type parameter een T-SQL-script is.

Indien opgegeven, moet de waarde TSql zijn.

[ ** \@command_source =** ] 'command_source'  
Het type locatie waar de opdracht wordt opgeslagen. command_source is nvarchar(50), met een standaardwaarde van Inline, wat betekent dat de waarde van de @command_source parameter de letterlijke tekst van de opdracht is.

Indien opgegeven, moet de waarde Inline zijn.

[ ** \@opdracht =** ] 'commando'  
De opdracht moet geldig T-SQL-script zijn en vervolgens worden uitgevoerd door deze taakstap. opdracht is nvarchar(max), met een standaard van NULL.

[ ** \@credential_name =** ] 'credential_name'  
De naam van de database scoped credential stored in this job control database that is used to connect to each of the target databases within the target group when this step is executed. credential_name is nvarchar(128).

[ ** \@target_group_name =** ] 'target-group_name'  
De naam van de doelgroep die de doeldatabases bevat waarop de taakstap wordt uitgevoerd. target_group_name is nvarchar(128).

[ ** \@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
De vertraging vóór de eerste poging opnieuw proberen, als de taakstap op de aanvankelijke uitvoeringspoging ontbreekt. initial_retry_interval_seconds is int, met standaardwaarde van 1.

[ ** \@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
De maximale vertraging tussen pogingen om opnieuw te proberen. Als de vertraging tussen nieuwe pogingen groter zou worden dan deze waarde, wordt deze beperkt tot deze waarde. maximum_retry_interval_seconds is int, met standaardwaarde van 120.

[ ** \@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
De multiplier die moet worden toegepast op de vertraging opnieuw proberen als pogingen tot uitvoering van meerdere taken mislukken. Bijvoorbeeld, als de eerste poging had een vertraging van 5 seconden en de backoff multiplier is 2.0, dan is de tweede opnieuw proberen zal een vertraging van 10 seconden en de derde poging zal een vertraging van 20 seconden hebben. retry_interval_backoff_multiplier is reëel, met een standaardwaarde van 2,0.

[ ** \@retry_attempts =** ] retry_attempts  
Het aantal keren dat de uitvoering opnieuw moet worden uitgevoerd als de eerste poging mislukt. Als de waarde van de retry_attempts bijvoorbeeld 10 is, zijn er 1 eerste poging en 10 pogingen om opnieuw te proberen, waardoor in totaal 11 pogingen worden gegeven. Als de laatste poging opnieuw proberen mislukt, wordt de taakuitvoering beëindigd met een levenscyclus van Mislukt. retry_attempts is int, met standaardwaarde van 10.

[ ** \@step_timeout_seconds =** ] step_timeout_seconds  
De maximale hoeveelheid tijd die is toegestaan voor de uitvoering van de stap. Als deze tijd wordt overschreden, wordt de taakuitvoering beëindigd met een levenscyclus van TimedOut. step_timeout_seconds is int, met een standaardwaarde van 43.200 seconden (12 uur).

[ ** \@output_type =** ] 'output_type'  
Als dit niet null is, wordt het type bestemming waarnaar de opdracht eerste resultaatset is geschreven. output_type is nvarchar(50), met een standaard van NULL.

Indien opgegeven, moet de waarde SqlDatabase zijn.

[ ** \@output_credential_name =** ] 'output_credential_name'  
Als dit niet null is, wordt de naam van de databasemet scoped-referentie gebruikt om verbinding te maken met de uitvoerdoeldatabase. Moet worden opgegeven als output_type gelijk is aan SqlDatabase. output_credential_name is nvarchar(128), met een standaardwaarde van NULL.

[ ** \@output_subscription_id =** ] 'output_subscription_id'  
Moet beschrijving.

[ ** \@output_resource_group_name =** ] 'output_resource_group_name'  
Moet beschrijving.

[ ** \@output_server_name =** ] 'output_server_name'  
Als dit niet null is, wordt de volledig gekwalificeerde DNS-naam van de server die de uitvoerdoeldatabase bevat, weergegeven. Moet worden opgegeven als output_type gelijk is aan SqlDatabase. output_server_name is nvarchar(256), met een standaard van NULL.

[ ** \@output_database_name =** ] 'output_database_name'  
Als null, de naam van de database die de uitvoerdoeltabel bevat. Moet worden opgegeven als output_type gelijk is aan SqlDatabase. output_database_name is nvarchar(128), met een standaard van NULL.

[ ** \@output_schema_name =** ] 'output_schema_name'  
Als dit niet null is, wordt de naam van het SQL-schema dat de uitvoerdoeltabel bevat, weergegeven. Als output_type gelijk is aan SqlDatabase, is de standaardwaarde dbo. output_schema_name is nvarchar(128).

[ ** \@output_table_name =** ] 'output_table_name'  
Als dit niet null is, wordt de naam van de tabel waarnaar de opdracht eerste resultaatset is geschreven. Als de tabel nog niet bestaat, wordt deze gemaakt op basis van het schema van de terugkerende resultaatset. Moet worden opgegeven als output_type gelijk is aan SqlDatabase. output_table_name is nvarchar(128), met een standaardwaarde van NULL.

[ ** \@job_version =** ] job_version OUTPUT  
Uitvoerparameter die het nieuwe taakversienummer krijgt toegewezen. job_version is int.

[ ** \@max_parallelism =** ] max_parallelism OUTPUT  
Het maximale parallellisme per elastische pool. Als ingesteld, wordt de taakstap beperkt tot maximaal zoveel databases per elastische groep. Dit geldt voor elke elastische pool die direct in de doelgroep is opgenomen of zich bevindt in een server die deel uitmaakt van de doelgroep. max_parallelism is int.


#### <a name="return-code-values"></a>Waarden voor retourcode
0 (succes) of 1 (mislukking)

#### <a name="remarks"></a>Opmerkingen
Wanneer sp_add_jobstep slaagt, wordt het huidige versienummer van de taak verhoogd. De volgende keer dat de taak wordt uitgevoerd, wordt de nieuwe versie gebruikt. Als de taak momenteel wordt uitgevoerd, bevat die uitvoering de nieuwe stap niet.

#### <a name="permissions"></a>Machtigingen
Standaard kunnen leden van de vaste serverrol van sysadmin deze opgeslagen procedure uitvoeren. Ze beperken een gebruiker om alleen taken te kunnen controleren, u de gebruiker de opdracht geven om deel uit te maken van de volgende databaserol in de database met taakagents die is opgegeven bij het maken van de taakagent:  

- jobs_reader

Zie de sectie Machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken van taken te bewerken die eigendom zijn van andere gebruikers.



### <a name="sp_update_jobstep"></a><a name="sp_update_jobstep"></a>sp_update_jobstep

Werkt een taakstap bij.

#### <a name="syntax"></a>Syntaxis

```sql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @new_id = ] new_id ]   
     [ , [ @new_name = ] 'new_name' ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumenten
[ ** \@job_name =** ] 'job_name'  
De naam van de taak waartoe de stap behoort. job_name is nvarchar(128).

[ ** \@step_id =** ] step_id  
Het identificatienummer voor de taakstap die moet worden gewijzigd. Step_id of step_name moeten worden opgegeven. step_id is een int.

[ ** \@step_name =** ] "step_name"  
De naam van de stap die moet worden gewijzigd. Step_id of step_name moeten worden opgegeven. step_name is nvarchar(128).

[ ** \@new_id =** ] new_id  
Het nieuwe sequentie-identificatienummer voor de taakstap. Stapidentificatienummers beginnen bij 1 en nemen zonder hiaten toe. Als een stap opnieuw wordt geordend, worden andere stappen automatisch opnieuw genummerd.

[ ** \@new_name =** ] 'new_name'  
De nieuwe naam van de stap. new_name is nvarchar(128).

[ ** \@command_type =** ] "command_type"  
Het type opdracht dat wordt uitgevoerd door deze taakstap. command_type is nvarchar(50), met een standaardwaarde van TSql, wat betekent dat de waarde van de @command_type parameter een T-SQL-script is.

Indien opgegeven, moet de waarde TSql zijn.

[ ** \@command_source =** ] 'command_source'  
Het type locatie waar de opdracht wordt opgeslagen. command_source is nvarchar(50), met een standaardwaarde van Inline, wat betekent dat de waarde van de @command_source parameter de letterlijke tekst van de opdracht is.

Indien opgegeven, moet de waarde Inline zijn.

[ ** \@opdracht =** ] 'commando'  
De opdracht(en) moet geldig T-SQL-script zijn en vervolgens worden uitgevoerd door deze taakstap. opdracht is nvarchar(max), met een standaard van NULL.

[ ** \@credential_name =** ] 'credential_name'  
De naam van de database scoped credential stored in this job control database that is used to connect to each of the target databases within the target group when this step is executed. credential_name is nvarchar(128).

[ ** \@target_group_name =** ] 'target-group_name'  
De naam van de doelgroep die de doeldatabases bevat waarop de taakstap wordt uitgevoerd. target_group_name is nvarchar(128).

[ ** \@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
De vertraging vóór de eerste poging opnieuw proberen, als de taakstap op de aanvankelijke uitvoeringspoging ontbreekt. initial_retry_interval_seconds is int, met standaardwaarde van 1.

[ ** \@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
De maximale vertraging tussen pogingen om opnieuw te proberen. Als de vertraging tussen nieuwe pogingen groter zou worden dan deze waarde, wordt deze beperkt tot deze waarde. maximum_retry_interval_seconds is int, met standaardwaarde van 120.

[ ** \@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
De multiplier die moet worden toegepast op de vertraging opnieuw proberen als pogingen tot uitvoering van meerdere taken mislukken. Bijvoorbeeld, als de eerste poging had een vertraging van 5 seconden en de backoff multiplier is 2.0, dan is de tweede opnieuw proberen zal een vertraging van 10 seconden en de derde poging zal een vertraging van 20 seconden hebben. retry_interval_backoff_multiplier is reëel, met een standaardwaarde van 2,0.

[ ** \@retry_attempts =** ] retry_attempts  
Het aantal keren dat de uitvoering opnieuw moet worden uitgevoerd als de eerste poging mislukt. Als de waarde van de retry_attempts bijvoorbeeld 10 is, zijn er 1 eerste poging en 10 pogingen om opnieuw te proberen, waardoor in totaal 11 pogingen worden gegeven. Als de laatste poging opnieuw proberen mislukt, wordt de taakuitvoering beëindigd met een levenscyclus van Mislukt. retry_attempts is int, met standaardwaarde van 10.

[ ** \@step_timeout_seconds =** ] step_timeout_seconds  
De maximale hoeveelheid tijd die is toegestaan voor de uitvoering van de stap. Als deze tijd wordt overschreden, wordt de taakuitvoering beëindigd met een levenscyclus van TimedOut. step_timeout_seconds is int, met een standaardwaarde van 43.200 seconden (12 uur).

[ ** \@output_type =** ] 'output_type'  
Als dit niet null is, wordt het type bestemming waarnaar de opdracht eerste resultaatset is geschreven. Als u de waarde van output_type terug wilt zetten naar NULL, stelt u de waarde van deze parameter in op '' (lege tekenreeks). output_type is nvarchar(50), met een standaard van NULL.

Indien opgegeven, moet de waarde SqlDatabase zijn.

[ ** \@output_credential_name =** ] 'output_credential_name'  
Als dit niet null is, wordt de naam van de databasemet scoped-referentie gebruikt om verbinding te maken met de uitvoerdoeldatabase. Moet worden opgegeven als output_type gelijk is aan SqlDatabase. Als u de waarde van output_credential_name terug wilt zetten naar NULL, stelt u de waarde van deze parameter in op '' (lege tekenreeks). output_credential_name is nvarchar(128), met een standaardwaarde van NULL.

[ ** \@output_server_name =** ] 'output_server_name'  
Als dit niet null is, wordt de volledig gekwalificeerde DNS-naam van de server die de uitvoerdoeldatabase bevat, weergegeven. Moet worden opgegeven als output_type gelijk is aan SqlDatabase. Als u de waarde van output_server_name terug wilt zetten naar NULL, stelt u de waarde van deze parameter in op '' (lege tekenreeks). output_server_name is nvarchar(256), met een standaard van NULL.

[ ** \@output_database_name =** ] 'output_database_name'  
Als null, de naam van de database die de uitvoerdoeltabel bevat. Moet worden opgegeven als output_type gelijk is aan SqlDatabase. Als u de waarde van output_database_name terug wilt zetten naar NULL, stelt u de waarde van deze parameter in op '' (lege tekenreeks). output_database_name is nvarchar(128), met een standaard van NULL.

[ ** \@output_schema_name =** ] 'output_schema_name'  
Als dit niet null is, wordt de naam van het SQL-schema dat de uitvoerdoeltabel bevat, weergegeven. Als output_type gelijk is aan SqlDatabase, is de standaardwaarde dbo. Als u de waarde van output_schema_name terug wilt zetten naar NULL, stelt u de waarde van deze parameter in op '' (lege tekenreeks). output_schema_name is nvarchar(128).

[ ** \@output_table_name =** ] 'output_table_name'  
Als dit niet null is, wordt de naam van de tabel waarnaar de opdracht eerste resultaatset is geschreven. Als de tabel nog niet bestaat, wordt deze gemaakt op basis van het schema van de terugkerende resultaatset. Moet worden opgegeven als output_type gelijk is aan SqlDatabase. Als u de waarde van output_server_name terug wilt zetten naar NULL, stelt u de waarde van deze parameter in op '' (lege tekenreeks). output_table_name is nvarchar(128), met een standaardwaarde van NULL.

[ ** \@job_version =** ] job_version OUTPUT  
Uitvoerparameter die het nieuwe taakversienummer krijgt toegewezen. job_version is int.

[ ** \@max_parallelism =** ] max_parallelism OUTPUT  
Het maximale parallellisme per elastische pool. Als ingesteld, wordt de taakstap beperkt tot maximaal zoveel databases per elastische groep. Dit geldt voor elke elastische pool die direct in de doelgroep is opgenomen of zich bevindt in een server die deel uitmaakt van de doelgroep. Als u de waarde van max_parallelism terug wilt zetten naar null, stelt u de waarde van deze parameter in op -1. max_parallelism is int.


#### <a name="return-code-values"></a>Waarden voor retourcode
0 (succes) of 1 (mislukking)

#### <a name="remarks"></a>Opmerkingen
Alle lopende uitvoeringen van de taak worden niet beïnvloed. Wanneer sp_update_jobstep slaagt, wordt het versienummer van de taak verhoogd. De volgende keer dat de taak wordt uitgevoerd, wordt de nieuwe versie gebruikt.

#### <a name="permissions"></a>Machtigingen
Standaard kunnen leden van de vaste serverrol van sysadmin deze opgeslagen procedure uitvoeren. Ze beperken een gebruiker om alleen taken te kunnen controleren, u de gebruiker de opdracht geven om deel uit te maken van de volgende databaserol in de database met taakagents die is opgegeven bij het maken van de taakagent:

- jobs_reader

Zie de sectie Machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken van taken te bewerken die eigendom zijn van andere gebruikers




### <a name="sp_delete_jobstep"></a><a name="sp_delete_jobstep"></a>sp_delete_jobstep

Hiermee verwijdert u een taakstap uit een taak.

#### <a name="syntax"></a>Syntaxis


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumenten
[ ** \@job_name =** ] 'job_name'  
De naam van de taak waaruit de stap wordt verwijderd. job_name is nvarchar(128), zonder standaard.

[ ** \@step_id =** ] step_id  
Het identificatienummer voor de taakstap die moet worden verwijderd. Step_id of step_name moeten worden opgegeven. step_id is een int.

[ ** \@step_name =** ] "step_name"  
De naam van de stap die moet worden verwijderd. Step_id of step_name moeten worden opgegeven. step_name is nvarchar(128).

[ ** \@job_version =** ] job_version OUTPUT  
Uitvoerparameter die het nieuwe taakversienummer krijgt toegewezen. job_version is int.

#### <a name="return-code-values"></a>Waarden voor retourcode
0 (succes) of 1 (mislukking)

#### <a name="remarks"></a>Opmerkingen
Alle lopende uitvoeringen van de taak worden niet beïnvloed. Wanneer sp_update_jobstep slaagt, wordt het versienummer van de taak verhoogd. De volgende keer dat de taak wordt uitgevoerd, wordt de nieuwe versie gebruikt.

De andere taakstappen worden automatisch opnieuw genummerd om het gat op te vullen dat is achtergelaten bij de verwijderde taakstap.
 
#### <a name="permissions"></a>Machtigingen
Standaard kunnen leden van de vaste serverrol van sysadmin deze opgeslagen procedure uitvoeren. Ze beperken een gebruiker om alleen taken te kunnen controleren, u de gebruiker de opdracht geven om deel uit te maken van de volgende databaserol in de database met taakagents die is opgegeven bij het maken van de taakagent:
- jobs_reader

Zie de sectie Machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken van taken te bewerken die eigendom zijn van andere gebruikers.






### <a name="sp_start_job"></a><a name="sp_start_job"></a>sp_start_job

Begint met het uitvoeren van een taak.

#### <a name="syntax"></a>Syntaxis


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argumenten
[ ** \@job_name =** ] 'job_name'  
De naam van de taak waaruit de stap wordt verwijderd. job_name is nvarchar(128), zonder standaard.

[ ** \@job_execution_id =** ] job_execution_id OUTPUT  
Uitvoerparameter die de id van de taakuitvoering zal toegewezen krijgen. job_version is uniek.

#### <a name="return-code-values"></a>Waarden voor retourcode
0 (succes) of 1 (mislukking)

#### <a name="remarks"></a>Opmerkingen
Geen.
 
#### <a name="permissions"></a>Machtigingen
Standaard kunnen leden van de vaste serverrol van sysadmin deze opgeslagen procedure uitvoeren. Ze beperken een gebruiker om alleen taken te kunnen controleren, u de gebruiker de opdracht geven om deel uit te maken van de volgende databaserol in de database met taakagents die is opgegeven bij het maken van de taakagent:
- jobs_reader

Zie de sectie Machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken van taken te bewerken die eigendom zijn van andere gebruikers.

### <a name="sp_stop_job"></a><a name="sp_stop_job"></a>sp_stop_job

Stopt een taakuitvoering.

#### <a name="syntax"></a>Syntaxis


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argumenten
[ ** \@job_execution_id =** ] job_execution_id  
Het identificatienummer van de taakuitvoering om te stoppen. job_execution_id is uniekid, met standaard van NULL.

#### <a name="return-code-values"></a>Waarden voor retourcode
0 (succes) of 1 (mislukking)

#### <a name="remarks"></a>Opmerkingen
Geen.
 
#### <a name="permissions"></a>Machtigingen
Standaard kunnen leden van de vaste serverrol van sysadmin deze opgeslagen procedure uitvoeren. Ze beperken een gebruiker om alleen taken te kunnen controleren, u de gebruiker de opdracht geven om deel uit te maken van de volgende databaserol in de database met taakagents die is opgegeven bij het maken van de taakagent:
- jobs_reader

Zie de sectie Machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken van taken te bewerken die eigendom zijn van andere gebruikers.


### <a name="sp_add_target_group"></a><a name="sp_add_target_group"></a>sp_add_target_group

Voegt een doelgroep toe.

#### <a name="syntax"></a>Syntaxis


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argumenten
[ ** \@target_group_name =** ] 'target_group_name'  
De naam van de te maken doelgroep. target_group_name is nvarchar(128), zonder standaard.

[ ** \@target_group_id =** ] target_group_id OUTPUT Het identificatienummer van de doelgroep dat aan de taak is toegewezen als deze met succes is gemaakt. target_group_id is een uitvoervariabele van type uniqueidentifier, met een standaard van NULL.

#### <a name="return-code-values"></a>Waarden voor retourcode
0 (succes) of 1 (mislukking)

#### <a name="remarks"></a>Opmerkingen
Doelgroepen bieden een eenvoudige manier om een baan te richten op een verzameling databases.

#### <a name="permissions"></a>Machtigingen
Standaard kunnen leden van de vaste serverrol van sysadmin deze opgeslagen procedure uitvoeren. Ze beperken een gebruiker om alleen taken te kunnen controleren, u de gebruiker de opdracht geven om deel uit te maken van de volgende databaserol in de database met taakagents die is opgegeven bij het maken van de taakagent:
- jobs_reader

Zie de sectie Machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken van taken te bewerken die eigendom zijn van andere gebruikers.

### <a name="sp_delete_target_group"></a><a name="sp_delete_target_group"></a>sp_delete_target_group

Hiermee verwijdert u een doelgroep.

#### <a name="syntax"></a>Syntaxis


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argumenten
[ ** \@target_group_name =** ] 'target_group_name'  
De naam van de te verwijderen doelgroep. target_group_name is nvarchar(128), zonder standaard.

#### <a name="return-code-values"></a>Waarden voor retourcode
0 (succes) of 1 (mislukking)

#### <a name="remarks"></a>Opmerkingen
Geen.

#### <a name="permissions"></a>Machtigingen
Standaard kunnen leden van de vaste serverrol van sysadmin deze opgeslagen procedure uitvoeren. Ze beperken een gebruiker om alleen taken te kunnen controleren, u de gebruiker de opdracht geven om deel uit te maken van de volgende databaserol in de database met taakagents die is opgegeven bij het maken van de taakagent:
- jobs_reader

Zie de sectie Machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken van taken te bewerken die eigendom zijn van andere gebruikers.

### <a name="sp_add_target_group_member"></a><a name="sp_add_target_group_member"></a>sp_add_target_group_member

Hiermee voegt u een database of groep databases toe aan een doelgroep.

#### <a name="syntax"></a>Syntaxis

```sql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] 'membership_type' ]   
        [ , [ @target_type = ] 'target_type' ]   
        [ , [ @refresh_credential_name = ] 'refresh_credential_name' ]   
        [ , [ @server_name = ] 'server_name' ]   
        [ , [ @database_name = ] 'database_name' ]   
        [ , [ @elastic_pool_name = ] 'elastic_pool_name' ]   
        [ , [ @shard_map_name = ] 'shard_map_name' ]   
        [ , [ @target_id = ] 'target_id' OUTPUT ]
```

#### <a name="arguments"></a>Argumenten
[ ** \@target_group_name =** ] 'target_group_name'  
De naam van de doelgroep waaraan het lid zal worden toegevoegd. target_group_name is nvarchar(128), zonder standaard.

[ ** \@membership_type =** ] 'membership_type'  
Hiermee geeft u op of het doelgroeplid wordt opgenomen of uitgesloten. target_group_name is nvarchar(128), met standaard 'Opnemen'. Geldige waarden voor target_group_name zijn 'Opnemen' of 'Uitsluiten'.

[ ** \@target_type =** ] 'target_type'  
Het type doeldatabase of verzameling van databases, inclusief alle databases in een server, alle databases in een Elastic-groep, alle databases in een shardkaart of een afzonderlijke database. target_type is nvarchar(128), zonder standaard. Geldige waarden voor target_type zijn 'SqlServer', 'SqlElasticPool', 'SqlDatabase' of 'SqlShardMap'. 

[ ** \@refresh_credential_name =** ] 'refresh_credential_name'  
De naam van de SQL Database-server. refresh_credential_name is nvarchar(128), zonder standaard.

[ ** \@server_name =** ] 'server_name'  
De naam van de SQL Database-server die moet worden toegevoegd aan de opgegeven doelgroep. server_name moet worden opgegeven wanneer target_type 'SqlServer' is. server_name is nvarchar(128), zonder standaard.

[ ** \@database_name =** ] 'database_name'  
De naam van de database die moet worden toegevoegd aan de opgegeven doelgroep. database_name moet worden opgegeven wanneer target_type 'SqlDatabase' is. database_name is nvarchar(128), zonder standaard.

[ ** \@elastic_pool_name =** ] "elastic_pool_name"  
De naam van de elastic pool die moet worden toegevoegd aan de opgegeven doelgroep. elastic_pool_name moet worden opgegeven wanneer target_type 'SqlElasticPool' is. elastic_pool_name is nvarchar(128), zonder standaard.

[ ** \@shard_map_name =** ] 'shard_map_name'  
De naam van de shardmappool die moet worden toegevoegd aan de opgegeven doelgroep. elastic_pool_name moet worden opgegeven wanneer target_type 'SqlSqlShardMap' is. shard_map_name is nvarchar(128), zonder standaard.

[ ** \@target_id =** ] target_group_id OUTPUT  
Het doelidentificatienummer dat is toegekend aan het doelgroeplid als het wordt gemaakt, wordt toegevoegd aan de doelgroep. target_id is een uitvoervariabele van type uniqueidentifier, met een standaard van NULL.
Retourcodewaarden 0 (succes) of 1 (mislukt)

#### <a name="remarks"></a>Opmerkingen
Een taak wordt uitgevoerd op alle afzonderlijke databases binnen een SQL Database-server of in een elastische groep op het moment van uitvoering, wanneer een SQL Database-server of Elastic-groep in de doelgroep is opgenomen.

#### <a name="permissions"></a>Machtigingen
Standaard kunnen leden van de vaste serverrol van sysadmin deze opgeslagen procedure uitvoeren. Ze beperken een gebruiker om alleen taken te kunnen controleren, u de gebruiker de opdracht geven om deel uit te maken van de volgende databaserol in de database met taakagents die is opgegeven bij het maken van de taakagent:
- jobs_reader

Zie de sectie Machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken van taken te bewerken die eigendom zijn van andere gebruikers.

#### <a name="examples"></a>Voorbeelden
In het volgende voorbeeld worden alle databases in de servers van Londen en NewYork toegevoegd aan de groep Servers Die klantgegevens onderhouden. U moet verbinding maken met de taakdatabase die is opgegeven bij het maken van de taakagent, in dit geval ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information'
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net' ;
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net' ;
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="sp_delete_target_group_member"></a><a name="sp_delete_target_group_member"></a>sp_delete_target_group_member

Hiermee verwijdert u een doelgroeplid uit een doelgroep.

#### <a name="syntax"></a>Syntaxis


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] 'target_id']
```



Argumenten @target_group_name [ = ] "target_group_name"  
De naam van de doelgroep waaruit het doelgroeplid moet worden verwijderd. target_group_name is nvarchar(128), zonder standaard.

[ @target_id = ] target_id  
 Het doelidentificatienummer dat is toegekend aan het te verwijderen doelgroeplid. target_id is een unieke id, met een standaard van NULL.

#### <a name="return-code-values"></a>Waarden voor retourcode
0 (succes) of 1 (mislukking)

#### <a name="remarks"></a>Opmerkingen
Doelgroepen bieden een eenvoudige manier om een baan te richten op een verzameling databases.

#### <a name="permissions"></a>Machtigingen
Standaard kunnen leden van de vaste serverrol van sysadmin deze opgeslagen procedure uitvoeren. Ze beperken een gebruiker om alleen taken te kunnen controleren, u de gebruiker de opdracht geven om deel uit te maken van de volgende databaserol in de database met taakagents die is opgegeven bij het maken van de taakagent:
- jobs_reader

Zie de sectie Machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken van taken te bewerken die eigendom zijn van andere gebruikers.

#### <a name="examples"></a>Voorbeelden
In het volgende voorbeeld wordt de Londense server verwijderd uit de groep Servers Maintaining Customer Information. U moet verbinding maken met de taakdatabase die is opgegeven bij het maken van de taakagent, in dit geval ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net'

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid
GO
```

### <a name="sp_purge_jobhistory"></a><a name="sp_purge_jobhistory"></a>sp_purge_jobhistory

Hiermee verwijdert u de geschiedenisrecords voor een taak.

#### <a name="syntax"></a>Syntaxis


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumenten
[ ** \@job_name =** ] 'job_name'  
De naam van de taak waarvoor de geschiedenisrecords moeten worden verwijderd. job_name is nvarchar(128), met een standaard van NULL. job_id of job_name moet worden opgegeven, maar beide kunnen niet worden opgegeven.

[ ** \@job_id =** ] job_id  
 Het taakidentificatienummer van de taak voor de records die moeten worden verwijderd. job_id is uniekid, met een standaard van NULL. job_id of job_name moet worden opgegeven, maar beide kunnen niet worden opgegeven.

[ ** \@oldest_date =** ] oldest_date  
 Het oudste record dat je in de geschiedenis bewaart. oldest_date is DATETIME2, met een standaard van NULL. Wanneer oldest_date is opgegeven, verwijdert sp_purge_jobhistory alleen records die ouder zijn dan de opgegeven waarde.

#### <a name="return-code-values"></a>Waarden voor retourcode
0 (succes) of 1 (mislukking) Opmerkingen Doelgroepen bieden een eenvoudige manier om een baan te richten op een verzameling databases.

#### <a name="permissions"></a>Machtigingen
Standaard kunnen leden van de vaste serverrol van sysadmin deze opgeslagen procedure uitvoeren. Ze beperken een gebruiker om alleen taken te kunnen controleren, u de gebruiker de opdracht geven om deel uit te maken van de volgende databaserol in de database met taakagents die is opgegeven bij het maken van de taakagent:
- jobs_reader

Zie de sectie Machtiging in dit document voor meer informatie over de machtigingen van deze rollen. Alleen leden van sysadmin kunnen deze opgeslagen procedure gebruiken om de kenmerken van taken te bewerken die eigendom zijn van andere gebruikers.

#### <a name="examples"></a>Voorbeelden
In het volgende voorbeeld worden alle databases in de servers van Londen en NewYork toegevoegd aan de groep Servers Die klantgegevens onderhouden. U moet verbinding maken met de taakdatabase die is opgegeven bij het maken van de taakagent, in dit geval ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Vacatures

De volgende weergaven zijn beschikbaar in de [taakdatabase](sql-database-job-automation-overview.md#job-database).


|Weergave  |Beschrijving  |
|---------|---------|
|[job_executions](#job_executions-view)     |  Toont de uitvoeringsgeschiedenis van de taak.      |
|[Banen](#jobs-view)     |   Toont alle vacatures.      |
|[job_versions](#job_versions-view)     |   Toont alle taakversies.      |
|[jobsteps](#jobsteps-view)     |     Hiermee worden alle stappen in de huidige versie van elke taak weergegeven.    |
|[jobstep_versions](#jobstep_versions-view)     |     Hiermee worden alle stappen in alle versies van elke taak weergegeven.    |
|[target_groups](#target_groups-view)     |      Toont alle doelgroepen.   |
|[target_group_members](#target_group_members-view)     |   Toont alle leden van alle doelgroepen.      |


### <a name="job_executions-view"></a><a name="job_executions-view"></a>job_executions weergave

[banen]. [job_executions]

Toont de uitvoeringsgeschiedenis van de taak.


|Kolomnaam|   Gegevenstype   |Beschrijving|
|---------|---------|---------|
|**job_execution_id**   |uniqueidentifier|  Unieke ID van een instantie van een taakuitvoering.
|**job_name**   |nvarchar(128)  |Naam van de baan.
|**job_id** |uniqueidentifier|  Unieke ID van de baan.
|**job_version**    |int    |Versie van de taak (automatisch bijgewerkt elke keer dat de taak wordt gewijzigd).
|**step_id**    |int|   Unieke (voor deze taak) id voor de stap. NULL geeft aan dat dit de bovenliggende taakuitvoering is.
|**is_active**| bit |Geeft aan of informatie actief of inactief is. 1 geeft actieve taken aan en 0 geeft inactief aan.
|**Levenscyclus**| nvarchar(50)|Waarde die de status van de taak aangeeft:'Gemaakt', 'In uitvoering', 'Mislukt', 'Geslaagd', 'Overgeslagen', 'Geslaagd'|
|**create_time**|   datum2(7)|   Datum en tijd van de taak is gemaakt.
|**start_time** |datum2(7)|  Datum en tijd waarop de taak is gestart met de uitvoering. NULL als de taak nog niet is uitgevoerd.
|**end_time**|  datum2(7)    |Datum en tijd waarop de taak is voltooid. NULL als de taak nog niet is uitgevoerd of nog niet is voltooid.
|**current_attempts**   |int    |Het aantal keren dat de stap opnieuw is geprobeerd. Bovenliggende taak is 0, uitvoeringen van onderliggende taken zijn 1 of hoger op basis van het uitvoeringsbeleid.
|**current_attempt_start_time** |datum2(7)|  Datum en tijd waarop de taak is gestart met de uitvoering. NULL geeft aan dat dit de bovenliggende taakuitvoering is.
|**last_message**   |nvarchar(max.| Bericht van de baan- of stapgeschiedenis. 
|**target_type**|   nvarchar(128)   |Type doeldatabase of verzameling van databases, inclusief alle databases in een server, alle databases in een Elastic-groep of een database. Geldige waarden voor target_type zijn 'SqlServer', 'SqlElasticPool' of 'SqlDatabase'. NULL geeft aan dat dit de bovenliggende taakuitvoering is.
|**target_id**  |uniqueidentifier|  Unieke ID van het doelgroeplid.  NULL geeft aan dat dit de bovenliggende taakuitvoering is.
|**target_group_name**  |nvarchar(128)  |Naam van de doelgroep. NULL geeft aan dat dit de bovenliggende taakuitvoering is.
|**target_server_name**|    nvarchar(256)|  Naam van de SQL Database-server in de doelgroep. Alleen opgegeven als target_type 'SqlServer' is. NULL geeft aan dat dit de bovenliggende taakuitvoering is.
|**target_database_name**   |nvarchar(128)| Naam van de database in de doelgroep. Alleen opgegeven wanneer target_type 'SqlDatabase' is. NULL geeft aan dat dit de bovenliggende taakuitvoering is.


### <a name="jobs-view"></a>banenweergave

[banen]. [vacatures]

Toont alle vacatures.

|Kolomnaam|   Gegevenstype|  Beschrijving|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Naam van de baan.|
|**job_id**|    uniqueidentifier    |Unieke ID van de baan.|
|**job_version**    |int    |Versie van de taak (automatisch bijgewerkt elke keer dat de taak wordt gewijzigd).|
|**Beschrijving**    |nvarchar(512)| Beschrijving voor de taak. ingeschakelde bit Geeft aan of de taak is ingeschakeld of uitgeschakeld. 1 geeft ingeschakelde taken aan en 0 geeft uitgeschakelde taken aan.|
|**schedule_interval_type** |nvarchar(50)   |Waarde die aangeeft wanneer de taak moet worden uitgevoerd:'Eenmaal', 'Minuten', 'Uren', 'Dagen', 'Weken', 'Maanden'
|**schedule_interval_count**|   int|    Aantal schedule_interval_type perioden die moeten optreden tussen elke uitvoering van de taak.|
|**schedule_start_time**    |datum2(7)|  Datum en tijd waarop de taak voor het laatst is gestart met de uitvoering.|
|**schedule_end_time**| datum2(7)|   Datum en tijd waarop de taak voor het laatst is voltooid.|


### <a name="job_versions-view"></a><a name="job_versions-view"></a>job_versions weergave

[banen]. [job_versions]

Toont alle taakversies.

|Kolomnaam|   Gegevenstype|  Beschrijving|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Naam van de baan.|
|**job_id**|    uniqueidentifier    |Unieke ID van de baan.|
|**job_version**    |int    |Versie van de taak (automatisch bijgewerkt elke keer dat de taak wordt gewijzigd).|


### <a name="jobsteps-view"></a>jobsteps-weergave

[banen]. [jobsteps]

Hiermee worden alle stappen in de huidige versie van elke taak weergegeven.

|Kolomnaam    |Gegevenstype| Beschrijving|
|------|------|-------|
|**job_name**   |nvarchar(128)| Naam van de baan.|
|**job_id** |uniqueidentifier   |Unieke ID van de baan.|
|**job_version**|   int|    Versie van de taak (automatisch bijgewerkt elke keer dat de taak wordt gewijzigd).|
|**step_id**    |int    |Unieke (voor deze taak) id voor de stap.|
|**step_name**  |nvarchar(128)  |Unieke (voor deze taak) naam voor de stap.|
|**command_type**   |nvarchar(50)   |Type opdracht dat moet worden uitgevoerd in de taakstap. Voor v1 moet de waarde gelijk zijn aan en standaard 'TSql' zijn.|
|**command_source** |nvarchar(50)|  Locatie van de opdracht. Voor v1 is 'Inline' de standaard waarde en alleen geaccepteerde waarde.|
|**command**|   nvarchar(max.|  De opdrachten die door Elastic-taken moeten worden uitgevoerd via command_type.|
|**credential_name**|   nvarchar(128)   |Naam van de database scoped credential die wordt gebruikt om de taak uit te voeren.|
|**target_group_name**| nvarchar(128)   |Naam van de doelgroep.|
|**target_group_id**|   uniqueidentifier|   Unieke ID van de doelgroep.|
|**initial_retry_interval_seconds**|    int |De vertraging voor de eerste poging opnieuw proberen. De standaardwaarde is 1.|
|**maximum_retry_interval_seconds** |int|   De maximale vertraging tussen pogingen om opnieuw te proberen. Als de vertraging tussen nieuwe pogingen groter zou worden dan deze waarde, wordt deze beperkt tot deze waarde. De standaardwaarde is 120.|
|**retry_interval_backoff_multiplier**  |real|  De multiplier die moet worden toegepast op de vertraging opnieuw proberen als pogingen tot uitvoering van meerdere taken mislukken. De standaardwaarde is 2,0.|
|**retry_attempts** |int|   Het aantal pogingen om opnieuw te proberen te gebruiken als deze stap mislukt. Standaard van 10, wat aangeeft dat er geen pogingen opnieuw proberen.|
|**step_timeout_seconds**   |int|   De hoeveelheid tijd in minuten tussen pogingen opnieuw proberen. De standaardinstelling is 0, wat een interval van 0 minuten aangeeft.|
|**output_type**    |nvarchar(11)|  Locatie van de opdracht. In de huidige preview is 'Inline' de standaard waarde en alleen geaccepteerde waarde.|
|**output_credential_name**|    nvarchar(128)   |Naam van de referenties die moeten worden gebruikt om verbinding te maken met de doelserver om de resultatenset op te slaan.|
|**output_subscription_id**|    uniqueidentifier|   Unieke id van het abonnement van de doelserver\-database voor de resultaten die zijn ingesteld op de queryuitvoering.|
|**output_resource_group_name** |nvarchar(128)| Naam resourcegroep waar de doelserver zich bevindt.|
|**output_server_name**|    nvarchar(256)   |Naam van de doelserver voor de resultatenset.|
|**output_database_name**   |nvarchar(128)| Naam van de doeldatabase voor de ingestelde resultaten.|
|**output_schema_name** |nvarchar(max.| Naam van het doelschema. Standaard dbo, indien niet opgegeven.|
|**output_table_name**| nvarchar(max.|  Naam van de tabel om de resultaten set op te slaan van de queryresultaten. Tabel wordt automatisch gemaakt op basis van het schema van de resultaten ingesteld als het niet al bestaat. Het schema moet overeenkomen met het schema van de ingestelde resultaten.|
|**max_parallelism**|   int|    Het maximum aantal databases per elastische groep waarop de taakstap wordt uitgevoerd. De standaardinstelling is NULL, wat betekent dat er geen limiet is. |


### <a name="jobstep_versions-view"></a><a name="jobstep_versions-view"></a>jobstep_versions weergave

[banen]. [jobstep_versions]

Hiermee worden alle stappen in alle versies van elke taak weergegeven. Het schema is identiek aan [jobsteps](#jobsteps-view).

### <a name="target_groups-view"></a><a name="target_groups-view"></a>target_groups weergave

[banen]. [target_groups]

Hier worden alle doelgroepen weergegeven.

|Kolomnaam|Gegevenstype| Beschrijving|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |De naam van de doelgroep, een verzameling databases. 
|**target_group_id**    |uniqueidentifier   |Unieke ID van de doelgroep.

### <a name="target_group_members-view"></a><a name="target_group_members-view"></a>target_group_members weergave

[banen]. [target_group_members]

Toont alle leden van alle doelgroepen.

|Kolomnaam|Gegevenstype| Beschrijving|
|-----|-----|-----|
|**target_group_name**  |nvarchar(128|De naam van de doelgroep, een verzameling databases. |
|**target_group_id**    |uniqueidentifier   |Unieke ID van de doelgroep.|
|**membership_type**    |int|   Hiermee geeft u op of het doelgroeplid is opgenomen of uitgesloten in de doelgroep. Geldige waarden voor target_group_name zijn 'Opnemen' of 'Uitsluiten'.|
|**target_type**    |nvarchar(128)| Type doeldatabase of verzameling van databases, inclusief alle databases in een server, alle databases in een Elastic-groep of een database. Geldige waarden voor target_type zijn 'SqlServer', 'SqlElasticPool', 'SqlDatabase' of 'SqlShardMap'.|
|**target_id**  |uniqueidentifier|  Unieke ID van het doelgroeplid.|
|**refresh_credential_name**    |nvarchar(128)  |Naam van de database scoped credential die wordt gebruikt om verbinding te maken met het doelgroeplid.|
|**subscription_id**    |uniqueidentifier|  Unieke ID van het abonnement.|
|**resource_group_name**    |nvarchar(128)| Naam van de resourcegroep waarin het doelgroeplid zich bevindt.|
|**Servernaam**    |nvarchar(128)  |Naam van de SQL Database-server in de doelgroep. Alleen opgegeven als target_type 'SqlServer' is. |
|**Database_name**  |nvarchar(128)  |Naam van de database in de doelgroep. Alleen opgegeven wanneer target_type 'SqlDatabase' is.|
|**elastic_pool_name**  |nvarchar(128)| Naam van de Elastic pool in de doelgroep. Alleen opgegeven wanneer target_type 'SqlElasticPool' is.|
|**shard_map_name** |nvarchar(128)| Naam van de shardkaart in de doelgroep. Alleen opgegeven als target_type 'SqlShardMap' is.|


## <a name="resources"></a>Resources

 - ![Onderwerpkoppelingspictogram](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "Pictogram Onderwerpkoppeling") [Transact-SQL-syntaxisconventies](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  


## <a name="next-steps"></a>Volgende stappen

- [Elastische taken maken en beheren met PowerShell](elastic-jobs-powershell.md)
- [Autorisatie- en machtigingen SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
