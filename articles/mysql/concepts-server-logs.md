---
title: Server logboeken voor Azure Database for MySQL
description: Beschrijft de logboeken voor trage query's die beschikbaar zijn in Azure Database for MySQL en de beschik bare para meters voor het inschakelen van verschillende registratie niveaus.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 4d801ada8fd8a8b35c71601d3ca274f26afb24f6
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262279"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Trage query Logboeken in Azure Database for MySQL
In Azure Database for MySQL is het langzame query logboek beschikbaar voor gebruikers. Toegang tot het transactie logboek wordt niet ondersteund. Het logboek met trage query's kan worden gebruikt om prestatie knelpunten voor het oplossen van problemen te identificeren.

Voor meer informatie over het MySQL-logboek voor langzame query's raadpleegt u de sectie voor het [langzame query logboek](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)van MySQL-verwijzing hand matig.

## <a name="access-slow-query-logs"></a>Toegang tot langzame query logboeken
U kunt Azure Database for MySQL langzame query logboeken weer geven en downloaden met behulp van de Azure Portal en de Azure CLI.

Selecteer uw Azure Database voor MySQL-server in de Azure-portal. Selecteer de pagina **Server logboeken** onder de sectie **bewaking** .

Zie [Server logboeken configureren en openen met Azure cli](howto-configure-server-logs-in-cli.md)voor meer informatie over Azure cli.

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

Zie de documentatie van het MySQL- [logboek voor langzame query's](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) voor volledige beschrijvingen van de para meters van de langzame query-Logboeken.

## <a name="diagnostic-logs"></a>Diagnostische logboeken
Azure Database for MySQL is geïntegreerd met Azure Monitor Diagnostische logboeken. Zodra u langzame query Logboeken hebt ingeschakeld op de MySQL-server, kunt u ervoor kiezen om deze te laten uitbrengen naar Azure Monitor-logboeken, Event Hubs of Azure Storage. Zie de sectie over de [documentatie van Diagnostische logboeken](../azure-monitor/platform/resource-logs-overview.md)voor meer informatie over het inschakelen van Diagnostische logboeken.

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
| `ResourceProvider` | De naam van de resource provider. Altijd `MICROSOFT.DBFORMYSQL` |
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
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Invoeg-ID |
| `sql_text_s` | Volledige query |
| `server_id_s` | De server-ID |
| `thread_id_s` | Thread-ID |
| `\_ResourceId` | Resource-URI |

## <a name="next-steps"></a>Volgende stappen
- Het [configureren en openen van server logboeken vanuit de Azure cli](howto-configure-server-logs-in-cli.md).
