---
title: Beheerbaarheid en bewaking - queryactiviteit, resourcegebruik
description: Ontdek welke mogelijkheden beschikbaar zijn voor het beheren en bewaken van Azure Synapse Analytics. Gebruik de Azure-portal en Dynamic Management Views (DMVs) om inzicht te krijgen in de queryactiviteit en het gebruik van resources in uw gegevensmagazijn.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 85457b62c905cb30ca22264eff1b6c677473f9b9
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745301"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Beheer van resource- en queryactiviteit in Azure Synapse Analytics controleren

Azure Synapse Analytics biedt een uitgebreide monitoringervaring binnen de Azure-portal om inzicht te geven in de werkbelasting van uw gegevensmagazijn. De Azure-portal is het aanbevolen hulpmiddel bij het bewaken van uw gegevensmagazijn, omdat het configureerbare bewaarperioden, waarschuwingen, aanbevelingen en aanpasbare grafieken en dashboards voor statistieken en logboeken biedt. De portal stelt u ook in staat om te integreren met andere Azure-bewakingsservices, zoals Azure Monitor (logboeken) met Logboekanalyses, zodat u niet alleen uw gegevensmagazijn, maar ook uw hele Azure-analyseplatform een geïntegreerde monitoringervaring biedt. In deze documentatie wordt beschreven welke bewakingsmogelijkheden beschikbaar zijn om uw analyseplatform te optimaliseren en te beheren met SQL Analytics.

## <a name="resource-utilization"></a>Resourcegebruik

De volgende statistieken zijn beschikbaar in de Azure-portal voor SQL Analytics. Deze statistieken worden weergegeven via [Azure Monitor](../../azure-monitor/platform/data-collection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#metrics).

| Metrische naam             | Beschrijving                                                  | Aggregatietype |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU-percentage          | CPU-gebruik voor alle knooppunten voor het gegevensmagazijn      | Avg, Min, Max    |
| Gegevens-I/O-percentage      | IO-gebruik voor alle knooppunten voor het gegevensmagazijn       | Avg, Min, Max    |
| Geheugenpercentage       | Geheugengebruik (SQL Server) voor alle knooppunten voor het gegevensmagazijn | Avg, Min, Max   |
| Actieve query's          | Aantal actieve query's dat op het systeem wordt uitgevoerd             | Sum              |
| Query's in de wachtrij          | Aantal query's in de wachtrij dat wacht om te beginnen met uitvoeren          | Sum              |
| Succesvolle verbindingen  | Aantal succesvolle verbindingen met de gegevens                 | Som, Aantal       |
| Mislukte verbindingen      | Aantal mislukte verbindingen met het gegevensmagazijn           | Som, Aantal       |
| Geblokkeerd door firewall     | Aantal aanmeldingen in het datawarehouse dat is geblokkeerd     | Som, Aantal       |
| DWU-limiet               | Doelstelling op serviceniveau van het datawarehouse                | Avg, Min, Max    |
| DWU-percentage          | Maximum tussen CPU-percentage en Data IO-percentage        | Avg, Min, Max    |
| Gebruikte DWU                | DWU-limiet * DWU-percentage                                   | Avg, Min, Max    |
| Percentage cachehit    | (cache hits / cache miss) * 100 waar cache hits is de som van alle columnstore segmenten hits in de lokale SSD cache en cache missen is de columnstore segmenten mist in de lokale SSD cache samengevat over alle knooppunten | Avg, Min, Max    |
| Door cache gebruikt percentage   | (gebruikte cache / cachecapaciteit) * 100 waar cache wordt gebruikt is de som van alle bytes in de lokale SSD-cache op alle knooppunten en cachecapaciteit is de som van de opslagcapaciteit van de lokale SSD-cache op alle knooppunten | Avg, Min, Max    |
| Lokaal tempdb percentage | Lokaal tempdb-gebruik voor alle compute nodes - waarden worden elke vijf minuten uitgestoten | Avg, Min, Max    |
| Grootte van de gegevensopslag (GB) | Totale grootte van de gegevens die in de database zijn geladen. Dit omvat gegevens die zich in CCI- en niet-CCI-tabellen bevinden, waarbij de grootte van niet-CCI-tabellen wordt gemeten aan de basis van de totale bestandsgrootte van de database | Sum |
| Grootte van noodherstel (GB) | Totale grootte van de geo-back-up genomen om de 24 uur | Sum |
| Grootte van momentopnameopslag (GB) | Totale grootte van momentopnamen die zijn gemaakt om databaseherstelpunten te bieden. Dit omvat geautomatiseerde en door de gebruiker gedefinieerde momentopnamen. | Sum |

Dingen om rekening mee te houden bij het bekijken van statistieken en het instellen van waarschuwingen:

- Gebruikte DWU vertegenwoordigt slechts een weergave op hoog niveau van het gebruik in de **SQL-groep** en is niet bedoeld als een uitgebreide indicator van het gebruik. Om te bepalen of u wilt opschalen of omlaag wilt, moet u rekening houden met alle factoren die door DWU kunnen worden beïnvloed, zoals gelijktijdigheid, geheugen, tempdb en adaptieve cachecapaciteit. We raden u aan uw werklast uit te [voeren op verschillende DWU-instellingen](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) om te bepalen wat het beste werkt om aan uw bedrijfsdoelstellingen te voldoen.
- Mislukte en succesvolle verbindingen worden gerapporteerd voor een bepaald gegevensmagazijn - niet voor de logische server
- Het geheugenpercentage weerspiegelt het gebruik, zelfs als het gegevensmagazijn niet actief is, maar weerspiegelt geen actief geheugenverbruik. Gebruik en houd deze statistiek samen met anderen (tempdb, gen2-cache) bij om een holistische beslissing te nemen over het niveau van schalen voor extra cachecapaciteit zal de prestaties van de werkbelasting verhogen om aan uw vereisten te voldoen.

## <a name="query-activity"></a>Queryactiviteit

Voor een programmatische ervaring bij het monitoren van SQL Analytics via T-SQL, biedt de service een set Dynamic Management Views (DMVs). Deze weergaven zijn handig bij het actief oplossen van problemen en het identificeren van prestatieknelpunten met uw werkbelasting.

Als u de lijst met DMVs wilt bekijken die SQL Analytics biedt, raadpleegt u deze [documentatie.](sql-data-warehouse-reference-tsql-system-views.md#sql-data-warehouse-dynamic-management-views-dmvs)

## <a name="metrics-and-diagnostics-logging"></a>Metrische gegevens en diagnoselogboeken

Zowel statistieken als logboeken kunnen worden geëxporteerd naar Azure Monitor, met name de azure [monitor-logboekencomponent](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) en kunnen programmatisch worden geopend via [logboekquery's.](../../azure-monitor/log-query/get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) De loglatentie voor SQL Analytics is ongeveer 10-15 minuten. Ga voor meer informatie over de factoren die van invloed zijn op de latentie naar de volgende documentatie.

## <a name="next-steps"></a>Volgende stappen

In de volgende handleidingen worden veelvoorkomende scenario's en use cases beschreven bij het bewaken en beheren van uw gegevensmagazijn:

- [Uw werkbelasting voor gegevensmagazijnen bewaken met DMVs](sql-data-warehouse-manage-monitor.md)
