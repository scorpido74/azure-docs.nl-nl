---
title: Workload bewaken - Azure-portal
description: Synapse SQL bewaken met behulp van de Azure-portal
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 9eacb813c3ddce028fcd9b24c86c6d32ed7a7584
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633231"
---
# <a name="monitor-workload---azure-portal"></a>Workload bewaken - Azure-portal

In dit artikel wordt beschreven hoe u de Azure-portal gebruiken om uw werkbelasting te controleren. Dit omvat het instellen van Azure Monitor Logs om query-uitvoering en workloadtrends te onderzoeken met behulp van loganalytics voor [Synapse SQL.](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
- SQL-pool: We verzamelen logboeken voor een SQL-groep. Als u geen SQL-groep hebt ingericht, raadpleegt u de instructies in [Een SQL-groep maken.](load-data-from-azure-blob-storage-using-polybase.md)

## <a name="create-a-log-analytics-workspace"></a>Een Log Analytics-werkruimte maken

Navigeer naar het bladerblad voor Log Analytics-werkruimten en maak een werkruimte

![Log Analytics-werkruimten](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Analytics-werkruimte toevoegen](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Analytics-werkruimte toevoegen](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Ga voor meer informatie over werkruimten naar de volgende [documentatie](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Diagnostische logboeken inschakelen

Configureer diagnostische instellingen om logboeken uit uw SQL-groep uit te zenden. Logboeken bestaan uit telemetrieweergaven die gelijkwaardig zijn aan de meest gebruikte prestatieprobleemoplossende DMVs. Momenteel worden de volgende weergaven ondersteund:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)

![Diagnostische logboeken inschakelen](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Logboeken kunnen worden uitgezonden naar Azure Storage, Stream Analytics of Log Analytics. Selecteer Logboekanalyse voor deze zelfstudie.

![Logboeken opgeven](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Query's uitvoeren tegen Logboekanalyse

Navigeer naar de werkruimte Log Analytics waar u het volgende doen:

- Logboeken analyseren met logboekquery's en query's opslaan voor hergebruik
- Query's opslaan voor hergebruik
- Logboekwaarschuwingen maken
- Queryresultaten vastmaken aan een dashboard

Ga voor meer informatie over de mogelijkheden van logboekquery's naar de volgende [documentatie](../../azure-monitor/log-query/query-language.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

![Editor van Log Analytics-werkruimte](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Query's voor logboekanalysewerkruimtes](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Voorbeeldlogboekquery's

```Kusto
//List all queries
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```

```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart
```

```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits"
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```

## <a name="next-steps"></a>Volgende stappen

Nu u Azure-monitorlogboeken hebt ingesteld en geconfigureerd, [kunt u Azure-dashboards aanpassen](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) om te delen in uw hele team.
