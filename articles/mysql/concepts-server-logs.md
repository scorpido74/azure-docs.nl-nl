---
title: Langzame query logboeken-Azure Database for MySQL
description: Beschrijft de logboeken voor trage query's die beschikbaar zijn in Azure Database for MySQL en de beschik bare para meters voor het inschakelen van verschillende registratie niveaus.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: cd0d09e4d46747b7f3f8e6fb714dd711beef9484
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770833"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Trage query Logboeken in Azure Database for MySQL
In Azure Database for MySQL is het langzame query logboek beschikbaar voor gebruikers. Toegang tot het transactie logboek wordt niet ondersteund. Het logboek met trage query's kan worden gebruikt om prestatie knelpunten voor het oplossen van problemen te identificeren.

Voor meer informatie over het MySQL-logboek voor langzame query's raadpleegt u de sectie voor het [langzame query logboek](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)van MySQL-verwijzing hand matig.

## <a name="access-slow-query-logs"></a>Toegang tot langzame query logboeken
U kunt Azure Database for MySQL langzame query logboeken weer geven en downloaden met behulp van de Azure Portal en de Azure CLI.

Selecteer uw Azure Database for MySQL server in het Azure Portal. Selecteer de pagina **Server logboeken** onder de sectie **bewaking** .

Zie voor meer informatie over Azure CLI [langzame query logboeken configureren en openen met behulp van Azure cli](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Bewaar periode logboek
Logboeken zijn Maxi maal zeven dagen beschikbaar vanaf het maken ervan. Als de totale grootte van de beschik bare logboeken groter is dan 7 GB, worden de oudste bestanden verwijderd totdat er ruimte beschikbaar is. 

Logboeken worden elke 24 uur of 7 GB gedraaid, afhankelijk van wat het eerste komt.

## <a name="configure-slow-query-logging"></a>Langzame query logboek registratie configureren 
Het logboek voor langzame query's is standaard uitgeschakeld. Als u deze functie wilt inschakelen, stelt u slow_query_log in op aan.

Andere para meters die u kunt aanpassen zijn onder andere:

- **long_query_time**: als een query langer duurt dan long_query_time (in seconden) dat de query wordt geregistreerd. De standaard waarde is 10 seconden.
- **log_slow_admin_statements**: als u beheer instructies zoals ALTER_TABLE en ANALYZE_TABLE in de instructies bevat die naar de slow_query_log zijn geschreven.
- **log_queries_not_using_indexes**: bepaalt of query's die geen indexen gebruiken, worden geregistreerd in het slow_query_log
- **log_throttle_queries_not_using_indexes**: deze para meter beperkt het aantal niet-index query's dat naar het langzame query logboek kan worden geschreven. Deze para meter wordt van kracht als log_queries_not_using_indexes is ingesteld op aan.

> [!Note]
> Voor `sql_text`wordt het logboek afgekapt als dit 2048 tekens overschrijdt.

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
| `TimeGenerated` [UTC] | Tijds tempel voor het vastleggen van het logboek in UTC |
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
| `start_time_t` [UTC] | Tijdstip waarop de query is gestart |
| `query_time_s` | De totale tijd in seconden die de query heeft geduurd om uit te voeren |
| `lock_time_s` | Totale tijd in seconden dat de query is vergrendeld |
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
