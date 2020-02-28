---
title: Overzicht van Azure Monitor | Microsoft Docs
description: Overzicht van Microsoft-services en -functionaliteiten die bijdragen aan een complete bewakingsstrategie voor uw Azure-services en -toepassingen.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: cec437929e2f7ccd94cf4cf94ec81f87dac2678a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669162"
---
# <a name="azure-monitor-overview"></a>Overzicht van Azure Monitor

Azure Monitor maximaliseert de beschik baarheid en prestaties van uw toepassingen en services door een uitgebreide oplossing te bieden voor het verzamelen, analyseren en uitvoeren van telemetrie in uw Cloud-en on-premises omgevingen. Het helpt u begrijpen hoe uw toepassingen presteren en stelt proactief problemen vast die betrekking hebben op de toepassingen en de resources waarvan ze afhankelijk zijn.

Er zijn slechts enkele voor beelden van wat u kunt doen met Azure Monitor:

- Detectie en diagnose van problemen in toepassingen en afhankelijkheden met [Application Insights](app/app-insights-overview.md).
- Correleer infrastructuur problemen met [Azure monitor voor VM's](insights/vminsights-overview.md) en [Azure monitor voor containers](insights/container-insights-overview.md).
- Bekijk uw bewakings gegevens met [log Analytics](log-query/log-query-overview.md) voor probleem oplossing en uitgebreide diagnose.
- Ondersteunings bewerkingen op schaal met [slimme waarschuwingen](platform/alerts-smartgroups-overview.md) en [geautomatiseerde acties](platform/alerts-action-rules.md).
- Maak visualisaties met Azure- [Dash boards](learn/tutorial-logs-dashboards.md) en- [werkmappen](app/usage-workbooks.md).

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Overzicht

Het volgende diagram toont een weer gave op hoog niveau van Azure Monitor. In het midden van het diagram vindt u de gegevens archieven voor metrieken en Logboeken. Dit zijn de twee basis typen gegevens die door Azure Monitor worden gebruikt. Aan de linkerkant ziet u de [bronnen van bewakings gegevens](platform/data-sources.md) waarmee deze [gegevens archieven](platform/data-platform.md)worden gevuld. Rechts zijn de verschillende functies die Azure Monitor worden uitgevoerd met deze verzamelde gegevens, zoals analyse, waarschuwingen en streaming naar externe systemen.

![Overzicht van Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Bewakings gegevens platform

Alle gegevens die worden verzameld door Azure Monitor, komen in een van de volgende twee fundamentele typen, [metrische gegevens en logboeken](platform/data-platform.md). [Metrische gegevens](platform/data-platform-metrics.md) zijn numerieke waarden die een aspect van een systeem op een bepaald moment beschrijven. Ze zijn lichtgewicht en kan in de buurt van realtime scenario's ondersteunen. [Logboeken](platform/data-platform-logs.md) bevatten verschillende soorten gegevens die zijn ingedeeld in records met verschillende sets eigenschappen voor elk type. Telemetrie, zoals gebeurtenissen en traceringen worden opgeslagen als Logboeken ook naar prestatiegegevens zodat deze kan allemaal worden gecombineerd voor analyse.

Voor veel Azure-resources ziet u de gegevens die worden verzameld door Azure Monitor rechts op de pagina overzicht in de Azure Portal. Bekijk een voor beeld van een virtuele machine, en u ziet verschillende grafieken die prestatie gegevens weer geven. Klik op een van de grafieken om de gegevens in [Metrics Explorer](platform/metrics-charts.md) te openen in de Azure Portal. Hiermee kunt u de waarden van meerdere metrieken in de loop van de tijd in een grafiek weer gegeven.  U kunt de grafieken interactief weergeven of vastmaken aan een dashboard om deze met andere visualisaties weer te geven.

![Metrische gegevens](media/overview/metrics.png)

De door Azure Monitor verzamelde logboek gegevens kunnen worden geanalyseerd met [query's](log-query/log-query-overview.md) om verzamelde gegevens snel op te halen, samen te voegen en te analyseren.  U kunt query's maken en testen met behulp van [log Analytics](log-query/portals.md) in de Azure Portal en vervolgens de gegevens rechtstreeks analyseren met behulp van deze hulpprogram ma's of query's opslaan voor gebruik met [Visualisaties](visualizations.md) of [waarschuwings regels](platform/alerts-overview.md).

Azure Monitor gebruikt een versie van de [Kusto-query taal](/azure/kusto/query/) die wordt gebruikt door Azure Data Explorer die geschikt is voor eenvoudige logboek query's, maar bevat ook geavanceerde functies zoals aggregaties, samen voegingen en slimme analyses. U kunt de query taal snel leren kennen met [meerdere lessen](log-query/get-started-queries.md).  Er worden specifieke richtlijnen gegeven voor gebruikers die al bekend zijn met [SQL](log-query/sql-cheatsheet.md) en [Splunk](log-query/splunk-cheatsheet.md).

![Logboeken](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Welke gegevens Azure Monitor verzamelen?

Azure Monitor kunt gegevens verzamelen van verschillende bronnen. U kunt de gegevens van uw toepassingen controleren in lagen die variëren van uw toepassing, alle besturings systemen en services waarvan het afhankelijk is, tot het platform zelf. Azure Monitor verzamelt gegevens uit elk van de volgende lagen:

- **Gegevens over toepassings bewaking**: gegevens over de prestaties en functionaliteit van de code die u hebt geschreven, ongeacht het platform.
- **Bewakings gegevens van gast besturingssysteem**: gegevens over het besturings systeem waarop uw toepassing wordt uitgevoerd. Dit kan worden uitgevoerd in azure, een andere Cloud of on-premises. 
- **Azure resource monitoring-gegevens**: gegevens over de werking van een Azure-resource.
- **Bewakings gegevens voor Azure-abonnementen**: gegevens over de werking en het beheer van een Azure-abonnement, evenals gegevens over de status en werking van Azure zelf. 
- **Azure-Tenant bewakings gegevens**: gegevens over de werking van Azure-Services op Tenant niveau, zoals Azure Active Directory.

Zodra u een Azure-abonnement maakt en resources zoals virtuele machines en web-apps toevoegt, Azure Monitor begint met het verzamelen van gegevens.  [Activiteiten logboeken](platform/platform-logs-overview.md) record wanneer resources worden gemaakt of gewijzigd. Met [metrische gegevens](platform/data-platform.md) kunt u zien hoe de resource wordt uitgevoerd en welke resources deze gebruiken. 

Breid de gegevens die u verzamelt, uit in de daad werkelijke bewerking van de resources door diagnostiek in te [scha kelen](platform/platform-logs-overview.md) en [een agent toe te voegen](platform/agent-windows.md) om resources te berekenen. Hiermee wordt telemetrie verzameld voor de interne bewerking van de resource en kunt u verschillende [gegevens bronnen](platform/agent-data-sources.md) configureren voor het verzamelen van Logboeken en metrieken van Windows en Linux-gast besturingssystemen. 

Schakel de bewaking in voor uw [app Services toepassing](app/azure-web-apps.md) of [virtuele machine en de toepassing voor de VM-schaalset](app/azure-vm-vmss-apps.md), zodat Application Insights gedetailleerde informatie over uw toepassing kunt verzamelen, waaronder pagina weergaven, toepassings aanvragen en uitzonde ringen. Controleer de beschik baarheid van uw toepassing door een [beschikbaarheids test](app/monitor-web-app-availability.md) te configureren om gebruikers verkeer te simuleren.

### <a name="custom-sources"></a>Aangepaste bronnen

Azure Monitor kunt logboek gegevens verzamelen van elke REST-client met behulp van de [Data Collector-API](platform/data-collector-api.md). Hierdoor kunt u aangepaste bewakings scenario's maken en de bewaking uitbreiden naar resources die geen telemetrie via andere bronnen beschikbaar stellen.

## <a name="insights"></a>Insights
Bewakings gegevens zijn alleen nuttig als u de zicht baarheid van uw computer omgeving kunt verg Roten. Azure Monitor bevat verschillende functies en hulpprogram ma's waarmee u inzicht krijgt in uw toepassingen en andere bronnen waarvan ze afhankelijk zijn. Het [bewaken van oplossingen](insights/solutions.md) en functies, zoals [Application Insights](app/app-insights-overview.md) en [Azure monitor voor containers](insights/container-insights-overview.md) , bieden uitgebreide inzichten in verschillende aspecten van uw toepassing en specifieke Azure-Services. 

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) bewaakt de beschik baarheid, prestaties en het gebruik van uw webtoepassingen, ongeacht of ze worden gehost in de Cloud of on-premises. De oplossing maakt gebruik van het krachtige platform voor gegevens analyse in Azure Monitor om u in staat te stellen om diep inzicht te krijgen in de bewerkingen van uw toepassing en fouten te onderzoeken zonder te hoeven wachten tot een gebruiker ze meldt. Application Insights bevat verbindings punten naar verschillende ontwikkel hulpprogramma's en integreert met Visual Studio om uw DevOps-processen te ondersteunen.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers
[Azure monitor voor containers](insights/container-insights-overview.md) is een functie die is ontworpen voor het bewaken van de prestaties van container werkbelastingen die zijn geïmplementeerd op beheerde Kubernetes-clusters die worden gehost op de Azure Kubernetes service (AKS). Hiermee krijgt u inzicht in de prestaties door het verzamelen van geheugen-en processor metrieken van controllers, knoop punten en containers die beschikbaar zijn in Kubernetes via de API voor metrische gegevens. Er worden ook containerlogboeken verzameld.  Nadat u bewaking van Kubernetes-clusters hebt ingeschakeld, worden deze metrische gegevens en logboeken automatisch voor u verzameld via een container versie van de Log Analytics-agent voor Linux.

![Container status](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor voor virtuele machines
[Azure monitor voor VM's](insights/vminsights-overview.md) bewaakt uw Azure virtual machines (VM) op schaal door de prestaties en status van uw Windows-en Linux-vm's te analyseren, inclusief de verschillende processen en onderling verbonden afhankelijkheden voor andere bronnen en externe processen. De oplossing bevat ondersteuning voor het bewaken van de prestatie-en toepassings afhankelijkheden voor Vm's die on-premises of een andere Cloud provider worden gehost.  


![VM Insights](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Bewakingsoplossingen
[Bewakings oplossingen](insights/solutions.md) in azure monitor zijn verpakte sets van logica die inzichten bieden voor een bepaalde toepassing of service. Ze omvatten logica voor het verzamelen van bewakings gegevens voor de toepassing of service, [query's](log-query/log-query-overview.md) voor het analyseren van die gegevens en [weer gaven](../log-analytics/log-analytics-view-designer.md) voor visualisatie. Bewakings oplossingen zijn [beschikbaar van micro soft](insights/solutions-inventory.md) en partners om bewaking te bieden voor verschillende Azure-Services en andere toepassingen.

![Bewakingsoplossingen](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Reageren op kritieke situaties
Naast de mogelijkheid om bewakings gegevens interactief te analyseren, moet een efficiënte bewakings oplossing proactief kunnen reageren op kritieke omstandigheden die in de verzamelde gegevens worden geïdentificeerd. Dit kan een tekst of e-mail bericht verzenden naar een beheerder die verantwoordelijk is voor het onderzoeken van een probleem. U kunt ook een geautomatiseerd proces starten dat probeert om een fout te corrigeren.


### <a name="alerts"></a>Waarschuwingen
[Waarschuwingen in azure monitor](platform/alerts-overview.md) proactief op de hoogte te stellen van kritieke omstandigheden en mogelijk proberen corrigerende actie te ondernemen. Waarschuwings regels op basis van metrische gegevens bieden bijna realtime waarschuwingen op basis van numerieke waarden, terwijl regels op basis van Logboeken complexe logica op gegevens uit meerdere bronnen toestaan.

Waarschuwings regels in Azure Monitor [actie groepen](platform/action-groups.md)gebruiken, die unieke sets ontvangers en acties bevatten die kunnen worden gedeeld in meerdere regels. Op basis van uw vereisten kunnen actie groepen dergelijke acties uitvoeren met behulp van webhooks zodat waarschuwingen externe acties starten of kunnen worden geïntegreerd met uw ITSM-hulpprogram ma's.

![Waarschuwingen](media/overview/alerts.png)

### <a name="autoscale"></a>Automatisch schalen
Met automatisch schalen kunt u de juiste hoeveelheid resources uitvoeren om de belasting van uw toepassing af te handelen. U kunt regels maken die gebruikmaken van metrische gegevens die worden verzameld door Azure Monitor om te bepalen wanneer resources automatisch moeten worden toegevoegd om de werk belasting te verwerken en ook geld besparen door resources te verwijderen die niet-actief zijn. U geeft een minimum en maximum aantal exemplaren op en de logica voor het verg Roten of verkleinen van resources.

![Automatisch schalen](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Bewakings gegevens visualiseren
[Visualisaties](visualizations.md) zoals grafieken en tabellen zijn effectief hulp middelen voor het samenvatten van bewakings gegevens en om deze te presen teren aan verschillende doel groepen. Azure Monitor heeft zijn eigen functies voor het visualiseren van bewakings gegevens en maakt gebruik van andere Azure-Services om deze te publiceren naar verschillende doel groepen.

### <a name="dashboards"></a>Dashboards
Met [Azure-Dash boards](../azure-portal/azure-portal-dashboards.md) kunt u verschillende soorten gegevens, met inbegrip van metrieken en Logboeken, combi neren in één deel venster in het [Azure Portal](https://portal.azure.com). U kunt het dash board eventueel delen met andere Azure-gebruikers. Elementen in Azure Monitor kunnen worden toegevoegd aan een Azure-dash board naast de uitvoer van een logboek query of metrische grafiek. U kunt bijvoorbeeld een dash board maken waarin tegels worden gecombineerd waarin een grafiek met metrische gegevens, een tabel met activiteiten logboeken, een gebruiks diagram van Application Insights en de uitvoer van een logboek query wordt weer gegeven.

![Dashboard](media/overview/dashboard.png)

### <a name="views"></a>Weergaven
[Weer gaven](../log-analytics/log-analytics-view-designer.md) geven logboek gegevens visueel presen teren in azure monitor.  Elke weer gave bevat één tegel die inzoomt op een combi natie van visualisaties, zoals staaf-en lijn diagrammen, en bevat een overzicht van belang rijke gegevens.  Bewakings oplossingen bevatten weer gaven waarin gegevens voor een bepaalde toepassing worden samenvatten, en u kunt uw eigen weer gaven maken om gegevens uit een logboek query weer te geven. Net als andere elementen in Azure Monitor kunnen weer gaven worden toegevoegd aan Azure-Dash boards.

![Weergave](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power bi](https://powerbi.microsoft.com) is een Business Analytics-service die interactieve visualisaties biedt in diverse gegevens bronnen. Dit is een doel matige manier om gegevens beschikbaar te maken voor anderen binnen en buiten uw organisatie. U kunt Power BI zodanig configureren dat [logboek gegevens van Azure monitor automatisch worden geïmporteerd](../log-analytics/log-analytics-powerbi.md) om te profiteren van deze extra visualisaties.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Gegevens integreren en exporteren
U hebt vaak de vereiste om Azure Monitor te integreren met andere systemen en om aangepaste oplossingen te bouwen die gebruikmaken van uw bewakings gegevens. Andere Azure-Services werken met Azure Monitor om deze integratie te bieden.

### <a name="event-hub"></a>Event Hub
[Azure Event hubs](https://docs.microsoft.com/azure/event-hubs) is een streaming-service voor het streamen van gegevens en het opnemen van gebeurtenissen, waarmee u in een real-time analyse provider of batch-en opslag adapters data kunt transformeren en opslaan. Gebruik Event Hubs om [Azure monitor gegevens te streamen](platform/stream-monitoring-data-event-hubs.md) naar Siem-en controle hulpprogramma's van de partner.


### <a name="logic-apps"></a>Logic Apps
[Logic apps](https://azure.microsoft.com/services/logic-apps) is een service waarmee u taken en bedrijfs processen kunt automatiseren met werk stromen die kunnen worden geïntegreerd met verschillende systemen en services. Er zijn activiteiten beschikbaar die metrische gegevens lezen en schrijven en Logboeken in Azure Monitor, waarmee u werk stromen kunt bouwen die zijn geïntegreerd met verschillende systemen.


### <a name="api"></a>API
Er zijn meerdere Api's beschikbaar voor het lezen en schrijven van metrische gegevens en logboeken van en naar Azure Monitor, naast de toegang tot gegenereerde waarschuwingen. U kunt ook waarschuwingen configureren en ophalen. Dit biedt u de mogelijkheid om aangepaste oplossingen te bouwen die met Azure Monitor worden geïntegreerd.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over:

* [Metrieken en logboeken](platform/data-platform.md) voor de gegevens die worden verzameld door Azure monitor.
* [Gegevens bronnen](platform/data-sources.md) voor het verzenden van telemetrie van de verschillende onderdelen van de toepassing.
* [Query's vastleggen](log-query/log-query-overview.md) voor het analyseren van verzamelde gegevens.
* [Aanbevolen procedures](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) voor het bewaken van Cloud toepassingen en-services.
