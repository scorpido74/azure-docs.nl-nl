---
title: Azure-resources bewaken met Azure Monitor | Microsoft Docs
description: Hierin wordt beschreven hoe u bewakings gegevens van resources in azure verzamelt en analyseert met behulp van Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 8ba50e5b55e31c6542e81879068bf231918ce1b0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244887"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Azure-resources bewaken met Azure Monitor
Wanneer u belang rijke toepassingen en bedrijfs processen hebt die afhankelijk zijn van Azure-resources, wilt u deze resources controleren op hun Beschik baarheid, prestaties en werking. In dit artikel worden de bewakings gegevens beschreven die worden gegenereerd door Azure-resources en hoe u de functies van Azure Monitor kunt gebruiken om deze gegevens te analyseren en te waarschuwen.

> [!IMPORTANT]
> Dit artikel is van toepassing op alle services in azure die gebruikmaken van Azure Monitor. Reken bronnen, met inbegrip van Vm's en App Service, genereren dezelfde bewakings gegevens die hier worden beschreven, maar ook een gast besturingssysteem dat ook logboeken en metrieken kan genereren. Zie de documentatie over het controleren van deze services voor meer informatie over het verzamelen en analyseren van deze gegevens.

## <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?
Azure Monitor is een volledige stack monitoring-service in azure met een volledige set functies voor het bewaken van uw Azure-resources naast bronnen in andere Clouds en on-premises. Het [Azure monitor-gegevens platform](../platform/data-platform.md) verzamelt gegevens in [Logboeken](../platform/data-platform-logs.md) en [metrieken](../platform/data-platform-metrics.md) waar ze kunnen worden geanalyseerd met behulp van een volledige set controle hulpprogramma's, zoals beschreven in de volgende secties.

- [Wat kunt u doen met Azure Monitor metrische gegevens?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [Wat kunt u doen met Azure Monitor-logboeken?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

Zodra u een Azure-resource maakt, wordt Azure Monitor ingeschakeld en worden er metrische gegevens en activiteiten logboeken verzameld die u [in de Azure Portal kunt bekijken en analyseren](#monitoring-in-the-azure-portal). Met een configuratie kunt u extra bewakings gegevens verzamelen en aanvullende functies inschakelen. Zie de onderstaande [bewakings gegevens](#monitoring-data) voor meer informatie over de configuratie vereisten.


## <a name="costs-associated-with-monitoring"></a>Kosten die zijn gekoppeld aan bewaking
Er zijn geen kosten voor het analyseren van bewakings gegevens die standaard worden verzameld. Dit omvat het volgende:

- De metrische gegevens van het platform verzamelen en analyseren met metrische gegevens Verkenner.
- Het activiteiten logboek verzamelen en analyseren in het Azure Portal.
- Een waarschuwings regel voor het activiteiten logboek maken.

Er zijn geen Azure Monitor kosten voor het verzamelen en exporteren van Logboeken en metrische gegevens, maar er zijn mogelijk gerelateerde kosten verbonden aan het doel:

- Kosten die zijn gekoppeld aan gegevens opname en-retentie bij het verzamelen van Logboeken en metrieken in Log Analytics werk ruimte. Zie [Azure monitor prijzen voor log Analytics](https://azure.microsoft.com/pricing/details/monitor/).
- Kosten die zijn gekoppeld aan gegevens opslag bij het verzamelen van Logboeken en metrieken voor een Azure-opslag account. Zie [Azure Storage prijzen voor Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Kosten die zijn gekoppeld aan Event Hub streaming bij het door sturen van Logboeken en metrische gegevens naar Azure Event Hubs. Zie [prijzen voor Azure Event hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

Er zijn mogelijk Azure Monitor kosten gekoppeld aan het volgende. Zie [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/):

- Een logboek query uitvoeren.
- Er wordt een waarschuwings regel voor metrische gegevens of logboek query's gemaakt.
- Er wordt een melding verzonden vanuit elke waarschuwings regel.
- Toegang tot metrische gegevens via API.

## <a name="monitoring-data"></a>Bewakingsgegevens
Resources in azure genereren [Logboeken](../platform/data-platform-logs.md) en [metrische gegevens](../platform/data-platform-metrics.md) die het volgende diagram weer geven. Raadpleeg de documentatie voor elke Azure-service voor de specifieke gegevens die ze genereren en eventuele aanvullende oplossingen of inzichten die ze bieden.

![Overzicht](media/monitor-azure-resource/logs-metrics.png)



- [Platform metrieken](../platform/data-platform-metrics.md) : numerieke waarden die automatisch met regel matige tussen pozen worden verzameld en een zekere hoogte van een resource op een bepaald moment beschrijven. 
- [Bron logboeken](../platform/resource-logs-overview.md) : bieden inzicht in bewerkingen die zijn uitgevoerd in een Azure-resource (het gegevens vlak), bijvoorbeeld het verkrijgen van een geheim van een Key Vault of het maken van een aanvraag voor een Data Base. De inhoud en structuur van bron logboeken zijn afhankelijk van de Azure-service en het resource type.
- [Activiteiten logboek](../platform/activity-logs-overview.md) : biedt inzicht in de bewerkingen op elke Azure-resource in het abonnement van buiten (het beheer vlak), bijvoorbeeld het maken van een nieuwe resource of het starten van een virtuele machine. Hier vindt u informatie over de functies wat, wie en wanneer u schrijf bewerkingen (PUT, POST, DELETE) hebt uitgevoerd voor de resources in uw abonnement.


## <a name="configuration-requirements"></a>Configuratie vereisten
Sommige bewakings gegevens worden automatisch verzameld, maar u moet mogelijk een configuratie uitvoeren, afhankelijk van uw vereisten. Zie de onderstaande informatie voor specifieke informatie voor elk type bewakings gegevens.

- [Platform metrieken](../platform/data-platform-metrics.md) : platform metrieken worden automatisch verzameld in [Azure monitor metrieken](../platform/data-platform-metrics.md) zonder configuratie vereist. Maak een diagnostische instelling voor het verzenden van vermeldingen naar Azure Monitor Logboeken of om ze buiten Azure door te sturen.
- [Resource logboeken](../platform/resource-logs-overview.md) : resource logboeken worden automatisch gegenereerd door Azure-resources, maar niet zonder een diagnostische instelling.  Maak een diagnostische instelling voor het verzenden van vermeldingen naar Azure Monitor Logboeken of om ze buiten Azure door te sturen.
- [Activiteiten logboek](../platform/activity-logs-overview.md) : het activiteiten logboek wordt automatisch verzameld zonder vereiste configuratie en kan worden weer gegeven in de Azure Portal. Een diagnostische instelling maken om deze te kopiëren naar Azure Monitor Logboeken of om ze buiten Azure door te sturen.


## <a name="diagnostic-settings"></a>Diagnostische instellingen
Diagnostische instellingen bepalen waar bron logboeken en metrische gegevens voor een bepaalde resource moeten worden verzonden. Mogelijke bestemmingen zijn:

- [Log Analytics werk ruimte](../platform/resource-logs-collect-workspace.md) waarmee u gegevens kunt analyseren met andere bewakings gegevens die worden verzameld door Azure monitor met behulp van krachtige logboek query's en ook voor het gebruik van andere Azure monitor functies, zoals logboek waarschuwingen en visualisaties. 
- [Event hubs](../platform/resource-logs-stream-event-hubs.md) voor het streamen van gegevens naar externe systemen, zoals siem's van derden en andere log Analytics-oplossingen. 
- [Azure Storage-account](../platform/resource-logs-collect-storage.md) dat nuttig is voor controle, statische analyses of back-ups.

Volg de procedure in [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](../platform/diagnostic-settings.md) voor het maken en beheren van diagnostische instellingen via de Azure Portal. Zie [Diagnostische instelling maken in azure met behulp van een resource manager-sjabloon](../platform/diagnostic-settings-template.md) om deze in een sjabloon te definiëren en volledige bewaking in te scha kelen voor een resource wanneer deze wordt gemaakt.


## <a name="monitoring-in-the-azure-portal"></a>Bewaking in de Azure Portal
 U hebt toegang tot bewakings gegevens voor de meeste Azure-resources vanuit het menu van de resource in de Azure Portal. Hiermee krijgt u toegang tot de gegevens van één resource met behulp van standaard Azure Monitor-hulpprogram ma's. Sommige Azure-Services bieden andere opties, dus u moet de documentatie voor die service raadplegen voor aanvullende informatie. Gebruik het menu **Azure monitor** om gegevens van alle bewaakte resources te analyseren. 

Veel services bevatten bewakings gegevens op de **overzichts** pagina als een kort overzicht van de werking ervan. Dit is doorgaans gebaseerd op een subset van platform metrieken die zijn opgeslagen in Azure Monitor metrische gegevens. Andere bewakings opties zijn doorgaans beschikbaar in een **bewakings** sectie van de services. snelmenu's.

![Overzichts pagina](media/monitor-azure-resource/overview-page.png)

## <a name="analyzing-metrics"></a>Metrische gegevens analyseren
Analyseer afzonderlijke metrische gegevens en correleert meerdere metrische gegevens om correlaties en trends te identificeren met behulp van [metrische gegevens Verkenner](../platform/metrics-getting-started.md). Sommige services bieden een aangepaste ervaring voor het werken met de metrische gegevens wanneer u **metrische gegevens** opent vanuit het menu resource.

- Zie [aan de slag met Azure Metrics Explorer](../platform/metrics-getting-started.md) voor de basis principes van het gebruik van metrische gegevens Verkenner.
- Zie [geavanceerde functies van Azure Metrics Explorer](../platform/metrics-charts.md) voor geavanceerde functies van de Verkenner voor metrische gegevens, zoals het gebruik van meerdere metrische gegevens en het Toep assen van filters en splitsen.

![Metrische gegevens](media/monitor-azure-resource/metrics.png)


## <a name="analyzing-logs"></a>Logboeken analyseren

### <a name="activity-log"></a>Activiteitenlogboek 
Vermeldingen in het activiteiten logboek weer geven in de Azure Portal met het eerste filter dat is ingesteld op de huidige resource. Kopieer het activiteiten logboek naar een Log Analytics werk ruimte om het te openen om het te gebruiken in logboek query's en-werkmappen. 

- Zie activiteiten [logboek gebeurtenissen van Azure weer geven en ophalen](../platform/activity-log-view.md) voor meer informatie over het weer geven van het activiteiten logboek en het ophalen van vermeldingen op basis van verschillende methoden.
- Raadpleeg de documentatie voor uw Azure-service voor de specifieke gebeurtenissen die worden vastgelegd in het logboek.

![Activiteitenlogboek](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken
Met Azure Monitor logboeken worden logboeken en metrische gegevens van meerdere services en andere data bronnen voor analyse geconsolideerd met een krachtig query programma. Zoals hierboven beschreven, maakt u een diagnostische instelling voor het verzamelen van metrische gegevens van het platform, het activiteiten logboek en bron Logboeken in een Log Analytics-werk ruimte in Azure Monitor.

Met [log Analytics](../log-query/get-started-portal.md) kunt u werken met [logboek query's](../log-query/log-query-overview.md). Dit is een krachtige functie van Azure monitor waarmee u een geavanceerde analyse van logboek gegevens kunt uitvoeren met behulp van een volledig aanbevolen query taal. Open Log Analytics van **Logboeken** in het **controle** menu voor een Azure-resource om te werken met logboek query's met behulp van de resource als [query bereik](../log-query/scope.md#query-scope). Zo kunt u gegevens analyseren over meerdere tabellen voor deze resource. Gebruik **Logboeken** in het menu Azure monitor om toegang te krijgen tot logboeken voor alle resources. 

- Zie [aan de slag met log Analytics in azure monitor](../log-query/get-started-portal.md) voor een overzicht van het gebruik van log Analytics om een query te schrijven en te werken met de resultaten.
- Zie aan de [slag met logboek query's in azure monitor](../log-query/get-started-queries.md) voor een zelf studie over het gebruik van de query taal die wordt gebruikt om logboek query's te schrijven.
- Zie [Azure-resource logboeken verzamelen in log Analytics werk ruimte in azure monitor](../platform/resource-logs-collect-workspace.md) voor informatie over hoe bron logboeken worden verzameld in azure monitor logboeken en Details over hoe u ze in een query kunt openen.
- Zie [Verzamel modus](../platform/resource-logs-collect-workspace.md#collection-mode) voor een uitleg van hoe bron logboek gegevens worden gestructureerd in azure monitor Logboeken.
- Raadpleeg de documentatie voor elke Azure-service voor meer informatie over de tabel in Azure Monitor logs.

![Logboeken](media/monitor-azure-resource/logs.png)


## <a name="alerts"></a>Waarschuwingen
[Waarschuwingen](../platform/alerts-overview.md) geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. U maakt een waarschuwings regel die een doel voor de waarschuwing definieert, de voor waarden om te bepalen of er een waarschuwing moet worden gemaakt en welke acties moeten worden uitgevoerd als reactie.

Verschillende soorten bewakings gegevens worden gebruikt voor verschillende soorten waarschuwings regels.

- [Waarschuwing voor activiteiten logboek](../platform/alerts-activity-log.md) -een waarschuwing maken wanneer er een vermelding in het activiteiten logboek wordt gemaakt die overeenkomt met bepaalde criteria. Zo kunt u een melding ontvangen wanneer een bepaald type resource wordt gemaakt of een configuratie wijziging mislukt.
- [Metrische waarschuwing](../platform/alerts-metric.md) : een waarschuwing maken wanneer een metrische waarde een bepaalde drempel overschrijdt. Metrische waarschuwingen zijn meer reageren dan andere waarschuwingen en kunnen automatisch worden omgezet wanneer het probleem is opgelost.
- [Logboek query waarschuwing](../platform/alerts-log.md) : Voer regel matig een logboek query uit en maak een waarschuwing als een bepaalde voor waarde wordt gevonden. Zo kunt u complexe analyses uitvoeren op meerdere gegevens sets en.

**Waarschuwingen** gebruiken in het menu van een resource om waarschuwingen weer te geven en waarschuwings regels voor die resource te beheren. Alleen waarschuwingen voor activiteiten logboeken en metrische waarschuwingen gebruiken afzonderlijke Azure-resources als doel. Waarschuwingen voor logboek query's maken gebruik van een Log Analytics werkruimte als doel en zijn gebaseerd op een query die toegang heeft tot de logboeken die zijn opgeslagen in die werk ruimte. Gebruik het menu Azure Monitor om waarschuwingen voor alle resources en de waarschuwings regels voor het beheren van logboek query's weer te geven en te beheren.

- Zie de artikelen voor de verschillende soorten waarschuwingen hierboven voor meer informatie over het maken van waarschuwings regels.
- Zie [actie groepen maken en beheren in de Azure Portal](../platform/action-groups.md) voor meer informatie over het maken van een actie groep waarmee u antwoorden op waarschuwingen kunt beheren.

## <a name="insights-and-solutions"></a>Inzichten en oplossingen 
Sommige services bieden nog meer hulp middelen dan de standaard functies van Azure Monitor. [Oplossingen](../insights/solutions.md) bieden vooraf gedefinieerde bewakings logica die is gebaseerd op standaard Azure monitor-functies. [Inzichten](../insights/insights-overview.md) bieden een aangepaste bewakings ervaring die is gebaseerd op het Azure monitor gegevens platform en de standaard functies.

Als een resource een inzicht beschikbaar heeft, kunt u deze openen vanuit **inzichten** in het menu resource. Toegang tot alle inzichten en oplossingen vanuit het Azure Monitor menu.

- Raadpleeg de controle documentatie voor elke service om te bepalen of er inzichten of oplossingen beschikbaar zijn.

![Inzichten](media/monitor-azure-resource/insights.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [ondersteunde services, schema's en categorieën voor Azure Diagnostische logboeken](../platform/diagnostic-logs-schema.md) voor meer informatie over resource logboeken voor verschillende Azure-Services.  
