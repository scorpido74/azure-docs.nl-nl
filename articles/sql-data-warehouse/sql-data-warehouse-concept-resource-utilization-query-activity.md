---
title: Beheer baarheid en controle-query activiteit, resource gebruik
description: Meer informatie over de mogelijkheden die beschikbaar zijn voor het beheren en bewaken van Azure Synapse Analytics. Gebruik de Azure Portal en dynamische beheer weergaven (Dmv's) om inzicht te krijgen in de query-activiteiten en het resource gebruik van uw data warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 82bf6f9a78a46659cc2e0955895c6e1a6e6eb3aa
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096621"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Resource gebruik en query activiteit bewaken in azure Synapse Analytics
Azure Synapse Analytics biedt een uitgebreide bewakings ervaring in het Azure Portal op oppervlakte inzichten met betrekking tot de werk belasting van uw data warehouse. De Azure Portal is het aanbevolen hulp programma voor het bewaken van uw data warehouse, zoals het biedt Configureer bare Bewaar perioden, waarschuwingen, aanbevelingen en aanpas bare grafieken en dash boards voor metrische gegevens en Logboeken. De portal biedt u ook de mogelijkheid om te integreren met andere Azure-bewakings Services, zoals Azure Monitor (Logboeken) met log Analytics om een holistische bewakings ervaring te bieden voor niet alleen uw data warehouse, maar ook uw volledige Azure Analytics-platform voor een geïntegreerde bewakings ervaring. In deze documentatie wordt beschreven welke bewakings mogelijkheden beschikbaar zijn voor het optimaliseren en beheren van uw analyse platform met SQL Analytics. 

## <a name="resource-utilization"></a>Resourcegebruik 
De volgende metrische gegevens zijn beschikbaar in de Azure Portal voor SQL Analytics. Deze metrische gegevens worden geoppereerd via [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).


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
| Grootte van gegevens opslag | Totale hoeveelheid gegevens die in de data base is geladen. Dit omvat gegevens die zich bevinden in CCI-en niet-CCI-tabellen, waarbij de grootte van niet-CCI-tabellen wordt gemeten door de totale grootte van het database bestand | Sum |
| Grootte nood herstel | Totale grootte van de geo-back-up die elke 24 uur wordt gemaakt | Sum |
| Opslag grootte van moment opname | Totale grootte van moment opnamen die zijn gemaakt om database herstel punten op te geven. Dit omvat geautomatiseerde en door de gebruiker gedefinieerde moment opnamen. | Sum |

Aandachtspunten bij het weer geven van metrische gegevens en het instellen van waarschuwingen:

- DWU die wordt gebruikt, duidt alleen op een **hoog niveau van gebruik** in de SQL-groep en is niet bedoeld als een uitgebreide indicatie van het gebruik. Als u wilt bepalen of u omhoog of omlaag wilt schalen, moet u rekening houden met alle factoren die van invloed kunnen zijn op DWU zoals gelijktijdigheid, geheugen, tempdb en adaptieve cache capaciteit. We raden u [aan om uw werk belasting uit te voeren op verschillende DWU-instellingen](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview#finding-the-right-size-of-data-warehouse-units) om te bepalen wat het beste werkt om te voldoen aan uw bedrijfs doelstellingen.
- Er zijn mislukte en geslaagde verbindingen gerapporteerd voor een bepaald data warehouse, niet voor de logische server
- Het geheugen percentage weerspiegelt het gebruik, zelfs als het Data Warehouse zich in niet-actieve status bevindt, het actieve verbruik van het workload-geheugen niet weer spie gelen. Gebruik en volg deze metrische gegevens samen met anderen (TempDB, Gen2 cache) om een holistische beslissing te nemen over als het schalen voor extra cache capaciteit de prestaties van de werk belasting verg root om te voldoen aan uw vereisten.


## <a name="query-activity"></a>Query activiteit
Voor een programmatische ervaring bij het bewaken van SQL Analytics via T-SQL, biedt de service een set dynamische beheer weergaven (Dmv's). Deze weer gaven zijn handig bij het oplossen van problemen met de prestaties en het identificeren van prestatie knelpunten met uw werk belasting.

Raadpleeg deze [documentatie](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs)als u de lijst met DMV'S die SQL Analytics biedt, wilt weer geven. 

## <a name="metrics-and-diagnostics-logging"></a>Metrische gegevens en diagnoselogboeken
Zowel metrische gegevens als logboeken kunnen worden geëxporteerd naar Azure Monitor, met name de [Azure monitor-logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) component en via een programma kunnen worden geopend via [logboek query's](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata). De logboek latentie voor SQL Analytics is ongeveer 10-15 minuten. Raadpleeg de volgende documentatie voor meer informatie over de factoren die van invloed zijn op de latentie.


## <a name="next-steps"></a>Volgende stappen
De volgende hand leidingen beschrijven veelvoorkomende scenario's en use cases voor het bewaken en beheren van uw data warehouse:

- [Uw data warehouse-workload bewaken met Dmv's](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
