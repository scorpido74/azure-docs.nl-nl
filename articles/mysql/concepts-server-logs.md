---
title: Slow querylogs - Azure Database voor MySQL
description: Beschrijft de slow query-logboeken die beschikbaar zijn in Azure Database voor MySQL en de beschikbare parameters voor het inschakelen van verschillende logboeken.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 9a3a58cab2d9673a4660967e3a11d7f88900e718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269430"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Logboeken van langzame query's in Azure Database voor MySQL
In Azure Database voor MySQL is het logboek voor trage query beschikbaar voor gebruikers. Toegang tot het transactielogboek wordt niet ondersteund. Het logboek voor trage query's kan worden gebruikt om prestatieknelpunten voor het oplossen van problemen te identificeren.

Zie de [sectie slow query log](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)van de MySQL-naquery voor meer informatie over het slow query-logboek van MySQL.

## <a name="access-slow-query-logs"></a>Toegang tot logboeken voor langzame query's
U Azure Database voor MySQL-slow querylogs aanbieden en downloaden met behulp van de Azure-portal en de Azure CLI.

Selecteer in de Azure-portal uw Azure-database voor MySQL-server. Selecteer onder de kop **Controle** de pagina **Serverlogboeken.**

Zie [Logboeken voor langzame query's configureren en openen met Azure CLI](howto-configure-server-logs-in-cli.md)voor meer informatie over Azure CLI.

Op dezelfde manier u de logboeken naar Azure Monitor verzenden met diagnostische logboeken. Zie [hieronder](concepts-server-logs.md#diagnostic-logs) voor meer informatie.

## <a name="log-retention"></a>Logboekbehoud
Logs zijn beschikbaar voor maximaal zeven dagen na hun creatie. Als de totale grootte van de beschikbare logboeken meer dan 7 GB bedraagt, worden de oudste bestanden verwijderd totdat er ruimte beschikbaar is. 

Logs worden gedraaid om de 24 uur of 7 GB, wat het eerst komt.

## <a name="configure-slow-query-logging"></a>Logboekregistratie voor langzame query's configureren 
Standaard is het logboek van de langzame query uitgeschakeld. Als u deze wilt inschakelen, stelt u slow_query_log in op AAN.

Andere parameters die u aanpassen zijn:

- **long_query_time:** als een query langer duurt dan long_query_time (in seconden) wordt die query geregistreerd. De standaardinstelling is 10 seconden.
- **log_slow_admin_statements**: indien ON administratieve verklaringen zoals ALTER_TABLE en ANALYZE_TABLE opneemt in de aan de slow_query_log geschreven verklaringen.
- **log_queries_not_using_indexes**: bepaalt of query's die geen indexen gebruiken, worden geregistreerd op de slow_query_log
- **log_throttle_queries_not_using_indexes:** Deze parameter beperkt het aantal niet-indexquery's dat naar het logboek voor langzame query's kan worden geschreven. Deze parameter wordt van kracht wanneer log_queries_not_using_indexes is ingesteld op AAN.
- **log_output**: als "Bestand" toestaat, kan het logboek voor trage query's worden geschreven naar zowel de lokale serveropslag als naar diagnostische logboeken van Azure Monitor. Als 'Geen' wordt het logboek voor trage query alleen naar Azure Monitor Diagnostics Logs geschreven. 

> [!IMPORTANT]
> Als uw tabellen niet zijn `log_queries_not_using_indexes` geïndexeerd, kan het instellen van de parameters en `log_throttle_queries_not_using_indexes` parameters op AAN van invloed zijn op de prestaties van MySQL, omdat alle query's die worden uitgevoerd op deze niet-geïndexeerde tabellen, worden geschreven naar het logboek voor trage query's.<br><br>
> Als u van plan bent om voor een langere periode langzame `log_output` query's te registreren, wordt het aanbevolen om in te stellen op 'Geen'. Als deze is ingesteld op 'Bestand', worden deze logboeken naar de lokale serveropslag geschreven en kunnen ze de prestaties van MySQL beïnvloeden. 

Zie de documentatie van het [Slow Query-logboek](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) van MySQL voor volledige beschrijvingen van de parameters voor het logboek van langzame query's.

## <a name="diagnostic-logs"></a>Diagnostische logboeken
Azure Database voor MySQL is geïntegreerd met Azure Monitor Diagnostic Logs. Zodra u slow querylogboeken op uw MySQL-server hebt ingeschakeld, u ervoor kiezen deze uit te laten voeren naar Azure Monitor-logboeken, gebeurtenishubs of Azure Storage. Zie de sectie hoe u diagnostische logboeken inschakelen voor meer informatie over het inschakelen van diagnostische [logboeken.](../azure-monitor/platform/platform-logs-overview.md)

In de volgende tabel wordt beschreven wat er in elk logboek staat. Afhankelijk van de uitvoermethode kunnen de opgenomen velden en de volgorde waarin ze worden weergegeven, variëren.

| **Eigenschap** | **Beschrijving** |
|---|---|
| `TenantId` | Uw tenant-id |
| `SourceSystem` | `Azure` |
| `TimeGenerated`[UTC] | Tijdstempel toen het logboek werd opgenomen in UTC |
| `Type` | Type van het logboek. Altijd`AzureDiagnostics` |
| `SubscriptionId` | GUID voor het abonnement waartoe de server behoort |
| `ResourceGroup` | Naam van de brongroep waartoe de server behoort |
| `ResourceProvider` | Naam van de resourceprovider. Altijd`MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Naam van de server |
| `start_time_t`[UTC] | Tijd dat de query is gestart |
| `query_time_s` | Totale tijd in seconden die de query heeft uitgevoerd |
| `lock_time_s` | Totale tijd in seconden dat de query is vergrendeld |
| `user_host_s` | Gebruikersnaam |
| `rows_sent_s` | Aantal verzonden rijen |
| `rows_examined_s` | Aantal onderzochte rijen |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Id invoegen |
| `sql_text_s` | Volledige query |
| `server_id_s` | De id van de server |
| `thread_id_s` | Thread-id |
| `\_ResourceId` | Resource URI |

> [!Note]
> Voor `sql_text`, log zal worden afgekapt als het meer dan 2048 tekens.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Logboeken in Azure-monitorlogboeken analyseren

Zodra uw logboeken voor trage query's via diagnostische logboeken naar Azure Monitor Logs zijn gepipeteerd, u uw langzame query's verder analyseren. Hieronder vindt u enkele voorbeeldquery's om u op weg te helpen. Zorg ervoor dat u de onderstaande update met uw server naam.

- Query's langer dan 10 seconden op een bepaalde server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Top 5 langste query's op een bepaalde server weergeven

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Langzame query's samenvatten op minimale, maximale, gemiddelde en standaarddeviatiequerytijd op een bepaalde server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- De langzame querydistributie op een bepaalde server in kaart gebracht

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Query's langer dan 10 seconden weergeven op alle MySQL-servers met diagnostische logboeken ingeschakeld

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Volgende stappen
- [Slow querylogs configureren vanuit de Azure-portal](howto-configure-server-logs-in-portal.md)
- [Logboeken van langzame query's configureren vanuit azure cli](howto-configure-server-logs-in-cli.md).
