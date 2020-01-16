---
title: Beheer baarheid en controle-query activiteit, resource gebruik
description: Meer informatie over de mogelijkheden die beschikbaar zijn voor het beheren en controleren van Azure SQL Data Warehouse. Gebruik de Azure Portal en dynamische beheer weergaven (Dmv's) om inzicht te krijgen in de query-activiteiten en het resource gebruik van uw data warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 01/14/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 366d170a4caf9ee7428b68d71f910c65356038ff
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76024536"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Resource gebruik en query activiteit bewaken in Azure SQL Data Warehouse
Azure SQL Data Warehouse biedt een uitgebreide bewakings ervaring in de Azure Portal om inzicht te krijgen in de werk belasting van uw data warehouse. De Azure Portal is het aanbevolen hulp programma voor het bewaken van uw data warehouse, zoals het biedt Configureer bare Bewaar perioden, waarschuwingen, aanbevelingen en aanpas bare grafieken en dash boards voor metrische gegevens en Logboeken. De portal biedt u ook de mogelijkheid om te integreren met andere Azure-bewakings services zoals OMS (Operations Management Suite) en Azure Monitor (Logboeken) om een holistische bewakings ervaring te bieden voor niet alleen uw data warehouse, maar ook uw volledige Azure Analytics platform voor een geïntegreerde bewakings ervaring. In deze documentatie wordt beschreven welke bewakings mogelijkheden beschikbaar zijn voor het optimaliseren en beheren van uw analyse platform met SQL Data Warehouse. 

## <a name="resource-utilization"></a>Resourcegebruik 
De volgende metrische gegevens zijn beschikbaar in de Azure Portal voor SQL Data Warehouse. Deze metrische gegevens worden geoppereerd via [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).


| Naam van meetwaarde             | Beschrijving                                                  | Aggregatietype |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU-percentage          | CPU-gebruik in alle knoop punten voor het Data Warehouse      | Gem, min, Max    |
| Gegevens-I/O-percentage      | IO-gebruik over alle knoop punten voor het Data Warehouse       | Gem, min, Max    |
| Geheugen percentage       | Geheugen gebruik (SQL Server) op alle knoop punten voor het Data Warehouse | Gem, min, Max   |
| Actieve Query's          | Aantal actieve query's dat op het systeem wordt uitgevoerd             | Sum              |
| Query's in de wachtrij          | Aantal query's in de wachtrij dat wacht om te worden uitgevoerd          | Sum              |
| Geslaagde verbindingen  | Aantal geslaagde verbindingen met de gegevens                 | Som, aantal       |
| Mislukte verbindingen      | Aantal mislukte verbindingen met het Data Warehouse           | Som, aantal       |
| Geblokkeerd door de firewall     | Aantal aanmeldingen bij het data warehouse dat is geblokkeerd     | Som, aantal       |
| Limiet voor DWU               | Serviceniveau doelstelling van het Data Warehouse                | Gem, min, Max    |
| Percentage DWU          | Maximum tussen CPU-percentage en gegevens-i/o-percentage        | Gem, min, Max    |
| DWU gebruikt                | Limiet voor DWU * DWU percentage                                   | Gem, min, Max    |
| Percentage cache treffers    | (cache treffers/cache-missers) * 100 waarbij treffers in cache de som zijn van alle treffers in de lokale SSD-cache en de cache-Misser is de column Store-segmenten in de lokale SSD-cache die is opgeteld voor alle knoop punten | Gem, min, Max    |
| Percentage gebruikt cache   | (cache capaciteit gebruikt/cache) * 100 waarbij gebruikte cache de som is van alle bytes in de lokale SSD-cache op alle knoop punten en de cache capaciteit is de som van de opslag capaciteit van de lokale SSD-cache op alle knoop punten | Gem, min, Max    |
| Lokaal TempDB-percentage | Lokaal TempDB-gebruik voor alle reken knooppunten: de waarden worden elke vijf minuten verzonden | Gem, min, Max    |

Aandachtspunten bij het weer geven van metrische gegevens en het instellen van waarschuwingen:

- Er zijn mislukte en geslaagde verbindingen gerapporteerd voor een bepaald data warehouse, niet voor de logische server
- Het geheugen percentage weerspiegelt het gebruik, zelfs als het Data Warehouse zich in niet-actieve status bevindt, het actieve verbruik van het workload-geheugen niet weer spie gelen. Gebruik en volg deze metrische gegevens samen met anderen (TempDB, Gen2 cache) om een holistische beslissing te nemen over als het schalen voor extra cache capaciteit de prestaties van de werk belasting verg root om te voldoen aan uw vereisten.


## <a name="query-activity"></a>Query activiteit
Voor een programmatische ervaring bij het bewaken van SQL Data Warehouse via T-SQL, biedt de service een set dynamische beheer weergaven (Dmv's). Deze weer gaven zijn handig bij het oplossen van problemen met de prestaties en het identificeren van prestatie knelpunten met uw werk belasting.

Raadpleeg deze [documentatie](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs)als u de lijst met dmv's die SQL Data Warehouse biedt, wilt weer geven. 

## <a name="metrics-and-diagnostics-logging"></a>Registratie van metrische gegevens en diagnostische gegevens
Zowel metrische gegevens als logboeken kunnen worden geëxporteerd naar Azure Monitor, met name de [Azure monitor-logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) component en via een programma kunnen worden geopend via [logboek query's](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata). De logboek latentie voor SQL Data Warehouse is ongeveer 10-15 minuten. Raadpleeg de volgende documentatie voor meer informatie over de factoren die van invloed zijn op de latentie.


## <a name="next-steps"></a>Volgende stappen
De volgende hand leidingen beschrijven veelvoorkomende scenario's en use cases voor het bewaken en beheren van uw data warehouse:

- [Uw data warehouse-workload bewaken met Dmv's](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
