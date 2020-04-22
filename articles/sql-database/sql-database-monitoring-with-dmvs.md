---
title: Prestaties bewaken met DMVs
description: Meer informatie over het detecteren en diagnosticeren van veelvoorkomende prestatieproblemen door dynamische beheerweergaven te gebruiken om Microsoft Azure SQL Database te controleren.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 04/19/2020
ms.openlocfilehash: 6f33f49be74419a8f0cd31d973d64798f5d76a2c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683004"
---
# <a name="monitoring-performance-azure-sql-database-using-dynamic-management-views"></a>Prestaties Azure SQL Database bewaken met dynamische beheerweergaven

Met Microsoft Azure SQL Database kan een subset van dynamische beheerweergaven prestatieproblemen worden gediagnosteerd, die kunnen worden veroorzaakt door geblokkeerde of langlopende query's, resourceknelpunten, slechte queryplannen, enzovoort. In dit onderwerp vindt u informatie over het detecteren van veelvoorkomende prestatieproblemen door dynamische beheerweergaven te gebruiken.

SQL Database ondersteunt gedeeltelijk drie categorieën dynamische beheerweergaven:

- Databasegerelateerde dynamische beheerweergaven.
- Execution-gerelateerde dynamische managementviews.
- Transactiegerelateerde dynamische beheerweergaven.

Zie [Dynamic Management Views and Functions (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) in SQL Server Books Online voor gedetailleerde informatie over dynamische beheerweergaven.

## <a name="permissions"></a>Machtigingen

Voor het opvragen van een dynamische beheerweergave is **in** SQL-database statusmachtigingen vereist. Met de machtiging **STATUS WEERGAVEDATABASE** retourneert informatie over alle objecten in de huidige database.
Als u de status van de **WEERGAVE-databasestatus** toestemming wilt verlenen aan een specifieke databasegebruiker, voert u de volgende query uit:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

In een geval van on-premises SQL Server geven dynamische beheerweergaven serverstatusstatusgegevens weer. In SQL Database geven ze alleen informatie terug over uw huidige logische database.

Dit artikel bevat een verzameling DMV-query's die u uitvoeren met SQL Server Management Studio of Azure Data Studio om de volgende typen problemen met queryprestaties op te sporen:

- [Query's identificeren met betrekking tot overmatig CPU-verbruik](#identify-cpu-performance-issues)
- [PAGELATCH_* en WRITE_LOG wacht met io-knelpunten](#identify-io-performance-issues)
- [PAGELATCH_* wacht veroorzaakt bytTempDB stelling](#identify-tempdb-performance-issues)
- [RESOURCE_SEMAHPORE wacht door problemen met wachten op geheugensubsidie](#identify-memory-grant-wait-performance-issues)
- [Database- en objectgroottes identificeren](#calculating-database-and-objects-sizes)
- [Informatie ophalen over actieve sessies](#monitoring-connections)
- [Informatie over systeembreed en databaseresourcegebruik ophalen](#monitor-resource-use)
- [Prestatiegegevens van query's ophalen](#monitoring-query-performance)

## <a name="identify-cpu-performance-issues"></a>Problemen met cpu-prestaties identificeren

Als het CPU-verbruik gedurende langere tijd boven de 80% ligt, u de volgende stappen voor het oplossen van problemen overwegen:

### <a name="the-cpu-issue-is-occurring-now"></a>Het CPU-probleem doet zich nu voor

Als er op dit moment problemen optreden, zijn er twee mogelijke scenario's:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Veel individuele query's die cumulatief hoge CPU verbruiken

Gebruik de volgende query om topqueryhashes te identificeren:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM (SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
    FROM (SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
                CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
    GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Langlopende query's die CPU verbruiken, worden nog steeds uitgevoerd

Gebruik de volgende query om deze query's te identificeren:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
    CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>Het CPU-probleem deed zich in het verleden voor

Als het probleem zich in het verleden heeft voorgedaan en u rootoorzaakanalyse wilt uitvoeren, gebruikt u [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Gebruikers met databasetoegang kunnen T-SQL gebruiken om Query Store-gegevens op te vragen. Standaardconfiguraties van de Query Store gebruiken een granulariteit van 1 uur. Gebruik de volgende query om te kijken naar activiteit voor hoge CPU-verbruikende query's. Met deze query worden de top 15 CPU-verbruikende query's geretourneerd. Vergeet niet `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`om te veranderen:

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

Zodra u de problematische query's identificeert, is het tijd om deze query's af te stemmen om het CPU-gebruik te verminderen.  Als u geen tijd hebt om de query's af te stemmen, u er ook voor kiezen om de SLO van de database te upgraden om het probleem te omzeilen.

## <a name="identify-io-performance-issues"></a>Io-prestatieproblemen identificeren

Bij het identificeren van IO-prestatieproblemen zijn de belangrijkste wachttypen die zijn gekoppeld aan IO-problemen:

- `PAGEIOLATCH_*`

  Voor IO-problemen met `PAGEIOLATCH_SH` `PAGEIOLATCH_EX`gegevensbestanden (waaronder , , `PAGEIOLATCH_UP`).  Als de naam van het wachttype **IO** in zich heeft, wordt een IO-probleem aangeschreven. Als er geen **IO** in de pagina vergrendeling wachten naam, het wijst op een ander type probleem (bijvoorbeeld tempdb stelling).

- `WRITE_LOG`

  Voor io-problemen met transactielogboeken.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Als het IO-probleem zich nu voordoet

Gebruik de [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) of [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) om de `wait_type` en `wait_time`.

#### <a name="identify-data-and-log-io-usage"></a>Gegevens identificeren en IO-gebruik registreren

Gebruik de volgende query om gegevens te identificeren en IO-gebruik te registreren. Als de gegevens of log IO hoger is dan 80%, betekent dit dat gebruikers de beschikbare IO hebben gebruikt voor de SQL DB-servicelaag.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Als de IO-limiet is bereikt, hebt u twee opties:

- Optie 1: De rekengrootte of servicelaag upgraden
- Optie 2: Identificeer en stem de query's af die de meeste IO verbruiken.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Buffergerelateerde IO weergeven met de Query Store

Voor optie 2 u de volgende query tegen Query Store voor buffergerelateerde IO gebruiken om de laatste twee uur van de bijgehouden activiteit weer te geven:

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>Bekijk totaal logboek IO voor WRITELOG wacht

Als het wachttype is, `WRITELOG`gebruikt u de volgende query om de totale log IO per instructie weer te geven:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>Prestatieproblemen identificeren `tempdb`

Bij het identificeren van IO-prestatieproblemen `tempdb` zijn `PAGELATCH_*` de `PAGEIOLATCH_*`belangrijkste wachttypen die aan problemen zijn gekoppeld (niet ). Echter, `PAGELATCH_*` wacht niet altijd betekenen `tempdb` dat je twist.  Dit wachten kan ook betekenen dat u een pagina geschil over gebruikersobjectgegevens hebt vanwege gelijktijdige aanvragen die zich op dezelfde gegevenspagina richten. Gebruik [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) om de geschil te bevestigen `tempdb` om te `tempdb` bevestigen dat de `x` wait_resource waarde begint `y` met `2:x:y` waar 2 de database-id is, de bestands-id is en de pagina-id.  

Voor tempdb stelling, een gemeenschappelijke methode is te verminderen `tempdb`of herschrijven applicatiecode die is gebaseerd op .  Veelvoorkomende gebruiksgebieden `tempdb` zijn:

- Tijdelijke tabellen
- Tabelvariabelen
- Tabelwaardeparameters
- Gebruik van versieopslag (specifiek gekoppeld aan langlopende transacties)
- Query's met queryplannen die soorten, hashjoins en spoelen gebruiken

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Topquery's met tabelvariabelen en tijdelijke tabellen

Gebruik de volgende query om topquery's te identificeren die tabelvariabelen en tijdelijke tabellen gebruiken:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Langlopende transacties identificeren

Gebruik de volgende query om langlopende transacties te identificeren. Langlopende transacties voorkomen het opschonen van versieopslag.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>Problemen met wachtprestaties voor geheugenverlening identificeren

Als uw hoogste `RESOURCE_SEMAHPORE` wachttijd type is en je hebt geen hoge CPU-gebruik probleem, u een memory grant wachten probleem.

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>Bepalen of `RESOURCE_SEMAHPORE` een wachttijd een top wachttijd is

Gebruik de volgende query `RESOURCE_SEMAHPORE` om te bepalen of een wachttijd een topwachttijd is

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identity-high-memory-consuming-statements"></a>Identiteit hoge geheugenverbruikende verklaringen

Gebruik de volgende query om hoge geheugenverbruikende instructies te identificeren:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>De top 10 actieve geheugensubsidies identificeren

Gebruik de volgende query om de top 10 actieve geheugensubsidies te identificeren:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>Database- en objectgroottes berekenen

De volgende query geeft de grootte van uw database (in megabytes) als resultaat:

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

De volgende query retourneert de grootte van afzonderlijke objecten (in megabytes) in uw database:

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Verbindingsverbindingen bewaken

U de [sys.dm_exec_connections-weergave](https://msdn.microsoft.com/library/ms181509.aspx) gebruiken om informatie op te halen over de verbindingen die zijn gemaakt met een specifieke Azure SQL Database-server en de details van elke verbinding. Daarnaast is de [sys.dm_exec_sessions-weergave](https://msdn.microsoft.com/library/ms176013.aspx) handig bij het ophalen van informatie over alle actieve gebruikersverbindingen en interne taken.
In de volgende query wordt informatie over de huidige verbinding opgehaald:

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Bij het uitvoeren van de **sys.dm_exec_requests-** en **sys.dm_exec_sessions-weergaven**ziet u alle uitvoeringssessies in de database als u de **statusvan VIEW DATABASE OVER** hebt. anders ziet u alleen de huidige sessie.

## <a name="monitor-resource-use"></a>Resourcegebruik controleren

U het gebruik van resources controleren met [SQL Database Query Performance Insight](sql-database-query-performance.md) en Query [Store.](https://msdn.microsoft.com/library/dn817826.aspx)

U het gebruik ook controleren met behulp van deze twee weergaven:

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdm_db_resource_stats"></a>sys.dm_db_resource_stats

U de [sys.dm_db_resource_stats-weergave](https://msdn.microsoft.com/library/dn800981.aspx) in elke SQL-database gebruiken. De weergave **sys.dm_db_resource_stats** toont recente gegevens over het resourcegebruik ten opzichte van de servicelaag. Gemiddelde percentages voor CPU, data IO, logschrijft en geheugen worden elke 15 seconden geregistreerd en worden gedurende 1 uur bewaard.

Omdat deze weergave een meer gedetailleerde weergave biedt van het gebruik van resources, gebruikt u **eerst sys.dm_db_resource_stats** voor een analyse of probleemoplossing in de huidige status. In deze query wordt bijvoorbeeld het gemiddelde en maximale resourcegebruik voor de huidige database van het afgelopen uur weergegeven:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

Zie de voorbeelden in [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)voor andere query's.

### <a name="sysresource_stats"></a>sys.resource_stats

De [sys.resource_stats-weergave](https://msdn.microsoft.com/library/dn269979.aspx) in de **hoofddatabase** bevat aanvullende informatie waarmee u de prestaties van uw SQL-database op de specifieke servicelaag en de rekengrootte controleren. De gegevens worden elke 5 minuten verzameld en worden ongeveer 14 dagen bewaard. Deze weergave is handig voor een historische analyse op langere termijn van hoe uw SQL-database resources gebruikt.

De volgende grafiek toont het CPU-resourcegebruik voor een Premium-database met de P2-rekengrootte voor elk uur in een week. Deze grafiek begint op een maandag, toont 5 werkdagen en toont vervolgens een weekend, wanneer er veel minder gebeurt op de toepassing.

![SQL-databasebrongebruik](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Uit de gegevens, deze database heeft momenteel een piek CPU belasting van iets meer dan 50 procent CPU-gebruik ten opzichte van de P2 compute size (12.00 uur op dinsdag). Als CPU de dominante factor is in het resourceprofiel van de toepassing, u besluiten dat P2 de juiste rekengrootte is om te garanderen dat de werkbelasting altijd past. Als u verwacht dat een toepassing na verloop van tijd zal groeien, is het een goed idee om een extra bronbuffer te hebben, zodat de toepassing nooit de limiet voor prestatieniveau bereikt. Als u de rekengrootte verhoogt, u klantzichtbare fouten voorkomen die kunnen optreden wanneer een database niet genoeg stroom heeft om aanvragen effectief te verwerken, vooral in latentiegevoelige omgevingen. Een voorbeeld is een database die een toepassing ondersteunt die webpagina's schildert op basis van de resultaten van databasecalls.

Andere toepassingstypen kunnen dezelfde grafiek anders interpreteren. Als een toepassing bijvoorbeeld elke dag salarisgegevens probeert te verwerken en dezelfde grafiek heeft, kan dit soort 'batchjob'-model het prima doen op een P1-rekengrootte. De P1 compute size heeft 100 DT's in vergelijking met 200 DTU's op de P2 compute size. De P1-rekengrootte biedt de helft van de prestaties van de P2-rekengrootte. Dus, 50 procent van het CPU-gebruik in P2 is gelijk aan 100 procent CPU-gebruik in P1. Als de toepassing geen time-outs heeft, maakt het misschien niet uit of een taak 2 uur of 2,5 uur duurt om te voltooien, als het vandaag wordt gedaan. Een toepassing in deze categorie kan waarschijnlijk een P1-rekengrootte gebruiken. U profiteren van het feit dat er perioden van tijd gedurende de dag wanneer het gebruik van hulpbronnen lager is, zodat een grote piek zou kunnen overslaan in een van de troggen later op de dag. De P1-rekengrootte kan goed zijn voor dat soort toepassingen (en geld besparen), zolang de taken elke dag op tijd kunnen worden voltooid.

Azure SQL Database legt verbruikte brongegevens bloot voor elke actieve database in de **sys.resource_stats** weergave van de **hoofddatabase** in elke server. De gegevens in de tabel worden samengevoegd voor intervallen van 5 minuten. Met de servicelagen Basic, Standard en Premium kunnen de gegevens meer dan 5 minuten duren voordat ze in de tabel worden weergegeven, zodat deze gegevens nuttiger zijn voor historische analyse in plaats van bijna realtime analyse. Vraag de **sys.resource_stats** weergave om de recente geschiedenis van een database te bekijken en om te valideren of de door u gekozen reservering de gewenste prestaties heeft geleverd wanneer dat nodig is.

> [!NOTE]
> U moet zijn verbonden met de **hoofddatabase** van uw SQL Database-server om **sys.resource_stats** in de volgende voorbeelden op te vragen.

In dit voorbeeld ziet u hoe de gegevens in deze weergave worden weergegeven:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![De catalogusweergave sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

In het volgende voorbeeld ziet u verschillende manieren waarop u de **catalogusweergave sys.resource_stats** gebruiken om informatie te krijgen over hoe uw SQL-database resources gebruikt:

1. Als u wilt kijken naar het resourcegebruik van de afgelopen week voor de databaseuserdb1, u deze query uitvoeren:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Om te evalueren hoe goed uw werkbelasting past bij de rekengrootte, moet u inzoomen op elk aspect van de resourcestatistieken: CPU, leest, schrijft, aantal werknemers en het aantal sessies. Hier vindt u een herziene query met behulp van **sys.resource_stats** om de gemiddelde en maximale waarden van deze resourcestatistieken te rapporteren:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Met deze informatie over de gemiddelde en maximale waarden van elke resourcestatistiek u beoordelen hoe goed uw werkbelasting past in de door u gekozen rekengrootte. Meestal geven gemiddelde waarden van **sys.resource_stats** u een goede basislijn om te gebruiken ten opzichte van de doelgrootte. Het moet je primaire meetstok zijn. Voor een voorbeeld gebruikt u mogelijk de standaardservicelaag met de rekengrootte van S2. De gemiddelde gebruikspercentages voor CPU- en IO-reads en -schrijfbewerkingen liggen onder de 40 procent, het gemiddelde aantal werknemers ligt onder de 50 en het gemiddelde aantal sessies ligt onder de 200. Uw werkbelasting past mogelijk in de s1-compute-grootte. Het is eenvoudig om te zien of uw database past in de werk- en sessielimieten. Als u wilt zien of een database past in een lagere rekengrootte met betrekking tot CPU, leest en schrijft, verdeelt u het DTU-nummer van de lagere rekengrootte door het DTU-nummer van uw huidige rekengrootte en vermenigvuldigt u het resultaat met 100:

    `S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40`

    Het resultaat is het relatieve prestatieverschil tussen de twee rekengroottes in percentage. Als uw resourcegebruik dit bedrag niet overschrijdt, past uw werkbelasting mogelijk in de lagere rekengrootte. U moet echter alle bereiken van resourcegebruikswaarden bekijken en per percentage bepalen hoe vaak uw databasewerkbelasting in de lagere rekengrootte past. Met de volgende query wordt het geschiktheidspercentage per resourcedimensie uitgevoerd op basis van de drempelwaarde van 40 procent die we in dit voorbeeld hebben berekend:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Op basis van uw databaseservicelaag u bepalen of uw werkbelasting past in de lagere rekengrootte. Als de doelstelling voor de werkbelasting van uw database 99,9 procent bedraagt en de voorgaande querywaarden van meer dan 99,9 procent oplevert voor alle drie de resourcedimensies, past uw werkbelasting waarschijnlijk in de lagere rekengrootte.

    Kijkend naar het fit percentage geeft u ook inzicht in de vraag of u moet verhuizen naar de volgende hogere rekengrootte om uw doelstelling te bereiken. Gebruikersdb1 toont bijvoorbeeld het volgende CPU-gebruik van de afgelopen week:

   | Gemiddeld CPU-percentage | Maximaal CPU-percentage |
   | --- | --- |
   | 24.5 |100,00 |

    De gemiddelde CPU is ongeveer een kwart van de limiet van de compute size, die goed zou passen in de rekengrootte van de database. De maximale waarde geeft echter aan dat de database de limiet van de rekengrootte bereikt. Moet u naar de volgende hogere rekengrootte gaan? Kijk hoe vaak uw werkbelasting 100 procent bereikt en vergelijk deze vervolgens met de doelstelling voor de werkbelasting van uw database.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Als deze query een waarde van minder dan 99,9 procent retourneert voor een van de drie resourcedimensies, u overwegen om naar de volgende hogere rekengrootte over te gaan of toepassingstuningtechnieken gebruiken om de belasting op de SQL-database te verminderen.

4. Bij deze oefening wordt ook rekening gehouden met uw verwachte toename van de werklast in de toekomst.

Voor elastische pools kunt u afzonderlijke databases in de pool bewaken met de technieken die in deze sectie zijn beschreven. U kunt echter ook de groep als geheel bewaken. Zie [Een elastische pool bewaken en beheren](sql-database-elastic-pool-manage-portal.md) voor meer informatie.

### <a name="maximum-concurrent-requests"></a>Maximale gelijktijdige aanvragen

Als u het aantal gelijktijdige aanvragen wilt zien, voert u deze Transact-SQL-query uit in uw SQL-database:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R;
```

Als u de werkbelasting van een on-premises SQL Server-database wilt analyseren, wijzigt u deze query om te filteren op de specifieke database die u wilt analyseren. Als u bijvoorbeeld een on-premises database met de naam MyDatabase hebt, retourneert deze Transact-SQL-query het aantal gelijktijdige aanvragen in die database:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R
INNER JOIN sys.databases D ON D.database_id = R.database_id
AND D.name = 'MyDatabase';
```

Dit is slechts een momentopname op een enkel moment in de tijd. Om een beter inzicht te krijgen in uw werkbelasting en vereisten voor gelijktijdige aanvragen, moet u in de loop van de tijd veel voorbeelden verzamelen.

### <a name="maximum-concurrent-logins"></a>Maximale gelijktijdige aanmeldingen

U uw gebruikers- en toepassingspatronen analyseren om een idee te krijgen van de frequentie van aanmeldingen. U ook real-world belastingen uitvoeren in een testomgeving om ervoor te zorgen dat u deze of andere limieten die we in dit artikel bespreken niet raakt. Er is geen enkele query of dynamische beheerweergave (DMV) die u gelijktijdige aanmeldingsaantallen of geschiedenis kan laten zien.

Als meerdere clients dezelfde verbindingstekenreeks gebruiken, verifieert de service elke aanmelding. Als 10 gebruikers tegelijkertijd verbinding maken met een database met dezelfde gebruikersnaam en wachtwoord, zouden er 10 gelijktijdige aanmeldingen zijn. Deze limiet geldt alleen voor de duur van de login en authenticatie. Als dezelfde 10 gebruikers achtereenvolgens verbinding maken met de database, zou het aantal gelijktijdige aanmeldingen nooit groter zijn dan 1.

> [!NOTE]
> Momenteel is deze limiet niet van toepassing op databases in elastische pools.

### <a name="maximum-sessions"></a>Maximale sessies

Als u het aantal huidige actieve sessies wilt zien, voert u deze Transact-SQL-query uit in uw SQL-database:

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections
```

Als u een on-premises SQL Server-workload analyseert, wijzigt u de query om zich te concentreren op een specifieke database. Met deze query u bepalen welke sessiebehoeften voor de database nodig zijn als u overweegt deze naar Azure SQL Database te verplaatsen.

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections C
INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
INNER JOIN sys.databases D ON (D.database_id = S.database_id)
WHERE D.name = 'MyDatabase'
```

Nogmaals, deze query's geven een aantal punten in de tijd terug. Als u na verloop van tijd meerdere monsters verzamelt, hebt u het beste inzicht in uw sessiegebruik.

Voor SQL Database-analyse u historische statistieken over sessies opvragen door de [sys.resource_stats-weergave](https://msdn.microsoft.com/library/dn269979.aspx) op te vragen en de **active_session_count** kolom te bekijken.

## <a name="monitoring-query-performance"></a>Queryprestaties controleren

Langzame of langlopende query's kunnen aanzienlijke systeembronnen verbruiken. In dit gedeelte wordt uitgelegd hoe u dynamische beheerweergaven gebruiken om een paar veelvoorkomende problemen met de queryprestaties op te sporen. Een ouder, maar nog steeds nuttig referentie voor het oplossen van problemen, is het artikel [Problemen met de probleemoplossing in SQL Server 2008](https://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) op Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Top N-query's zoeken

In het volgende voorbeeld wordt informatie geretourneerd over de vijf belangrijkste query's die zijn gerangschikt op gemiddelde CPU-tijd. In dit voorbeeld worden de query's samengevoegd op basis van hun queryhash, zodat logischerwijs gelijkwaardige query's worden gegroepeerd op basis van het cumulatieve resourceverbruik.

```sql
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
     MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
FROM sys.dm_exec_query_stats AS QS
CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Geblokkeerde query's controleren

Trage of langlopende query's kunnen bijdragen aan overmatig resourceverbruik en het gevolg zijn van geblokkeerde query's. De oorzaak van het blokkeren kan slecht toepassingsontwerp, slechte vraagplannen, het gebrek aan nuttige indexen, enzovoort zijn. U de sys.dm_tran_locks-weergave gebruiken om informatie te krijgen over de huidige vergrendelingsactiviteit in uw Azure SQL-database. Zie bijvoorbeeld [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) in SQL Server Books Online.

### <a name="monitoring-query-plans"></a>Queryplannen controleren

Een inefficiënt queryplan kan ook het CPU-verbruik verhogen. In het volgende voorbeeld wordt de [weergave sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) gebruikt om te bepalen welke query de meest cumulatieve CPU gebruikt.

```sql
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Zie ook

[Inleiding tot SQL Database](sql-database-technical-overview.md)
