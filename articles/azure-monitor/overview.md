---
title: Overzicht van Azure Monitor | Microsoft Documenten
description: Overzicht van Microsoft-services en -functionaliteiten die bijdragen aan een complete bewakingsstrategie voor uw Azure-services en -toepassingen.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: cfdd0beac7d257a424d327df71602b4612c3da3b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79536850"
---
# <a name="azure-monitor-overview"></a>Overzicht van Azure Monitor

Azure Monitor maximaliseert de beschikbaarheid en prestaties van uw toepassingen en services door een uitgebreide oplossing te bieden voor het verzamelen, analyseren en handelen op telemetrie vanuit uw cloud- en on-premises omgevingen. Het helpt u begrijpen hoe uw toepassingen presteren en stelt proactief problemen vast die betrekking hebben op de toepassingen en de resources waarvan ze afhankelijk zijn.

Enkele voorbeelden van wat u met Azure Monitor doen, zijn:

- Detecteren en diagnosticeren van problemen in toepassingen en afhankelijkheden met [Application Insights.](app/app-insights-overview.md)
- Correleer infrastructuurproblemen met [Azure Monitor voor VM's](insights/vminsights-overview.md) en [Azure Monitor voor containers.](insights/container-insights-overview.md)
- Zoom in op uw bewakingsgegevens met [Log Analytics](log-query/log-query-overview.md) voor probleemoplossing en diepgaande diagnostiek.
- Ondersteuning op schaal met [slimme waarschuwingen](platform/alerts-smartgroups-overview.md) en [geautomatiseerde acties.](platform/alerts-action-rules.md)
- Maak visualisaties met [Azure-dashboards](learn/tutorial-logs-dashboards.md) en [werkmappen](app/usage-workbooks.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Overzicht

Het volgende diagram toont een weergave op hoog niveau van Azure Monitor. In het midden van het diagram bevinden zich de gegevensopslag voor metrische gegevens en logboeken, de twee fundamentele typen gegevensgebruik door Azure Monitor. Aan de linkerkant zijn de [bronnen van monitoringgegevens](platform/data-sources.md) die deze [gegevensopslag](platform/data-platform.md)vullen. Aan de rechterkant bevinden zich de verschillende functies die Azure Monitor uitvoert met deze verzamelde gegevens, zoals analyse, waarschuwing en streaming naar externe systemen.

![Overzicht van Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Monitoring dataplatform

Alle gegevens die door Azure Monitor worden verzameld, passen in een van de twee fundamentele [typen, statistieken en logboeken.](platform/data-platform.md) [Statistieken](platform/data-platform-metrics.md) zijn numerieke waarden die een bepaald aspect van een systeem op een bepaald moment beschrijven. Ze zijn licht van gewicht en kunnen bijna realtime scenario's ondersteunen. [Logboeken](platform/data-platform-logs.md) bevatten verschillende soorten gegevens die zijn ingedeeld in records met verschillende sets eigenschappen voor elk type. Telemetrie zoals gebeurtenissen en sporen worden opgeslagen als logboeken naast prestatiegegevens, zodat het allemaal kan worden gecombineerd voor analyse.

Voor veel Azure-bronnen ziet u gegevens die door Azure Monitor zijn verzameld, direct op de overzichtspagina in de Azure-portal. Bekijk bijvoorbeeld een virtuele machine en je ziet verschillende grafieken met prestatiestatistieken. Klik op een van de grafieken om de gegevens in [metrics explorer](platform/metrics-charts.md) in de Azure-portal te openen, waarmee u de waarden van meerdere statistieken in de loop van de tijd in kaart brengen.  U de grafieken interactief bekijken of vastmaken aan een dashboard om ze te bekijken met andere visualisaties.

![Metrische gegevens](media/overview/metrics.png)

Logboekgegevens die door Azure Monitor worden verzameld, kunnen worden geanalyseerd met [query's](log-query/log-query-overview.md) om verzamelde gegevens snel op te halen, te consolideren en te analyseren.  U query's maken en testen met Behulp van [Log Analytics](log-query/portals.md) in de Azure-portal en vervolgens de gegevens direct analyseren met behulp van deze hulpprogramma's of query's opslaan voor gebruik met [visualisaties](visualizations.md) of [waarschuwingsregels.](platform/alerts-overview.md)

Azure Monitor gebruikt een versie van de [Kusto-querytaal](/azure/kusto/query/) die wordt gebruikt door Azure Data Explorer en die geschikt is voor eenvoudige logboekquery's, maar ook geavanceerde functionaliteit bevat, zoals aggregaties, joins en slimme analyses. U de querytaal snel leren met [meerdere lessen.](log-query/get-started-queries.md)  Er worden specifieke richtlijnen gegeven voor gebruikers die al bekend zijn met [SQL](log-query/sql-cheatsheet.md) en [Splunk](log-query/splunk-cheatsheet.md).

![Logboeken](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Welke gegevens verzamelt Azure Monitor?

Azure Monitor kan gegevens uit verschillende resources verzamelen. U kunt gegevens voor uw toepassingen bekijken in verschillende lagen, variërend van uw toepassing, elk besturingssysteem en alle services waarvan het afhankelijk is, tot op het platform zelf. Azure Monitor verzamelt gegevens van de volgende lagen:

- **Gegevens over applicatiebewaking**: Gegevens over de prestaties en functionaliteit van de code die u hebt geschreven, ongeacht het platform.
- **Monitoringgegevens van gastbesturingssystemen**: gegevens over het besturingssysteem waarop uw toepassing wordt uitgevoerd. Dit kan worden uitgevoerd in Azure, een andere cloud of on-premises. 
- **Gegevens over azure-bronbewaking:** gegevens over de werking van een Azure-bron.
- **Gegevens over azure-abonnementsbewaking:** gegevens over de werking en het beheer van een Azure-abonnement en gegevens over de status en werking van Azure zelf. 
- **Azure tenant monitoring data:** Gegevens over de werking van Azure-services op tenantniveau, zoals Azure Active Directory.

Zodra u een Azure-abonnement maakt en begint met het toevoegen van resources zoals virtuele machines en webapps, begint Azure Monitor met het verzamelen van gegevens.  [Activiteitslogboeken](platform/platform-logs-overview.md) registreren wanneer resources worden gemaakt of gewijzigd. [Statistieken](platform/data-platform.md) vertellen u hoe de resource presteert en welke resources deze verbruikt. 

Breid de gegevens die u verzamelt uit in de werkelijke werking van de resources door [diagnosegegevens in](platform/platform-logs-overview.md) te schakelen en [een agent toe](platform/agent-windows.md) te voegen aan gegevensbronnen. Hiermee wordt telemetrie verzameld voor de interne werking van de bron en u verschillende [gegevensbronnen](platform/agent-data-sources.md) configureren om logboeken en statistieken van het gastbesturingssysteem van Windows en Linux te verzamelen. 

Schakel bewaking in voor uw [app-toepassing](app/azure-web-apps.md) of [VM- en virtuele machineschaalsettoepassing](app/azure-vm-vmss-apps.md)om Application Insights in staat te stellen gedetailleerde informatie over uw toepassing te verzamelen, inclusief paginaweergaven, toepassingsaanvragen en uitzonderingen. Controleer de beschikbaarheid van uw toepassing verder door een [beschikbaarheidstest](app/monitor-web-app-availability.md) te configureren om gebruikersverkeer te simuleren.

### <a name="custom-sources"></a>Aangepaste bronnen

Azure Monitor kan logboekgegevens van elke REST-client verzamelen met behulp van de [Api voor gegevensverzamelaar.](platform/data-collector-api.md) Hiermee u aangepaste bewakingsscenario's maken en de bewaking uitbreiden naar bronnen die telemetrie via andere bronnen niet blootstellen.

## <a name="insights"></a>Inzichten
Het monitoren van gegevens is alleen nuttig als het uw zichtbaarheid in de werking van uw computeromgeving kan vergroten. Azure Monitor bevat verschillende functies en hulpprogramma's die waardevolle inzichten bieden in uw toepassingen en andere bronnen waarvan ze afhankelijk zijn. [Monitoringoplossingen](insights/solutions.md) en -functies zoals [Application Insights](app/app-insights-overview.md) en [Azure Monitor voor containers](insights/container-insights-overview.md) bieden diepgaande inzichten in verschillende aspecten van uw toepassing en specifieke Azure-services. 

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) bewaakt de beschikbaarheid, prestaties en het gebruik van uw webtoepassingen, ongeacht of ze worden gehost in de cloud of on-premises. Het maakt gebruik van het krachtige data-analyseplatform in Azure Monitor om u diepgaande inzichten te bieden in de bewerkingen van uw toepassing en fouten te diagnosticeren zonder te wachten tot een gebruiker ze rapporteert. Application Insights bevat verbindingspunten voor een verscheidenheid aan ontwikkeltools en integreert met Visual Studio om uw DevOps-processen te ondersteunen.

![App-inzichten](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers
[Azure Monitor voor containers](insights/container-insights-overview.md) is een functie die is ontworpen om de prestaties te controleren van containerworkloads die zijn geïmplementeerd voor beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes Service (AKS). Het geeft u zichtbaarheid van de prestaties door geheugen- en processorstatistieken te verzamelen van controllers, knooppunten en containers die beschikbaar zijn in Kubernetes via de Metrics API. Er worden ook containerlogboeken verzameld.  Nadat u bewaking vanuit Kubernetes-clusters hebt ingeschakeld, worden deze statistieken en logboeken automatisch voor u verzameld via een containerversie van de Log Analytics-agent voor Linux.

![Containerstatus](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor voor virtuele machines
[Azure Monitor voor VM's](insights/vminsights-overview.md) bewaakt uw Virtuele Azure-machines (VM) op schaal door de prestaties en status van uw Windows- en Linux-VM's te analyseren, inclusief hun verschillende processen en onderling verbonden afhankelijkheden van andere resources en externe processen. De oplossing omvat ondersteuning voor het bewaken van prestaties en toepassingsafhankelijkheden voor vm's die on-premises of een andere cloudprovider worden gehost.  


![VM-inzichten](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Bewakingsoplossingen
[Monitoringoplossingen](insights/solutions.md) in Azure Monitor zijn verpakte sets logica die inzichten bieden voor een bepaalde toepassing of service. Ze bevatten logica voor het verzamelen van bewakingsgegevens voor de toepassing of service, [query's](log-query/log-query-overview.md) om die gegevens te analyseren en [weergaven](../log-analytics/log-analytics-view-designer.md) voor visualisatie. Monitoringoplossingen zijn [beschikbaar bij Microsoft](insights/solutions-inventory.md) en partners om monitoring te bieden voor verschillende Azure-services en andere toepassingen.

![Bewakingsoplossingen](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Reageren op kritieke situaties
Naast het feit dat u monitoringgegevens interactief analyseren, moet een effectieve monitoringoplossing proactief kunnen reageren op kritieke omstandigheden die zijn geïdentificeerd in de gegevens die worden gebruikt. Dit kan het verzenden van een sms of e-mail naar een beheerder die verantwoordelijk is voor het onderzoeken van een probleem. Of u een geautomatiseerd proces starten dat probeert een foutvoorwaarde te corrigeren.


### <a name="alerts"></a>Waarschuwingen
[Waarschuwingen in Azure Monitor](platform/alerts-overview.md) stellen u proactief op de hoogte van kritieke omstandigheden en proberen mogelijk corrigerende maatregelen te nemen. Waarschuwingsregels op basis van statistieken bieden bijna realtime waarschuwingen op basis van numerieke waarden, terwijl regels op basis van logboeken complexe logica mogelijk maken voor gegevens uit meerdere bronnen.

Waarschuwingsregels in Azure Monitor gebruiken [actiegroepen,](platform/action-groups.md)die unieke sets ontvangers en acties bevatten die kunnen worden gedeeld via meerdere regels. Op basis van uw vereisten kunnen actiegroepen acties uitvoeren zoals het gebruik van webhooks om waarschuwingen externe acties te laten starten of te integreren met uw ITSM-tools.

![Waarschuwingen](media/overview/alerts.png)

### <a name="autoscale"></a>Automatisch schalen
Met Autoscale u de juiste hoeveelheid resources uitvoeren om de belasting van uw toepassing af te handelen. Hiermee u regels maken die metrische gegevens gebruiken die door Azure Monitor zijn verzameld om te bepalen wanneer u resources automatisch wilt toevoegen om de toename van de belasting te verwerken en ook geld te besparen door resources te verwijderen die niet actief zijn. U geeft een minimum- en maximumaantal exemplaren op en de logica voor wanneer resources moeten worden verhoogd of verlaagd.

![Automatisch schalen](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Bewakingsgegevens visualiseren
[Visualisaties](visualizations.md) zoals grafieken en tabellen zijn effectieve hulpmiddelen voor het samenvatten van bewakingsgegevens en het presenteren ervan aan verschillende doelgroepen. Azure Monitor heeft zijn eigen functies voor het visualiseren van bewakingsgegevens en maakt gebruik van andere Azure-services voor het publiceren ervan aan verschillende doelgroepen.

### <a name="dashboards"></a>Dashboards
[Met Azure-dashboards](../azure-portal/azure-portal-dashboards.md) u verschillende soorten gegevens, waaronder zowel metrische gegevens als logboeken, combineren tot één deelvenster in de [Azure-portal.](https://portal.azure.com) U het dashboard optioneel delen met andere Azure-gebruikers. Elementen in Azure Monitor kunnen worden toegevoegd aan een Azure-dashboard, naast de uitvoer van een logboekquery of metrische gegevensdiagram. U bijvoorbeeld een dashboard maken dat tegels combineert met een grafiek met statistieken, een tabel met activiteitenlogboeken, een gebruiksgrafiek van Application Insights en de uitvoer van een logboekquery.

![Dashboard](media/overview/dashboard.png)

### <a name="views"></a>Weergaven
[Weergaven](../log-analytics/log-analytics-view-designer.md) visueel aanwezige logboekgegevens in Azure Monitor.  Elke weergave bevat één tegel die wordt inzoomen op een combinatie van visualisaties, zoals staaf- en lijndiagrammen, naast lijsten met een samenvatting van kritieke gegevens.  Monitoringoplossingen omvatten weergaven die gegevens voor een bepaalde toepassing samenvatten en u uw eigen weergaven maken om gegevens van elke logboekquery weer te geven. Net als andere elementen in Azure Monitor kunnen weergaven worden toegevoegd aan Azure-dashboards.

![Weergave](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) is een business analytics-service die interactieve visualisaties biedt in verschillende gegevensbronnen en een effectief middel is om gegevens beschikbaar te maken voor anderen binnen en buiten uw organisatie. U Power BI configureren om [logboekgegevens automatisch uit Azure Monitor](../log-analytics/log-analytics-powerbi.md) te importeren om te profiteren van deze extra visualisaties.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Gegevens integreren en exporteren
U hebt vaak de verplichting om Azure Monitor te integreren met andere systemen en aangepaste oplossingen te bouwen die uw bewakingsgegevens gebruiken. Andere Azure-services werken samen met Azure Monitor om deze integratie te bieden.

### <a name="event-hub"></a>Event Hub
[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs) is een streamingplatform- en gebeurtenisopnameservice die gegevens kan transformeren en opslaan met behulp van realtime analyseprovider of batching-/opslagadapters. Gebruik Gebeurtenishubs om [Azure Monitor-gegevens](platform/stream-monitoring-data-event-hubs.md) te streamen naar SIEM- en bewakingshulpprogramma's.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) is een service waarmee u taken en bedrijfsprocessen automatiseren met behulp van workflows die worden geïntegreerd met verschillende systemen en services. Er zijn activiteiten beschikbaar die statistieken en logboeken lezen en schrijven in Azure Monitor, waarmee u workflows bouwen die integreren met verschillende andere systemen.


### <a name="api"></a>API
Er zijn meerdere API's beschikbaar voor het lezen en schrijven van statistieken en logboeken van en naar Azure Monitor, naast toegang tot gegenereerde waarschuwingen. U ook waarschuwingen configureren en ophalen. Dit biedt u in wezen onbeperkte mogelijkheden om aangepaste oplossingen te bouwen die integreren met Azure Monitor.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over:

* [Statistieken en logboeken](platform/data-platform.md) voor de gegevens die worden verzameld door Azure Monitor.
* [Gegevensbronnen](platform/data-sources.md) voor hoe de verschillende onderdelen van uw toepassing telemetrie verzenden.
* [Logboekquery's](log-query/log-query-overview.md) voor het analyseren van verzamelde gegevens.
* [Aanbevolen procedures](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) voor het monitoren van cloudapplicaties en -services.
