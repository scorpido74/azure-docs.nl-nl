---
title: Azure Monitor-gegevensplatform | Microsoft Documenten
description: Monitoringgegevens die door Azure Monitor worden verzameld, worden gescheiden in statistieken die licht van gewicht zijn en in staat zijn om bijna realtime scenario's en logboeken te ondersteunen die worden gebruikt voor geavanceerde analyse.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 58f542238c952088777ed9809b57dae3cdb9cf12
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457260"
---
# <a name="azure-monitor-data-platform"></a>Azure Monitor-gegevensplatform

Om waarneembaarheid mogelijk te maken in de complexe computeromgevingen van vandaag met gedistribueerde toepassingen die afhankelijk zijn van zowel cloud- als on-premises services, moeten operationele gegevens worden verzameling van operationele gegevens van elke laag en elk onderdeel van het gedistribueerde systeem. U moet in staat zijn om diepgaande inzichten op deze gegevens uit te voeren en te consolideren in een enkel venster van glas met verschillende perspectieven ter ondersteuning van de veelheid van belanghebbenden in uw organisatie.

[Azure Monitor](../overview.md) verzamelt en verzamelt gegevens uit verschillende bronnen naar een gemeenschappelijk gegevensplatform waar deze kunnen worden gebruikt voor analyse, visualisatie en waarschuwingen. Het biedt een consistente ervaring bovenop gegevens uit meerdere bronnen, die u diepgaande inzichten geeft in al uw bewaakte bronnen en zelfs met gegevens van andere services die hun gegevens opslaan in Azure Monitor.


![Overzicht van Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Waarneembaarheidsgegevens in Azure Monitor
Metrische gegevens, logboeken en gedistribueerde sporen worden algemeen aangeduid als de drie pijlers van waarneembaarheid. Dit zijn de verschillende soorten gegevens die een monitoring tool moet verzamelen en analyseren om voldoende waarneembaarheid van een gecontroleerd systeem te bieden. Waarneembaarheid kan worden bereikt door gegevens van meerdere pijlers te correleren en gegevens te aggregeren over de gehele reeks resources die worden gecontroleerd. Omdat Azure Monitor gegevens uit meerdere bronnen samen opslaat, kunnen de gegevens worden gecorreleerd en geanalyseerd met behulp van een algemene set hulpprogramma's. Het correleert ook gegevens over meerdere Azure-abonnementen en tenants, naast hostinggegevens voor andere services.

Azure-bronnen genereren een aanzienlijke hoeveelheid bewakingsgegevens. Azure Monitor consolideert deze gegevens samen met het bewaken van gegevens uit andere bronnen in een metrics- of logboekenplatform. Elk is geoptimaliseerd voor bepaalde bewakingsscenario's en elk ondersteunt verschillende functies in Azure Monitor. Functies zoals gegevensanalyse, visualisaties of waarschuwingen vereisen dat u de verschillen begrijpt, zodat u uw vereiste scenario op de meest efficiënte en kosteneffectieve manier implementeren. Inzichten in Azure Monitor, zoals [Application Insights](../app/app-insights-overview.md) of Azure Monitor [voor VM's,](../insights/vminsights-overview.md) hebben analysetools waarmee u zich concentreren op het specifieke bewakingsscenario zonder dat u de verschillen tussen de twee typen gegevens hoeft te begrijpen. 


### <a name="metrics"></a>Metrische gegevens
[Statistieken](data-platform-metrics.md) zijn numerieke waarden die een bepaald aspect van een systeem op een bepaald moment beschrijven. Ze worden regelmatig verzameld en worden geïdentificeerd met een tijdstempel, een naam, een waarde en een of meer definiërende labels. Statistieken kunnen worden samengevoegd met behulp van verschillende algoritmen, in vergelijking met andere statistieken, en geanalyseerd op trends in de tijd. 

Statistieken in Azure Monitor worden opgeslagen in een database uit de tijdreeksdie is geoptimaliseerd voor het analyseren van tijdstempelgegevens. Dit maakt metrics bijzonder geschikt voor het waarschuwen en snel detecteren van problemen. Ze kunnen u vertellen hoe uw systeem presteert, maar moeten meestal worden gecombineerd met logboeken om de hoofdoorzaak van problemen te identificeren.

Statistieken zijn beschikbaar voor interactieve analyse in de Azure-portal met [Azure Metrics Explorer](../platform/metrics-getting-started.md). Ze kunnen worden toegevoegd aan een [Azure-dashboard](../learn/tutorial-app-dashboards.md) voor visualisatie in combinatie met andere gegevens en worden gebruikt voor near-real-time [waarschuwingen.](alerts-metric.md)

Lees meer over Azure Monitor Metrics, inclusief hun bronnen van gegevens in [Metrics in Azure Monitor](data-platform-metrics.md).

### <a name="logs"></a>Logboeken
[Logboeken](data-platform-logs.md) zijn gebeurtenissen die zich binnen het systeem hebben voorgedaan. Ze kunnen verschillende soorten gegevens bevatten en kunnen gestructureerd of vrije formuliertekst zijn met een tijdstempel. Ze kunnen sporadisch worden gemaakt als gebeurtenissen in de omgeving genereren log items, en een systeem onder zware belasting zal meestal genereren meer log volume.

Logboeken in Azure Monitor worden opgeslagen in een Log Analytics-werkruimte die is gebaseerd op [Azure Data Explorer,](/azure/data-explorer/) die een krachtige analyse-engine en [uitgebreide querytaal](/azure/kusto/query/)biedt. Logboeken bieden doorgaans voldoende informatie om de volledige context van het probleem dat wordt geïdentificeerd te bieden en zijn waardevol voor het identificeren van root-gevallen van problemen.

> [!NOTE]
> Het is belangrijk om onderscheid te maken tussen Azure Monitor Logs en bronnen van loggegevens in Azure. Gebeurtenissen op abonnementsniveau in Azure worden bijvoorbeeld geschreven naar een [activiteitenlogboek](platform-logs-overview.md) dat u weergeven in het menu Azure Monitor. De meeste bronnen schrijven operationele informatie naar een [resourcelogboek](platform-logs-overview.md) dat u naar verschillende locaties doorsturen. Azure Monitor Logs is een logboekgegevensplatform dat activiteitslogboeken en bronlogboeken verzamelt, samen met andere monitoringgegevens om diepgaande analyses te bieden over uw hele set resources.


 U interactief met [logboekquery's](../log-query/log-query-overview.md) werken met [Log Analytics](../log-query/portals.md) in de Azure-portal of de resultaten toevoegen aan een [Azure-dashboard](../learn/tutorial-app-dashboards.md) voor visualisatie in combinatie met andere gegevens. U ook [logboekwaarschuwingen](alerts-log.md) maken die een waarschuwing activeren op basis van de resultaten van een planningsquery.

Lees meer over Azure Monitor Logs inclusief hun bronnen van gegevens in [Logboeken in Azure Monitor](data-platform-logs.md).

### <a name="distributed-traces"></a>Gedistribueerde sporen
Traces zijn reeksgerelateerde gebeurtenissen die een gebruikersverzoek volgen via een gedistribueerd systeem. Ze kunnen worden gebruikt om het gedrag van de toepassingscode en de uitvoering van verschillende transacties te bepalen. Terwijl logboeken vaak worden gemaakt door afzonderlijke componenten van een gedistribueerd systeem, meet een trace de werking en prestaties van uw toepassing over de hele set componenten.

Gedistribueerde tracering in Azure Monitor is ingeschakeld met de [Application Insights SDK](../app/distributed-tracing.md)en traceergegevens worden opgeslagen met andere toepassingslogboekgegevens die zijn verzameld door Application Insights. Dit maakt het beschikbaar voor dezelfde analysetools als andere loggegevens, waaronder logboekquery's, dashboards en waarschuwingen.

Lees meer over distributed tracing bij [Wat is Distributed Tracing?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Azure Monitor-statistieken en -logboeken vergelijken

In de volgende tabel worden metrische gegevens en logboeken in Azure Monitor vergeleken.

| Kenmerk  | Metrische gegevens | Logboeken |
|:---|:---|:---|
| Voordelen | Lichtgewicht en in staat om bijna real-time scenario's zoals waarschuwen. Ideaal voor het snel opsporen van problemen. | Geanalyseerd met uitgebreide querytaal. Ideaal voor diepgaande analyse en het identificeren van de oorzaak. |
| Gegevens | Alleen numerieke waarden | Tekst- of numerieke gegevens |
| Structuur | Standaardset eigenschappen, waaronder voorbeeldtijd, resource die wordt gecontroleerd, een numerieke waarde. Sommige statistieken bevatten meerdere dimensies voor verdere definitie. | Unieke set eigenschappen, afhankelijk van het logboektype. |
| Verzameling | Verzameld op regelmatige tijdstippen. | Kan sporadisch worden verzameld als gebeurtenissen leiden tot een record worden gemaakt. |
| Openen in de Azure-portal | Metrics Explorer | Log Analytics |
| Gegevensbronnen omvatten | Platformstatistieken die zijn verzameld uit Azure-bronnen.<br>Toepassingen die worden gecontroleerd door Application Insights.<br>Aangepast gedefinieerd door toepassing of API. | Toepassings- en bronlogboeken.<br>Monitoring oplossingen.<br>Agents en VM-extensies.<br>Toepassingsaanvragen en uitzonderingen.<br>Azure Security Center.<br>API voor gegevensverzamelaar. |

## <a name="collect-monitoring-data"></a>Bewakingsgegevens verzamelen
Verschillende [gegevensbronnen voor Azure Monitor](data-sources.md) worden geschreven naar een log analytics-werkruimte (logboeken) of de Azure Monitor-metrische gegevensdatabase (Metrische gegevens) of beide. Sommige bronnen schrijven rechtstreeks naar deze gegevensopslag, terwijl andere naar een andere locatie, zoals Azure-opslag, kunnen schrijven en een bepaalde configuratie vereisen om logboeken of statistieken in te vullen. 

Zie [Statistieken in Azure Monitor](data-platform-metrics.md) en [Logboeken in Azure Monitor](data-platform-logs.md) voor een lijst met verschillende gegevensbronnen die elk type vullen.


## <a name="stream-data-to-external-systems"></a>Gegevens naar externe systemen streamen
Naast het gebruik van de hulpprogramma's in Azure om bewakingsgegevens te analyseren, moet u deze mogelijk ook doorsturen naar een extern hulpprogramma, zoals een SIEM-product (security information and event management). Deze doorsturen gebeurt meestal rechtstreeks vanuit bewaakte bronnen via [Azure Event Hubs.](/azure/event-hubs/) Sommige bronnen kunnen worden geconfigureerd om gegevens rechtstreeks naar een gebeurtenishub te verzenden, terwijl u een ander proces gebruiken, zoals een Logische App om de vereiste gegevens op te halen. Zie [Azure-bewakingsgegevens streamen naar een gebeurtenishub voor verbruik door een extern hulpprogramma](stream-monitoring-data-event-hubs.md) voor meer informatie.



## <a name="next-steps"></a>Volgende stappen

- Lees meer over [Metrics in Azure Monitor](data-platform-metrics.md).
- Lees meer over [Logboeken in Azure Monitor](data-platform-logs.md).
- Meer informatie over de [bewakingsgegevens die beschikbaar zijn](data-sources.md) voor verschillende bronnen in Azure.
