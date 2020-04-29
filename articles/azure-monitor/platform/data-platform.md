---
title: Azure Monitor gegevens platform | Microsoft Docs
description: Bewakings gegevens die door Azure Monitor worden verzameld, worden onderverdeeld in metrische waarden die lichter zijn en die vrijwel realtime scenario's en logboeken kunnen ondersteunen die worden gebruikt voor geavanceerde analyse.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 58f542238c952088777ed9809b57dae3cdb9cf12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457260"
---
# <a name="azure-monitor-data-platform"></a>Azure Monitor gegevens platform

Het inschakelen van de betrouw bare waarde voor de complexe reken omgevingen van vandaag waarop gedistribueerde toepassingen worden uitgevoerd die afhankelijk zijn van zowel Cloud-als on-premises Services, vereist het verzamelen van operationele gegevens uit elke laag en elk onderdeel van het gedistribueerde systeem. U moet in staat zijn om op deze gegevens dieper inzichten uit te voeren en deze samen te voegen in één glas venster met verschillende perspectieven ter ondersteuning van de talrijke belanghebbenden in uw organisatie.

[Azure monitor](../overview.md) verzamelt en integreert gegevens uit verschillende bronnen in een gemeen schappelijk gegevens platform waar het kan worden gebruikt voor analyse, visualisatie en waarschuwingen. Het biedt een consistente ervaring op het niveau van gegevens uit meerdere bronnen, waarmee u inzicht krijgt in al uw bewaakte resources en zelfs met gegevens uit andere services die hun gegevens in Azure Monitor opslaan.


![Overzicht van Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Waarneem bare gegevens in Azure Monitor
Metrische gegevens, logboeken en gedistribueerde traceringen worden meestal aangeduid als de drie pijlers van de waarneem baarheid. Dit zijn de verschillende soorten gegevens die een bewakings programma moet verzamelen en analyseren om voldoende waarneem bare informatie te bieden over een bewaakt systeem. De berekenings functie kan worden bereikt door gegevens van meerdere pijlers te correleren en gegevens samen te voegen over de volledige set van resources die worden bewaakt. Omdat Azure Monitor gegevens uit meerdere bronnen tegelijk opslaat, kunnen de gegevens worden gecorreleerd en geanalyseerd met behulp van een gemeen schappelijke set hulpprogram ma's. Daarnaast worden gegevens over meerdere Azure-abonnementen en-tenants gecorreleerd, naast het hosten van gegevens voor andere services.

Azure-resources genereren een aanzienlijke hoeveelheid bewakings gegevens. Azure Monitor consolideert deze gegevens samen met de bewaking van gegevens van andere bronnen in een meet-of Logboeken platform. Elk is geoptimaliseerd voor bepaalde bewakings scenario's en elk ondersteunt verschillende functies in Azure Monitor. Voor functies als gegevens analyse, visualisaties of waarschuwingen moet u inzicht krijgen in de verschillen zodat u uw vereiste scenario op de meest efficiënte en rendabele manier kunt implementeren. Inzichten in Azure Monitor zoals [Application Insights](../app/app-insights-overview.md) of [Azure monitor voor VM's](../insights/vminsights-overview.md) hulpprogram ma's voor analyse hebben waarmee u zich kunt richten op het specifieke bewakings scenario zonder dat u de verschillen tussen de twee typen gegevens hoeft te begrijpen. 


### <a name="metrics"></a>Metrische gegevens
[Metrische gegevens](data-platform-metrics.md) zijn numerieke waarden die een aspect van een systeem op een bepaald moment beschrijven. Ze worden regel matig verzameld en worden geïdentificeerd met een tijds tempel, een naam, een waarde en een of meer labels definiëren. Metrische gegevens kunnen worden geaggregeerd met behulp van verschillende algoritmen, vergeleken met andere metrische gegevens, en gedurende een bepaalde periode geanalyseerd. 

Metrische gegevens in Azure Monitor worden opgeslagen in een Data Base met een tijd reeks die is geoptimaliseerd voor het analyseren van tijdgebonden data. Dit maakt metrische gegevens bijzonder geschikt voor waarschuwingen en snelle detectie van problemen. Ze kunnen u vertellen hoe uw systeem wordt uitgevoerd, maar normaal gesp roken moet worden gecombineerd met Logboeken om de hoofd oorzaak van problemen te identificeren.

Metrische gegevens zijn beschikbaar voor interactieve analyse in de Azure Portal met [Azure Metrics Explorer](../platform/metrics-getting-started.md). Ze kunnen worden toegevoegd aan een [Azure-dash board](../learn/tutorial-app-dashboards.md) voor visualisatie in combi natie met andere gegevens en worden gebruikt voor vrijwel real time- [waarschuwingen](alerts-metric.md).

Lees meer over de metrische gegevens van Azure Monitor, inclusief de brongegevens in [metrieken in azure monitor](data-platform-metrics.md).

### <a name="logs"></a>Logboeken
[Logboeken](data-platform-logs.md) zijn gebeurtenissen die in het systeem hebben plaatsgevonden. Ze kunnen verschillende soorten gegevens bevatten en kunnen worden gestructureerd of vrije tekst met een tijds tempel. Ze kunnen sporadisch worden gemaakt als gebeurtenissen in de omgeving logboek vermeldingen genereren en een systeem onder zware belasting genereert normaal gesp roken meer logboek volume.

Logboeken in Azure Monitor worden opgeslagen in een Log Analytics werk ruimte die is gebaseerd op [Azure Data Explorer](/azure/data-explorer/) en die een krachtige analyse-engine en een [uitgebreide query taal](/azure/kusto/query/)biedt. Logboeken bevatten doorgaans voldoende informatie om te voorzien in de volledige context van het probleem dat wordt geïdentificeerd en zijn waardevol voor het identificeren van het hoofd geval van problemen.

> [!NOTE]
> Het is belang rijk om onderscheid te maken tussen Azure Monitor logboeken en bronnen van logboek gegevens in Azure. Gebeurtenissen op abonnements niveau in Azure worden bijvoorbeeld geschreven naar een [activiteiten logboek](platform-logs-overview.md) dat u kunt weer geven in het menu Azure monitor. De meeste resources schrijven operationele informatie naar een [bron logboek](platform-logs-overview.md) dat u kunt door sturen naar verschillende locaties. Azure Monitor-Logboeken is een platform voor gegevens registratie dat activiteiten logboeken en resource logboeken verzamelt samen met andere bewakings gegevens om een diepe analyse te bieden in uw hele set resources.


 U kunt [logboek query's](../log-query/log-query-overview.md) interactief gebruiken met [Log Analytics](../log-query/portals.md) in de Azure portal of de resultaten toevoegen aan een [Azure-dash board](../learn/tutorial-app-dashboards.md) voor visualisatie in combi natie met andere gegevens. U kunt ook [logboek waarschuwingen](alerts-log.md) maken waarmee een waarschuwing wordt geactiveerd op basis van de resultaten van een plannings query.

Lees meer over Azure Monitor-logboeken met inbegrip van de gegevens bronnen in de [Logboeken van Azure monitor](data-platform-logs.md).

### <a name="distributed-traces"></a>Gedistribueerde traceringen
Traceringen zijn reeksen gerelateerde gebeurtenissen die een gebruikers aanvraag volgen via een gedistribueerd systeem. Ze kunnen worden gebruikt om het gedrag van de toepassings code en de prestaties van verschillende trans acties te bepalen. Hoewel logboeken vaak worden gemaakt door afzonderlijke onderdelen van een gedistribueerd systeem, wordt door een tracering de werking en prestaties van uw toepassing in de hele set onderdelen gemeten.

Gedistribueerde tracering in Azure Monitor is ingeschakeld met de [Application INSIGHTS SDK](../app/distributed-tracing.md)en traceer gegevens worden opgeslagen met andere toepassings logboek gegevens die door Application Insights zijn verzameld. Dit maakt het beschikbaar voor dezelfde analyse hulpprogramma's als andere logboek gegevens, waaronder logboek query's, Dash boards en waarschuwingen.

Lees meer over gedistribueerde tracering bij [Wat is gedistribueerde](../app/distributed-tracing.md)tracering?.


## <a name="compare-azure-monitor-metrics-and-logs"></a>Azure Monitor metrische gegevens en logboeken vergelijken

In de volgende tabel worden metrische gegevens en Logboeken in Azure Monitor vergeleken.

| Kenmerk  | Metrische gegevens | Logboeken |
|:---|:---|:---|
| Voordelen | Licht gewicht en geschikt voor realtime scenario's zoals waarschuwingen. Ideaal voor snelle detectie van problemen. | Geanalyseerd met uitgebreide query taal. Ideaal voor een grondige analyse en het identificeren van de hoofd oorzaak. |
| Gegevens | Alleen numerieke waarden | Tekst of numerieke gegevens |
| Structuur | Standaardset eigenschappen, waaronder de voorbeeld tijd, de bron die wordt bewaakt, een numerieke waarde. Sommige metrische gegevens bevatten meerdere dimensies voor verdere definitie. | Unieke set eigenschappen, afhankelijk van het logboek type. |
| Verzameling | Verzameld met regel matige tussen pozen. | Kan sporadisch worden verzameld als gebeurtenissen een record activeren die moet worden gemaakt. |
| Openen in de Azure-portal | Metrics Explorer | Log Analytics |
| Gegevens bronnen bevatten | De metrische gegevens van het platform worden verzameld van Azure-resources.<br>Toepassingen die worden bewaakt door Application Insights.<br>Aangepast gedefinieerd door toepassing of API. | Toepassings-en bron Logboeken.<br>Bewakings oplossingen.<br>Agents en VM-extensies.<br>Toepassings aanvragen en-uitzonde ringen.<br>Azure Security Center.<br>Data Collector-API. |

## <a name="collect-monitoring-data"></a>Bewakings gegevens verzamelen
Verschillende [gegevens bronnen voor Azure monitor](data-sources.md) schrijven naar een log Analytics-werk ruimte (Logboeken) of de data base van de Azure monitor metriek (metrisch) of beide. Sommige bronnen schrijven rechtstreeks naar deze gegevens archieven, terwijl andere kunnen schrijven naar een andere locatie, zoals Azure Storage, en een bepaalde configuratie nodig heeft om Logboeken of metrieken te vullen. 

Bekijk de [metrische gegevens in azure monitor](data-platform-metrics.md) en [meldt zich aan bij Azure monitor](data-platform-logs.md) voor een vermelding van de verschillende bronnen die elk type vullen.


## <a name="stream-data-to-external-systems"></a>Gegevens streamen naar externe systemen
Naast het gebruik van de hulpprogram ma's in azure voor het analyseren van bewakings gegevens, hebt u mogelijk een vereiste om deze door te sturen naar een extern hulp programma, zoals een SIEM-product (Security Information and Event Management). Deze door sturing geschiedt doorgaans rechtstreeks vanuit bewaakte bronnen via [Azure Event hubs](/azure/event-hubs/). Sommige bronnen kunnen worden geconfigureerd om gegevens rechtstreeks naar een Event Hub te verzenden terwijl u een ander proces, zoals een logische app, kunt gebruiken om de vereiste gegevens op te halen. Zie [Azure monitoring-gegevens streamen naar een event hub voor gebruik door een extern hulp programma](stream-monitoring-data-event-hubs.md) voor meer informatie.



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [metrische gegevens vindt u in azure monitor](data-platform-metrics.md).
- Lees meer over de [Logboeken in azure monitor](data-platform-logs.md).
- Meer informatie over de [beschik bare bewakings gegevens](data-sources.md) voor verschillende bronnen in Azure.
