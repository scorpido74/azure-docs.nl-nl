---
title: Uw workload analyseren
description: Technieken voor het analyseren van queryprioritering voor uw werkbelasting in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 6a38fe65b4aedf4f594531f5e9cd8cf9b5dfaac7
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631234"
---
# <a name="analyze-your-workload-in-azure-synapse-analytics"></a>Uw werkbelasting analyseren in Azure Synapse Analytics

Technieken voor het analyseren van uw Synapse SQL-workload in Azure Synapse Analytics.

## <a name="resource-classes"></a>Resourceklassen

Synapse SQL biedt resourceklassen om systeembronnen toe te wijzen aan query's.  Zie [Resourceklassen & workloadmanagement](resource-classes-for-workload-management.md)voor meer informatie over resourceklassen.  Query's wachten als de resourceklasse die aan een query is toegewezen, meer resources nodig heeft dan momenteel beschikbaar is.

## <a name="queued-query-detection-and-other-dmvs"></a>Querydetectie in de wachtrij en andere DMVs

U `sys.dm_pdw_exec_requests` de DMV gebruiken om query's te identificeren die in een gelijktijdigheidswachtrij wachten. Query's die wachten op een gelijktijdigheidssleuf hebben de status **van opgeschort**.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

Functies voor werkbelastingbeheer `sys.database_principals`kunnen worden bekeken met .

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

In de volgende query wordt weergegeven aan welke rol elke gebruiker is toegewezen.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

Synapse SQL heeft de volgende wachttypen:

* **LocalQueriesConcurrencyResourceType:** query's die buiten het gelijktijdigheidssleufkader zitten. DMV-query's en `SELECT @@VERSION` systeemfuncties, zoals voorbeelden van lokale query's.
* **UserConcurrencyResourceType:** query's die binnen het gelijktijdigheidssleufkader zitten. Query's tegen tabellen voor eindgebruikers vertegenwoordigen voorbeelden die dit resourcetype zouden gebruiken.
* **DmsConcurrencyResourceType**: Wacht als gevolg van gegevensverplaatsingsbewerkingen.
* **BackupConcurrencyResourceType**: Deze wachttijd geeft aan dat er een back-up van een database wordt gemaakt. De maximale waarde voor dit resourcetype is 1. Als er meerdere back-ups tegelijk zijn aangevraagd, staan de andere back-ups in de wachtrij. In het algemeen raden we een minimale tijd aan tussen opeenvolgende momentopnamen van 10 minuten.

De `sys.dm_pdw_waits` DMV kan worden gebruikt om te zien op welke resources een aanvraag wacht.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

De `sys.dm_pdw_resource_waits` DMV toont de wachtinformatie voor een bepaalde query. Wachttijd voor resources meet de wachttijd voor resources die moeten worden verstrekt. De wachttijd van het signaal is de tijd die nodig is voor de onderliggende SQL-servers om de query op de CPU te plannen.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```

U ook `sys.dm_pdw_resource_waits` gebruik maken van de DMV berekenen hoeveel gelijktijdigheid slots zijn toegekend.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

De `sys.dm_pdw_wait_stats` DMV kan worden gebruikt voor historische trendanalyse van wachttijden.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Volgende stappen

Zie [Een database beveiligen in Synapse SQL](sql-data-warehouse-overview-manage-security.md)voor meer informatie over het beheren van databasegebruikers en beveiliging. Zie [Indexen opnieuw opbouwen om de segmentkwaliteit te verbeteren voor](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)meer informatie over hoe grotere resourceklassen de kwaliteit van de geclusterde kolomarchiefindex kunnen verbeteren.
