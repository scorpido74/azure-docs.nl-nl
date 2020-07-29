---
title: Overzicht van Azure Monitor | Microsoft Docs
description: Overzicht van Microsoft-services en -functionaliteiten die bijdragen aan een complete bewakingsstrategie voor uw Azure-services en -toepassingen.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: 21d980bcaa73af6367908b2f24c0c856d6a6c8ad
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505819"
---
# <a name="azure-monitor-overview"></a>Overzicht van Azure Monitor

Met Azure Monitor worden de beschikbaarheid en prestaties van uw toepassing en services gemaximaliseerd door een uitgebreide oplossing te bieden waarmee u telemetriegegevens kunt verzamelen en analyseren, en op basis hiervan kunt handelen, zowel in de cloud als on-premises. Het helpt u begrijpen hoe uw toepassingen presteren en stelt proactief problemen vast die betrekking hebben op de toepassingen en de resources waarvan ze afhankelijk zijn.

Enkele voorbeelden van de mogelijkheden die Azure Monitor biedt:

- Detecteren en analyseren van problemen in toepassingen en van afhankelijkheden met [Application Insights](app/app-insights-overview.md).
- Problemen met de infrastructuur correleren met [Azure Monitor voor VM's](insights/vminsights-overview.md) en [Azure Monitor voor containers](insights/container-insights-overview.md).
- Inzoomen op uw controlegegevens met [Log Analytics](log-query/log-query-overview.md) voor probleemoplossing en uitgebreide diagnose.
- Op grote schaal bewerkingen londersteunen met [slimme waarschuwingen](platform/alerts-smartgroups-overview.md) en [geautomatiseerde acties](platform/alerts-action-rules.md).
- Visualisaties maken met Azure-[dashboards](learn/tutorial-logs-dashboards.md) en [werkmappen](platform/workbooks-overview.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Overzicht

Het volgende diagram geeft u een algemeen beeld van Azure Monitor. In het midden van het diagram vindt u de gegevensopslag voor metrische gegevens en logboeken. Dit zijn de twee basistypen gegevens die door Azure Monitor worden gebruikt. Aan de linkerkant ziet u de [bronnen van de controlegegevens](platform/data-sources.md) waarmee deze [gegevensopslag](platform/data-platform.md) wordt gevuld. Aan de rechterkant bevinden zich verschillende functies die door Azure Monitor worden uitgevoerd met deze verzamelde gegevens, zoals analyse, waarschuwingen en streaming naar externe systemen.

![Overzicht van Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Platform voor het controleren van gegevens

Alle gegevens die worden verzameld door Azure Monitor, kunnen worden onderverdeeld in twee fundamentele typen gegevens, [metrische gegevens en logboeken](platform/data-platform.md). [Metrische gegevens](platform/data-platform-metrics.md) zijn numeriek waarden waarmee een bepaald aspect van een systeem op een bepaald tijdstip wordt beschreven. Ze zijn licht van gewicht en kunnen in bijna realtime scenario's ondersteunen. [Logboeken](platform/data-platform-logs.md) bevatten verschillende soorten gegevens die zijn ingedeeld in records met verschillende sets eigenschappen voor elk type. Telemetrische gegevens zoals gebeurtenissen en traceringen worden niet alleen opgeslagen als prestatiegegevens maar ook als logboeken, zodat alles kan worden gecombineerd voor analysedoeleinden.

Voor veel Azure-resources worden de gegevens die door Azure Monitor zijn verzameld, rechts op de pagina Overzicht in Azure Portal weergegeven. Als u bijvoorbeeld naar een virtuele machine kijkt, ziet u diverse diagrammen met metrische gegevens over prestaties. Klik op een van de grafieken om de gegevens in [Metrics Explorer](platform/metrics-charts.md) in Azure Portal weer te geven, zodat u de waarden van meerdere metrische gegevens in de loop van de tijd op de grafiek kunt plaatsen.  U kunt de grafieken interactief weergeven of deze vastmaken aan een dashboard om ze te bekijken met andere visualisaties.

![Metrische gegevens](media/overview/metrics.png)

De logboekgegevens die door Azure Monitor zijn verzameld, kunnen worden geanalyseerd met [query's](log-query/log-query-overview.md) om snel verzamelde gegevens op te halen, samen te voegen en te analyseren.  U kunt query's maken en testen door [Log Analytics](./log-query/log-query-overview.md) in Azure Portal te gebruiken. Vervolgens kunt u de gegevens onmiddellijk analyseren met verschillende hulpprogramma's, maar u kunt de query's ook opslaan om deze te gebruiken met [visualisaties](visualizations.md) of [waarschuwingsregels](platform/alerts-overview.md).

Azure Monitor gebruikt een versie van de [Kusto-querytaal](/azure/kusto/query/) (KQL) die door Azure Data Explorer wordt gebruikt en die geschikt is voor eenvoudige logboekquery's maar die ook geavanceerde functies bevat, zoals aggregaties, joins en slimme analyse. Via [diverse lessen](log-query/get-started-queries.md) kunt u de querytaal snel leren.  Er worden specifieke richtlijnen gegeven voor gebruikers die al bekend zijn met [SQL](log-query/sql-cheatsheet.md) en [Splunk](log-query/splunk-cheatsheet.md).

![Logboeken](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Welke gegevens worden door Azure Monitor verzameld?

Met Azure Monitor kunt u gegevens verzamelen van verschillende bronnen. De controlegegevens voor uw toepassingen kunnen als het ware worden onderverdeeld in lagen, variërend van uw toepassing, een besturingssysteem en de services waarvan het systeem afhankelijk is, tot en met het platform zelf. Azure Monitor verzamelt gegevens uit elk van de volgende lagen:

- **Controlegegevens van de toepassing** : Gegevens over de prestaties en functionaliteit van de code die u hebt geschreven, ongeacht het platform.
- **Controlegegevens van het gastbesturingssysteem**: Gegevens over het besturingssysteem waarop uw toepassing wordt uitgevoerd. Dit kan worden uitgevoerd in Azure, een andere cloud of on-premises. 
- **Bewakingsgegevens van Azure-resources**: Gegevens over de werking van een Azure-resource.
- **Bewakingsgegevens van Azure-abonnementen**: Gegevens over de werking en het beheer van een Azure-abonnement en gegevens over de status en werking van Azure zelf. 
- **Bewakingsgegevens van Azure-tenants**: Gegevens over de werking van Azure-services op tenantniveau, zoals Azure Active Directory.

Zodra u een Azure-abonnement maakt en resources zoals virtuele machines en web-apps toevoegt, begint Azure Monitor met het verzamelen van gegevens.  [Activiteitenlogboeken](platform/platform-logs-overview.md) registreren wanneer resources worden gemaakt of gewijzigd. [Metrische gegevens](platform/data-platform.md) vertellen u hoe de resource presteert en welke resources worden gebruikt. 

U kunt de gegevens die u verzamelt, uitbreiden naar de werkelijke werking van de resources door [diagnostische gegevens in te schakelen](platform/platform-logs-overview.md) en [een agent toe te voegen](platform/agent-windows.md) aan rekenresources. Hiermee wordt de telemetrie verzameld voor de interne werking van de resource en kunt u verschillende [gegevensbronnen](platform/agent-data-sources.md) configureren om metrische gegevens en logboeken te verzamelen van het Windows- en Linux-gastbesturingssysteem. 

Schakel controle in voor uw [App Services-toepassing](app/azure-web-apps.md) of [VM- en virtuele-machineschaalsettoepassing](app/azure-vm-vmss-apps.md) om gedetailleerde informatie met Application Insights te verzamelen over uw toepassing, waaronder paginaweergaven, toepassingsaanvragen en uitzonderingen. Controleer de beschikbaarheid van uw toepassing door een [beschikbaarheidstest ](app/monitor-web-app-availability.md) te configureren om gebruikersverkeer te simuleren.

### <a name="custom-sources"></a>Aangepaste bronnen

Azure Monitor kan met behulp van de [Data Collector-API](platform/data-collector-api.md) logboekgegevens verzamelen van elke REST-client. Hierdoor kunt u aangepaste controlescenario's maken en de controle uitbreiden naar resources die geen telemetrie via andere bronnen beschikbaar stellen.

## <a name="insights"></a>Inzichten
Het controleren van gegevens is alleen nuttig als dit meer inzicht biedt in de werking van uw computeromgeving. Azure Monitor bevat verschillende functies en hulpprogramma's die waardevolle inzichten bieden in uw toepassingen en andere bronnen waarvan ze afhankelijk zijn. [Bewakingsoplossingen](insights/solutions.md) en -functies, zoals [Application Insights](app/app-insights-overview.md) en [Azure Monitor voor containers](insights/container-insights-overview.md) bieden uitgebreid inzicht in verschillende aspecten van uw toepassing en specifieke Azure-services. 

### <a name="application-insights"></a>Application Insights
Met [Application Insights](app/app-insights-overview.md) kunt u de beschikbaarheid, prestaties en het gebruik van uw toepassingen controleren, ongeacht of deze worden gehost in de cloud of on-premises. De oplossing maakt gebruik van het krachtige platform voor gegevensanalyse in Azure Monitor om u uitgebreid inzicht te geven in de werking van uw toepassing en fouten te onderzoeken zonder te hoeven wachten tot een gebruiker ze meldt. Application Insights bevat verbindingspunten naar verschillende ontwikkelprogramma's en integreert met Visual Studio om uw DevOps-processen te ondersteunen.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers
[Azure Monitor voor containers](insights/container-insights-overview.md) is een functie waarmee u de prestaties kunt controleren van de containerworkloads die zijn geïmplementeerd op beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes Service (AKS). Dit geeft u inzicht in de prestaties door via de Metrics API metrische gegevens van het geheugen en de processor te verzamelen van controllers, knooppunten en containers die beschikbaar zijn in Kubernetes. Er worden ook containerlogboeken verzameld.  Nadat u de controle van Kubernetes-clusters hebt ingeschakeld, worden deze metrische gegevens en logboeken automatisch voor u verzameld via een containerversie van de Log Analytics-agent voor Linux.

![Containerstatus](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor voor virtuele machines
[Azure Monitor voor VM's](insights/vminsights-overview.md) controleert uw virtuele machines van Azure (VM's) op schaal door de prestaties en status van uw Windows- en Linux-VM's te analyseren, inclusief hun verschillende processen en onderling verbonden afhankelijkheden van andere resources en externe processen. De oplossing bevat ondersteuning voor het controleren van prestatie- en toepassingsafhankelijkheden voor VM's die on-premises of door een andere cloudprovider worden gehost.  


![VM Insights](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Bewakingsoplossingen
[Bewakingsoplossingen](insights/solutions.md) in Azure Monitor zijn verpakte logicasets die inzichten bieden voor een bepaalde toepassing of service. Ze bevatten logica voor het verzamelen van controlegegevens voor de toepassing of service, [query's](log-query/log-query-overview.md) om die gegevens te analyseren en [weergaven](./platform/view-designer.md) om de uitkomsten te visualiseren. Er zijn bewakingsoplossingen [beschikbaar via Microsoft](./monitor-reference.md) en zijn partners waarmee allerlei Azure-services en andere toepassingen kunnen worden gecontroleerd.

![Bewakingsoplossingen](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Reageren op kritieke situaties
Naast de mogelijkheid om controlegegevens interactief te analyseren, moet een efficiënte bewakingsoplossing proactief kunnen reageren op kritieke omstandigheden die in de verzamelde gegevens worden geïdentificeerd. Dit kan bijvoorbeeld door een tekst of e-mailbericht te versturen naar een beheerder die verantwoordelijk is voor het onderzoeken van een probleem. U kunt ook een geautomatiseerd proces starten waarmee wordt geprobeerd een fout te corrigeren.


### <a name="alerts"></a>Waarschuwingen
[Waarschuwingen in Azure Monitor](platform/alerts-overview.md) stellen u proactief op de hoogte van kritieke omstandigheden en proberen corrigerende maatregelen te nemen. Waarschuwingsregels op basis van metrische gegevens bieden in bijna realtime waarschuwingen op basis van numerieke waarden, terwijl regels op basis van logboeken complexe logica op gegevens uit meerdere bronnen toestaan.

Waarschuwingsregels in Azure Monitor gebruiken [actiegroepen](platform/action-groups.md) Deze actiegroepen bevatten unieke sets ontvangers en acties die kunnen worden gedeeld met meerdere regels. Op basis van uw vereisten kunnen actiegroepen bijvoorbeeld acties uitvoeren met behulp van webhooks, zodat waarschuwingen externe acties starten of worden geïntegreerd met uw ITSM-hulpprogramma's.

![Waarschuwingen](media/overview/alerts.png)

### <a name="autoscale"></a>Automatisch schalen
Met automatisch schalen kunt u de juiste hoeveelheid resources uitvoeren om de belasting van uw toepassing te verwerken. U kunt regels maken die gebruikmaken van metrische gegevens die worden verzameld door Azure Monitor om te bepalen wanneer resources automatisch moeten worden toegevoegd om een toename in de belasting te verwerken, en ook om geld te besparen door resources te verwijderen die niet-actief zijn. U geeft een minimum en maximum aantal instanties en de logica voor het vergroten of verkleinen van het aantal resources op.

![Automatisch schalen](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Bewakingsgegevens visualiseren
[Visualisaties](visualizations.md) zoals grafieken en tabellen zijn effectief hulpmiddelen voor het samenvatten van bewakingsgegevens en om deze te presenteren aan verschillende doelgroepen. Azure Monitor beschikt over eigen functies voor het visualiseren van bewakingsgegevens en maakt gebruik van andere Azure-services om de gegevens naar verschillende doelgroepen te publiceren.

### <a name="dashboards"></a>Dashboards
Met [Azure-dashboards](../azure-portal/azure-portal-dashboards.md) kunt u verschillende soorten gegevens, waaronder metrische gegevens en logboeken, in één paneel in [Azure Portal](https://portal.azure.com) combineren. U kunt het dashboard eventueel delen met andere Azure-gebruikers. Naast logboekquery's en grafieken met metrische gegevens kunnen er ook elementen uit Azure Monitor worden toegevoegd aan een Azure-dashboard. U kunt bijvoorbeeld een dashboard maken met een combinatie van verschillende tegels. Bijvoorbeeld tegels waarop een grafiek met metrische gegevens, een tabel met activiteitenlogboeken, een gebruiksdiagram van Application Insights en de uitvoer van een logboekquery worden weergegeven.

![Dashboard](media/overview/dashboard.png)

### <a name="views"></a>Weergaven
[Weergaven](./platform/view-designer.md) vormen een visuele presentatie van logboekgegevens in Azure Monitor.  Elke weergave bevat één tegel die inzoomt op een combinatie van visualisaties, zoals staaf- en lijndiagrammen, aangevuld met lijsten waarin kritieke gegevens worden samengevat.  Bewakingsoplossingen bevatten weergaven waarin gegevens voor een bepaalde toepassing worden samengevat. U kunt uw eigen weergaven maken om de resultaten van een logboekquery weer te geven. Net als andere elementen in Azure Monitor kunnen weergaven worden toegevoegd aan Azure-dashboards.

![Weergave](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) is een Business Analytics-service die interactieve visualisaties voor verschillende gegevensbronnen levert en vormt een effectieve manier om gegevens beschikbaar te maken voor anderen binnen en buiten uw organisatie. U kunt Power BI zodanig configureren dat er [automatisch logboekgegevens vanuit Azure Monitor worden geïmporteerd](./platform/powerbi.md) om te profiteren van deze extra visualisaties.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Gegevens integreren en exporteren
U moet Azure Monitor waarschijnlijk vaak integreren met andere systemen en aangepaste oplossingen bouwen die gebruikmaken van uw controlegegevens. Andere Azure-services werken met Azure Monitor om deze integratie te bieden.

### <a name="event-hub"></a>Event Hub
[Azure Event Hubs](../event-hubs/index.yml) is een streamingplatform en een service voor gebeurtnisopname waarmee gegevens kunnen worden getransformeerd en opgeslagen via een realtime analyseprovider of batching-/opslagadapters. Gebruik Event Hubs voor het [streamen van Azure Monitor-gegevens](platform/stream-monitoring-data-event-hubs.md) naar de SIEM-voorziening van een partner en bewakingshulpprogramma's.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) is een service waarmee u taken en bedrijfsprocessen kunt automatiseren met werkstromen die kunnen worden geïntegreerd met verschillende systemen en services. Er zijn activiteiten beschikbaar die metrische gegevens en logboeken in Azure Monitor kunnen lezen en schrijven. Dit biedt u de mogelijkheid om werkstromen te bouwen die zijn geïntegreerd met verschillende systemen.


### <a name="api"></a>API
Naast de toegang tot gegenereerde waarschuwingen zijn er meerdere API's beschikbaar voor het lezen en schrijven van metrische gegevens en logboeken van en naar Azure Monitor. U kunt waarschuwingen ook configureren en ophalen. Dit biedt u de onbeperkte mogelijkheden om aangepaste oplossingen te bouwen die met Azure Monitor kunnen worden geïntegreerd.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over:

* [Metrische gegevens en logboeken](platform/data-platform.md) voor de gegevens die worden verzameld door Azure Monitor.
* [Gegevensbronnen](platform/data-sources.md) voor de manier waarop de verschillende onderdelen van uw toepassing telemetrie verzenden.
* [Logboekquery's](log-query/log-query-overview.md) voor het analyseren van verzamelde gegevens.
* [Aanbevolen procedures](/azure/architecture/best-practices/monitoring) voor het bewaken van cloudtoepassingen en -services.
