---
title: Slow querylogs - Azure Database voor MariaDB
description: Beschrijft de logboeken die beschikbaar zijn in Azure Database voor MariaDB en de beschikbare parameters voor het inschakelen van verschillende logboekniveaus.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: ffd4ab463080001dbab5b0ed9ece69c4b5f91382
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272080"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Logboeken van langzame query's in Azure-database voor MariaDB
In Azure Database voor MariaDB is het logboek voor trage query beschikbaar voor gebruikers. Toegang tot het transactielogboek wordt niet ondersteund. Het logboek voor trage query's kan worden gebruikt om prestatieknelpunten voor het oplossen van problemen te identificeren.

Zie de MariaDB-documentatie voor slow query log voor meer informatie over het logboek voor trage [query's.](https://mariadb.com/kb/en/library/slow-query-log-overview/)

## <a name="configure-slow-query-logging"></a>Logboekregistratie voor langzame query's configureren
Standaard is het logboek van de langzame query uitgeschakeld. Als u deze `slow_query_log` wilt inschakelen, stelt u in op AAN. Dit kan worden ingeschakeld met behulp van de Azure-portal of Azure CLI. 

Andere parameters die u aanpassen zijn:

- **long_query_time:** als een query langer duurt dan long_query_time (in seconden) wordt die query geregistreerd. De standaardinstelling is 10 seconden.
- **log_slow_admin_statements**: indien ON administratieve verklaringen zoals ALTER_TABLE en ANALYZE_TABLE opneemt in de aan de slow_query_log geschreven verklaringen.
- **log_queries_not_using_indexes**: bepaalt of query's die geen indexen gebruiken, worden geregistreerd op de slow_query_log
- **log_throttle_queries_not_using_indexes:** Deze parameter beperkt het aantal niet-indexquery's dat naar het logboek voor langzame query's kan worden geschreven. Deze parameter wordt van kracht wanneer log_queries_not_using_indexes is ingesteld op AAN.
- **log_output**: als "Bestand" toestaat, kan het logboek voor trage query's worden geschreven naar zowel de lokale serveropslag als naar diagnostische logboeken van Azure Monitor. Als 'Geen' wordt het logboek voor trage query alleen naar Azure Monitor Diagnostics Logs geschreven. 

> [!IMPORTANT]
> Als uw tabellen niet zijn `log_queries_not_using_indexes` geïndexeerd, kan het instellen van de parameters en `log_throttle_queries_not_using_indexes` parameters op AAN van invloed zijn op de prestaties van MariaDB, omdat alle query's die worden uitgevoerd op deze niet-geïndexeerde tabellen, worden geschreven naar het logboek voor trage query's.<br><br>
> Als u van plan bent om voor een langere periode langzame `log_output` query's te registreren, wordt het aanbevolen om in te stellen op 'Geen'. Als deze is ingesteld op 'Bestand', worden deze logboeken naar de lokale serveropslag geschreven en kunnen ze de prestaties van MariaDB beïnvloeden. 

Zie de documentatie van het [traag querylogboek](https://mariadb.com/kb/en/library/slow-query-log-overview/) van MariaDB voor volledige beschrijvingen van de parameters voor het logboek van langzame query's.

## <a name="access-slow-query-logs"></a>Toegang tot logboeken voor langzame query's
Er zijn twee opties voor toegang tot slow querylogs in Azure Database voor MariaDB: lokale serveropslag of Azure Monitor Diagnostic Logs. Dit wordt ingesteld `log_output` met behulp van de parameter.

Voor lokale serveropslag u slow querylogboeken aanbieden en downloaden met behulp van de Azure-portal of de Azure CLI. Navigeer in de Azure-portal naar uw server in de Azure-portal. Selecteer onder de kop **Controle** de pagina **Serverlogboeken.** Zie [Serverlogboeken configureren en openen met Azure CLI](howto-configure-server-logs-cli.md)voor meer informatie over Azure CLI. 

Met diagnostische logboeken voor Azure Monitor u slow querylogs doorgeven naar Azure Monitor Logs (Log Analytics), Azure Storage of Event Hubs. Zie [hieronder](concepts-server-logs.md#diagnostic-logs) voor meer informatie.

## <a name="local-server-storage-log-retention"></a>Behoud van lokale serveropslaglogboeken
Wanneer u zich aanmeldt bij de lokale opslag van de server, zijn logboeken maximaal zeven dagen na het maken ervan beschikbaar. Als de totale grootte van de beschikbare logboeken meer dan 7 GB bedraagt, worden de oudste bestanden verwijderd totdat er ruimte beschikbaar is.

Logs worden gedraaid om de 24 uur of 7 GB, wat het eerst komt.

> [!Note]
> De bovenstaande logboekretentie is niet van toepassing op logboeken die worden gepipeteerd met Azure Monitor Diagnostic Logs. U de bewaartermijn wijzigen voor de gegevenssinks die worden uitgestoten naar (bijvoorbeeld. Azure Storage).

## <a name="diagnostic-logs"></a>Diagnostische logboeken
Azure Database voor MariaDB is geïntegreerd met Azure Monitor Diagnostic Logs. Zodra u slow querylogboeken op uw MariaDB-server hebt ingeschakeld, u ervoor kiezen deze uit te laten voeren naar Azure Monitor-logboeken, gebeurtenishubs of Azure Storage. Zie de sectie hoe u diagnostische logboeken inschakelen voor meer informatie over het inschakelen van diagnostische [logboeken.](../azure-monitor/platform/platform-logs-overview.md)

In de volgende tabel wordt beschreven wat er in elk logboek staat. Afhankelijk van de uitvoermethode kunnen de opgenomen velden en de volgorde waarin ze worden weergegeven, variëren.

| **Eigenschap** | **Beschrijving** |
|---|---|
| `TenantId` | Uw tenant-id |
| `SourceSystem` | `Azure` |
| `TimeGenerated`[UTC] | Tijdstempel toen het logboek werd opgenomen in UTC |
| `Type` | Type van het logboek. Altijd`AzureDiagnostics` |
| `SubscriptionId` | GUID voor het abonnement waartoe de server behoort |
| `ResourceGroup` | Naam van de brongroep waartoe de server behoort |
| `ResourceProvider` | Naam van de resourceprovider. Altijd`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Naam van de server |
| `start_time_t`[UTC] | Tijd dat de query is gestart |
| `query_time_s` | Totale tijd die de query heeft uitgevoerd |
| `lock_time_s` | Totale tijd dat de query is vergrendeld |
| `user_host_s` | Gebruikersnaam |
| `rows_sent_s` | Aantal verzonden rijen |
| `rows_examined_s` | Aantal onderzochte rijen |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Id invoegen |
| `sql_text_s` | Volledige query |
| `server_id_s` | Server-id |
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

- Query's langer dan 10 seconden weergeven op alle MariaDB-servers met diagnostische logboeken ingeschakeld

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Volgende stappen
- [Slow querylogs configureren vanuit de Azure-portal](howto-configure-server-logs-portal.md)
- [Slow querylogs configureren vanuit de Azure CLI](howto-configure-server-logs-cli.md)
