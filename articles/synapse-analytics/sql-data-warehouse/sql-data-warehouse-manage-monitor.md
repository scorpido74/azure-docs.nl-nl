---
title: Uw SQL-poolworkload bewaken met DMVs
description: Meer informatie over het bewaken van uw SQL-poolworkload van Azure Synapse Analytics en queryuitvoering met DMVs.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: cf6f25e8839ead5738eb7259cc4fccb674a4adea
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633197"
---
# <a name="monitor-your-azure-synapse-analytics-sql-pool-workload-using-dmvs"></a>Uw AZURE Synapse Analytics SQL-poolworkload bewaken met DMVs

In dit artikel wordt beschreven hoe u Dynamic Management Views (DMVs) gebruiken om uw werkbelasting te controleren, inclusief het onderzoeken van query-uitvoering in SQL-groep.

## <a name="permissions"></a>Machtigingen

Als u de DMVs in dit artikel wilt opvragen, hebt u **DE STATUS VAN DATABASE BEKIJKEN** of **DE** CONTROLE-machtiging nodig. Meestal is het verlenen **van VIEW DATABASE STATE** de voorkeursmachtiging omdat deze veel restrictiever is.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Verbindingen controleren

Alle aanmeldingen in uw datawarehouse worden geregistreerd op [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Deze DMV bevat de laatste 10.000 aanmeldingen.  De session_id is de primaire sleutel en wordt achtereenvolgens toegewezen voor elke nieuwe aanmelding.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Query-uitvoering controleren

Alle query's die in SQL-groep worden uitgevoerd, worden geregistreerd op [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Deze DMV bevat de laatste 10.000 query's uitgevoerd.  De request_id identificeert op unieke wijze elke query en is de primaire sleutel voor deze DMV.  De request_id wordt achtereenvolgens toegewezen voor elke nieuwe query en is vooraf bevestigd met QID, wat staat voor query-ID.  Als u deze dmv opvraagt voor een bepaald session_id worden alle query's voor een bepaalde aanmelding weergegeven.

> [!NOTE]
> Opgeslagen procedures maken gebruik van meerdere aanvraag-id's.  Aanvraag-id's worden in opeenvolgende volgorde toegewezen.

Hier volgen volgen stappen om queryuitvoeringsplannen en -tijden voor een bepaalde query te onderzoeken.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>STAP 1: De query identificeren die u wilt onderzoeken

```sql
-- Monitor active queries
SELECT *
FROM sys.dm_pdw_exec_requests
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 *
FROM sys.dm_pdw_exec_requests
ORDER BY total_elapsed_time DESC;

```

Noteer in de voorgaande queryresultaten **de aanvraag-id** van de query die u wilt onderzoeken.

Query's in de **status Opgeschort** kunnen in de wachtrij worden geplaatst vanwege een groot aantal actieve actieve query's. Deze query's worden ook weergegeven in de [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) wacht query met een type UserConcurrencyResourceType. Zie [Geheugen- en gelijktijdigheidslimieten of](memory-concurrency-limits.md) [Resourceklassen voor werkbelastingbeheer voor](resource-classes-for-workload-management.md)informatie over gelijktijdigheidslimieten . Query's kunnen ook wachten op andere redenen, zoals voor objectvergrendelingen.  Zie [Query's onderzoeken die wachten op bronnen](#monitor-waiting-queries) verderop in dit artikel als uw query op een resource wacht.

Als u het opzoeken van een query in de [tabel sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) wilt vereenvoudigen, gebruikt u [LABEL](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om een opmerking toe te wijzen aan uw query, die kan worden opgezocht in de sys.dm_pdw_exec_requests-weergave.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>STAP 2: Het queryplan onderzoeken

Gebruik de aanvraag-id om het gedistribueerde SQL-sql-abonnement (distributed SQL) van de query op te halen van [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Wanneer een DSQL-abonnement langer duurt dan verwacht, kan de oorzaak een complex plan zijn met veel DSQL-stappen of slechts één stap die lang duurt.  Als het plan veel stappen met verschillende verplaatsingsbewerkingen is, u overwegen uw tabeldistributies te optimaliseren om de gegevensverplaatsing te verminderen. In het artikel [tabeldistributie](sql-data-warehouse-tables-distribute.md) wordt uitgelegd waarom gegevens moeten worden verplaatst om een query op te lossen. Het artikel legt ook een aantal distributiestrategieën uit om gegevensverplaatsing te minimaliseren.

Als u meer details over één stap wilt onderzoeken, wordt in de *operation_type* kolom van de langlopende querystap en noteert u de **stapindex:**

* Ga verder met Stap 3a voor **SQL-bewerkingen:** OnOperation, RemoteOperation, ReturnOperation.
* Ga verder met stap 3b voor **bewerkingen van gegevensverkeer:** ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>STAP 3: SQL onderzoeken op de gedistribueerde databases

Gebruik de aanvraag-id en de stapindex om gegevens op te halen uit [sys.dm_pdw_sql_requests](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), die uitvoeringsgegevens van de querystap op alle gedistribueerde databases bevat.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Wanneer de querystap wordt uitgevoerd, kunnen [DBCC-PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) worden gebruikt om het sql-server-geschatte plan op te halen uit de SQL Server-plancache voor de stap die wordt uitgevoerd op een bepaalde distributie.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>STAP 4: Gegevensverplaatsing op de gedistribueerde databases onderzoeken

Gebruik de aanvraag-id en de stapindex om informatie over een stap voor gegevensverplaatsing die op elke distributie wordt uitgevoerd, op te halen via [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Controleer de *total_elapsed_time* kolom om te zien of een bepaalde distributie aanzienlijk langer duurt dan andere voor gegevensverplaatsing.
* Controleer voor de langlopende verdeling de *kolom rows_processed* om te zien of het aantal rijen dat uit die verdeling wordt verplaatst aanzienlijk groter is dan andere. Als dit het zo is, kan deze bevinding wijzen op scheeftrekking van uw onderliggende gegevens.

Als de query wordt uitgevoerd, u [DBCC-PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) gebruiken om het sql-server-geschatte plan op te halen uit de SQL Server-plancache voor de momenteel actieve SQL Step binnen een bepaalde distributie.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Wachtquery's bewaken

Als u ontdekt dat uw query geen vooruitgang boekt omdat deze wacht op een resource, is hier een query die alle resources weergeeft waarop een query wacht.

```sql
-- Find queries
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Als de query actief wacht op resources van een andere query, wordt de status **AcquireResources .**  Als de query alle vereiste resources heeft, wordt de status **verleend**.

## <a name="monitor-tempdb"></a>Monitor tempdb

Tempdb wordt gebruikt om tussentijdse resultaten te behouden tijdens de uitvoering van query's. Een hoog gebruik van de tempdb-database kan leiden tot trage queryprestaties. Voor elke DW100c geconfigureerd, 399 GB tempdb ruimte wordt toegewezen (DW1000c zou hebben 3,99 TB van de totale tempdb ruimte).  Hieronder vindt u tips voor het monitoren van tempdb gebruik en voor het verminderen van tempdb gebruik in uw zoekopdrachten.

### <a name="monitoring-tempdb-with-views"></a>Monitoring tempdb met views

Als u het gebruik van tempdb wilt controleren, installeert u eerst de [microsoft.vw_sql_requests-weergave](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) vanuit de [Microsoft Toolkit voor SQL-groep.](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring) Vervolgens u de volgende query uitvoeren om het tempdb-gebruik per knooppunt voor alle uitgevoerde query's te bekijken:

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa'
ORDER BY sr.request_id;
```

Als u een query hebt die een grote hoeveelheid geheugen verbruikt of een foutbericht hebt ontvangen met betrekking tot de toewijzing van tempdb, kan dit te wijten zijn aan een zeer grote [TABEL MAKEN ALS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) of [SELECT-instructie INVOEGEN](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) die niet optreedt in de bewerking van de uiteindelijke gegevensverplaatsing. Dit kan meestal worden geïdentificeerd als een ShuffleMove-bewerking in het gedistribueerde queryplan vlak voor de uiteindelijke INSERT SELECT.  Gebruik [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om shufflemove-bewerkingen te controleren.

De meest voorkomende beperking is om uw CTAS- of INSERT SELECT-instructie in meerdere belastingsinstructies te breken, zodat het gegevensvolume de tempdb-limiet van 1 TB per knooppunt niet overschrijdt. U uw cluster ook schalen naar een groter formaat dat de tempdb-grootte over meer knooppunten verspreidt, waardoor de tempdb op elk afzonderlijk knooppunt wordt verminderd.

Naast CTAS- en INSERT SELECT-instructies kunnen grote, complexe query's die met onvoldoende geheugen worden uitgevoerd, overlopen in tempdb waardoor query's mislukken.  Overweeg om met een grotere [resourceklasse](resource-classes-for-workload-management.md) te werken om te voorkomen dat er in tempdb wordt gemorst.

## <a name="monitor-memory"></a>Monitorgeheugen

Geheugen kan de hoofdoorzaak zijn voor trage prestaties en geheugenproblemen. Overweeg uw gegevensmagazijn te schalen als u merkt dat sql server-geheugengebruik zijn grenzen bereikt tijdens het uitvoeren van query's.

Met de volgende query retourneert SQL Server-geheugengebruik en geheugendruk per knooppunt:

```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB,
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```

## <a name="monitor-transaction-log-size"></a>Transactielogboekgrootte controleren

Met de volgende query wordt de grootte van het transactielogboek op elke distributie geretourneerd. Als een van de logboekbestanden 160 GB bereikt, u overwegen uw instantie op te schalen of de transactiegrootte te beperken.

```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id
FROM sys.dm_pdw_nodes_os_performance_counters
WHERE
instance_name like 'Distribution_%'
AND counter_name = 'Log File(s) Used Size (KB)'
```

## <a name="monitor-transaction-log-rollback"></a>Rollback van transactielogboeken controleren

Als uw query's mislukken of lang duren om door te gaan, u controleren en controleren of transacties worden teruggedraaid.

```sql
-- Monitor rollback
SELECT
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>PolyBase-belasting controleren

De volgende query geeft een schatting van de voortgang van uw belasting. De query toont alleen bestanden die momenteel worden verwerkt.

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>Volgende stappen

Zie [Systeemweergaven](sql-data-warehouse-reference-tsql-system-views.md)voor meer informatie over DMVs.
