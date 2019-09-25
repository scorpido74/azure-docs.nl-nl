---
title: Server logboeken voor Azure Database for MariaDB
description: Hierin worden de Logboeken beschreven die beschikbaar zijn in Azure Database for MariaDB en de beschik bare para meters voor het inschakelen van verschillende registratie niveaus.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 10dbd4d7fa838ee7f8a3f70b3caadb570877d685
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259973"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Trage query Logboeken in Azure Database for MariaDB
In Azure Database for MariaDB is het langzame query logboek beschikbaar voor gebruikers. Toegang tot het transactie logboek wordt niet ondersteund. Het logboek met trage query's kan worden gebruikt om prestatie knelpunten voor het oplossen van problemen te identificeren.

Zie de MariaDB-documentatie voor [langzame query logboeken](https://mariadb.com/kb/en/library/slow-query-log-overview/)voor meer informatie over het langzame query logboek.

## <a name="access-slow-query-logs"></a>Toegang tot langzame query logboeken
U kunt Azure Database for MariaDB langzame query logboeken weer geven en downloaden met behulp van de Azure Portal en de Azure CLI.

Selecteer uw Azure Database for MariaDB server in het Azure Portal. Selecteer de pagina **Server logboeken** onder de sectie **bewaking** .

Zie [Server logboeken configureren en openen met Azure cli](howto-configure-server-logs-cli.md)voor meer informatie over Azure cli.

## <a name="log-retention"></a>Bewaar periode logboek
Logboeken zijn Maxi maal zeven dagen beschikbaar vanaf het maken ervan. Als de totale grootte van de beschik bare logboeken groter is dan 7 GB, worden de oudste bestanden verwijderd totdat er ruimte beschikbaar is.

Logboeken worden elke 24 uur of 7 GB gedraaid, afhankelijk van wat het eerste komt.

## <a name="configure-slow-query-logging"></a>Langzame query logboek registratie configureren
Het logboek voor langzame query's is standaard uitgeschakeld. Als u deze wilt inschakelen, stelt u slow_query_log in op ON.

Andere para meters die u kunt aanpassen zijn onder andere:

- **long_query_time**: als een query langer duurt dan long_query_time (in seconden) dat de query wordt geregistreerd. De standaard waarde is 10 seconden.
- **log_slow_admin_statements**: als het gaat om beheer instructies zoals ALTER_TABLE en ANALYZE_TABLE in de instructies die naar de slow_query_log zijn geschreven.
- **log_queries_not_using_indexes**: bepaalt of query's die geen indexen gebruiken, worden geregistreerd in de slow_query_log
- **log_throttle_queries_not_using_indexes**: Met deze para meter wordt het aantal niet-index query's beperkt dat naar het langzame query logboek kan worden geschreven. Deze para meter wordt van kracht wanneer log_queries_not_using_indexes is ingesteld op ON.

Zie de documentatie voor MariaDB [langzame query logboeken](https://mariadb.com/kb/en/library/slow-query-log-overview/) voor volledige beschrijvingen van de para meters van de langzame query-Logboeken.

## <a name="diagnostic-logs"></a>Diagnostische logboeken
Azure Database for MariaDB is geïntegreerd met Azure Monitor Diagnostische logboeken. Zodra u langzame query logboeken op uw MariaDB-server hebt ingeschakeld, kunt u ervoor kiezen om deze aan Azure Monitor-logboeken, Event Hubs of Azure Storage uit te geven. Zie de sectie over de [documentatie van Diagnostische logboeken](../azure-monitor/platform/resource-logs-overview.md)voor meer informatie over het inschakelen van Diagnostische logboeken.

> [!IMPORTANT]
> Deze diagnostische functie voor Server Logboeken is alleen beschikbaar in de [prijs categorie](concepts-pricing-tiers.md)algemeen en geoptimaliseerd voor geheugen.

In de volgende tabel wordt de inhoud van elk logboek beschreven. Afhankelijk van de uitvoer methode worden de opgenomen velden en de volg orde waarin ze worden weer gegeven, mogelijk verschillend.

| **Eigenschap** | **Beschrijving** |
|---|---|
| `TenantId` | Uw Tenant-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated`UTC | Tijds tempel voor het vastleggen van het logboek in UTC |
| `Type` | Het type van het logboek. Altijd `AzureDiagnostics` |
| `SubscriptionId` | GUID voor het abonnement waartoe de server behoort |
| `ResourceGroup` | Naam van de resource groep waartoe de server behoort |
| `ResourceProvider` | De naam van de resource provider. Altijd `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource-URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Naam van de server |
| `start_time_t`UTC | Tijdstip waarop de query is gestart |
| `query_time_s` | De totale tijd die de query heeft uitgevoerd |
| `lock_time_s` | Totale tijd dat de query is vergrendeld |
| `user_host_s` | Gebruikersnaam |
| `rows_sent_s` | Aantal verzonden rijen |
| `rows_examined_s` | Aantal onderzochte rijen |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Invoeg-ID |
| `sql_text_s` | Volledige query |
| `server_id_s` | Server-id |
| `thread_id_s` | Thread-ID |
| `\_ResourceId` | Resource-URI |

## <a name="next-steps"></a>Volgende stappen
- [Het configureren en openen van server logboeken vanuit de Azure Portal](howto-configure-server-logs-portal.md).
