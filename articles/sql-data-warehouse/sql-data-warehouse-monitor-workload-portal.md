---
title: Workload bewaken-Azure Portal
description: Azure SQL Data Warehouse bewaken met behulp van de Azure Portal
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 73d837c34dd5a480cae08a41c89939414899052a
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645637"
---
# <a name="monitor-workload---azure-portal"></a>Workload bewaken-Azure Portal

In dit artikel wordt beschreven hoe u de Azure Portal kunt gebruiken om uw workload te bewaken. Dit omvat het instellen van Azure Monitor logboeken voor het onderzoeken van de uitvoering van query's en het analyseren van werk belasting met behulp van log Analytics voor [Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
- Azure SQL Data Warehouse: er worden logboeken verzameld voor een SQL Data Warehouse. Als u geen SQL Data Warehouse hebt ingericht, raadpleegt u de instructies in [Create a SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Een Log Analytics-werk ruimte maken

Ga naar de Blade bladeren voor Log Analytics-werk ruimten en maak een werk ruimte 

![Log Analytics-werkruimten](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Analytics-werk ruimte toevoegen](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Analytics-werk ruimte toevoegen](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Raadpleeg de volgende [documentatie](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace#create-a-workspace)voor meer informatie over werk ruimten.

## <a name="turn-on-diagnostic-logs"></a>Diagnostische logboeken inschakelen 

Diagnostische instellingen configureren voor het verzenden van logboeken van uw SQL Data Warehouse. Logboeken bestaan uit telemetriegegevens van uw data warehouse die overeenkomen met de meest voorkomende prestatie problemen met Dmv's voor SQL Data Warehouse. Momenteel worden de volgende weer gaven ondersteund:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Diagnostische logboeken inschakelen](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Logboeken kunnen worden verzonden naar Azure Storage, Stream Analytics of Log Analytics. Voor deze zelf studie selecteert u Log Analytics.

![Logboeken opgeven](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Query's uitvoeren op Log Analytics

Navigeer naar uw Log Analytics-werk ruimte waar u het volgende kunt doen:

- Logboeken analyseren met behulp van logboek query's en query's opslaan voor hergebruik
- Query's opslaan voor hergebruik
- Logboekwaarschuwingen maken
- Query resultaten vastmaken aan een dash board

Raadpleeg de volgende [documentatie](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)voor meer informatie over de mogelijkheden van logboek query's.

![Log Analytics werkruimte editor](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Log Analytics werkruimte query's](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Voorbeeld logboek query's



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

Nu u de logboeken van Azure monitor hebt ingesteld en geconfigureerd, [past u Azure-Dash boards](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) aan om te delen in uw team.
