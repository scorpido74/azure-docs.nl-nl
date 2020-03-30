---
title: Azure-bronnen bewaken met Azure Monitor | Microsoft Documenten
description: Beschrijft hoe u bewakingsgegevens verzamelt en analyseert van resources in Azure met Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 01d188e0e39888297ff8d6a57129a3a17e1654fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249267"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Azure-resources bewaken met Azure Monitor
Wanneer u kritieke toepassingen en bedrijfsprocessen hebt die afhankelijk zijn van Azure-resources, wilt u deze resources controleren op hun beschikbaarheid, prestaties en werking. In dit artikel worden de bewakingsgegevens beschreven die zijn gegenereerd door Azure-bronnen en hoe u de functies van Azure Monitor gebruiken om deze gegevens te analyseren en te waarschuwen.

> [!IMPORTANT]
> Dit artikel is van toepassing op alle services in Azure die Azure Monitor gebruiken. Compute resources, waaronder VM's en App Service, genereren dezelfde monitoringgegevens die hier worden beschreven, maar hebben ook een gastbesturingssysteem dat ook logboeken en statistieken kan genereren. Zie de bewakingsdocumentatie voor deze services voor meer informatie over het verzamelen en analyseren van deze gegevens.

## <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?
Azure Monitor is een volledige stackmonitoringservice in Azure die een complete set functies biedt om uw Azure-resources te controleren, naast resources in andere clouds en on-premises. Het [Azure Monitor-gegevensplatform](../platform/data-platform.md) verzamelt gegevens in [logboeken](../platform/data-platform-logs.md) en [statistieken](../platform/data-platform-metrics.md) waar ze samen kunnen worden geanalyseerd met behulp van een complete set bewakingstools zoals beschreven in de volgende secties.

- [Wat u doen met Azure Monitor Metrics?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [Wat u doen met Azure Monitor Logs?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

Zodra u een Azure-bron maakt, is Azure Monitor ingeschakeld en begint het verzamelen van statistieken en activiteitslogboeken die u [bekijken en analyseren in de Azure-portal.](#monitoring-in-the-azure-portal) Bij sommige configuraties u extra bewakingsgegevens verzamelen en extra functies inschakelen. Zie [Monitoringgegevens](#monitoring-data) hieronder voor meer informatie over eventuele configuratievereisten.


## <a name="costs-associated-with-monitoring"></a>Kosten in verband met monitoring
Er zijn geen kosten verbonden aan het analyseren van monitoringgegevens die standaard worden verzameld. Dit omvat het volgende:

- Het verzamelen van platformstatistieken en het analyseren ervan met metrics explorer.
- Het verzamelen van activiteitslogboeken en het analyseren ervan in de Azure-portal.
- Een waarschuwingsregel voor het logboek activiteit maken.

Er zijn geen Azure Monitor-kosten voor het verzamelen en exporteren van logboeken en statistieken, maar er kunnen gerelateerde kosten verbonden zijn aan de bestemming:

- Kosten in verband met het innemen en bewaren van gegevens bij het verzamelen van logboeken en statistieken in de werkruimte Log Analytics. Zie [Azure Monitor-prijzen voor Logboekanalyse](https://azure.microsoft.com/pricing/details/monitor/).
- Kosten in verband met gegevensopslag bij het verzamelen van logboeken en statistieken voor een Azure-opslagaccount. Zie [Azure Storage-prijzen voor blob-opslag](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Kosten in verband met het streamen van gebeurtenishubs bij het doorsturen van logboeken en statistieken naar Azure Event Hubs. Zie [de prijzen van Azure Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

Er kunnen azure monitorkosten zijn gekoppeld aan het volgende. Zie [Azure Monitor-prijzen:](https://azure.microsoft.com/pricing/details/monitor/)

- Een logboekquery uitvoeren.
- Een metrische regel voor waarschuwingen voor query's maken of logboekquery's.
- Een melding verzenden vanuit een waarschuwingsregel.
- Toegang tot statistieken via API.

## <a name="monitoring-data"></a>Bewakingsgegevens
Bronnen in Azure genereren [logboeken](../platform/data-platform-logs.md) en [statistieken](../platform/data-platform-metrics.md) die het volgende diagram worden weergegeven. Raadpleeg de documentatie voor elke Azure-services voor de specifieke gegevens die ze genereren en eventuele aanvullende oplossingen of inzichten die ze bieden.

![Overzicht](media/monitor-azure-resource/logs-metrics.png)



- [Platformstatistieken](../platform/data-platform-metrics.md) - Numerieke waarden die automatisch op regelmatige tijdstippen worden verzameld en een bepaald aspect van een resource op een bepaald moment beschrijven. 
- [Bronlogboeken](../platform/platform-logs-overview.md) - Geef inzicht in bewerkingen die zijn uitgevoerd binnen een Azure-bron (het gegevensvlak), bijvoorbeeld het verkrijgen van een geheim uit een Key Vault of het indienen van een aanvraag voor een database. De inhoud en structuur van resourcelogboeken varieert per Azure-service en resourcetype.
- [Activiteitenlogboek](../platform/platform-logs-overview.md) - Geeft inzicht in de bewerkingen van elke Azure-bron in het abonnement van buitenaf (het beheervlak), bijvoorbeeld het maken van een nieuwe bron of het starten van een virtuele machine. Dit is informatie over het wat, wie en wanneer voor schrijfbewerkingen (PUT, POST, DELETE) die zijn overgenomen op de bronnen in uw abonnement.


## <a name="configuration-requirements"></a>Configuratievereisten

### <a name="configure-monitoring"></a>Bewaking configureren
Sommige bewakingsgegevens worden automatisch verzameld, maar het kan nodig zijn om een bepaalde configuratie uit te voeren, afhankelijk van uw vereisten. Zie de onderstaande informatie voor specifieke informatie voor elk type bewakingsgegevens.

- [Platformstatistieken](../platform/data-platform-metrics.md) - Platformstatistieken worden automatisch verzameld in [Azure Monitor Metrics](../platform/data-platform-metrics.md) zonder dat er configuratie is vereist. Maak een diagnostische instelling om items naar Azure Monitor Logs te verzenden of om ze buiten Azure door te sturen.
- [Resourcelogboeken](../platform/platform-logs-overview.md) - Resourcelogboeken worden automatisch gegenereerd door Azure-bronnen, maar niet verzameld zonder diagnostische instelling.  Maak een diagnostische instelling om items naar Azure Monitor Logs te verzenden of om ze buiten Azure door te sturen.
- [Activiteitenlogboek](../platform/platform-logs-overview.md) - Het activiteitenlogboek wordt automatisch verzameld zonder dat er een configuratie nodig is en kan worden bekeken in de Azure-portal. Maak een diagnostische instelling om ze naar Azure Monitor Logs te kopiëren of om ze buiten Azure door te sturen.

### <a name="log-analytics-workspace"></a>Log Analytics-werkruimte
Voor het verzamelen van gegevens in Azure Monitor Logs is een Log Analytics-werkruimte vereist. U uw service snel controleren door een nieuwe werkruimte te maken, maar er kan waarde zijn in het gebruik van een werkruimte die gegevens van andere services verzamelt. Zie [Een werkruimte voor Logboekanalyse maken in de Azure-portal](../learn/quick-create-workspace.md) voor meer informatie over het maken van een werkruimte en Het ontwerpen van de implementatie van uw Azure Monitor [Logs](../platform/design-logs-deployment.md) om te helpen bij het bepalen van het beste werkruimteontwerp voor uw vereisten. Als u een bestaande werkruimte in uw organisatie gebruikt, hebt u de juiste machtigingen nodig zoals beschreven in [Toegang beheren tot logboekgegevens en werkruimten in Azure Monitor.](../platform/manage-access.md) 





## <a name="diagnostic-settings"></a>Diagnostische instellingen
Diagnostische instellingen bepalen waar bronlogboeken en statistieken voor een bepaalde resource moeten worden verzonden. Mogelijke bestemmingen zijn:

- [Log Analytics-werkruimte](../platform/resource-logs-collect-workspace.md) waarmee u gegevens analyseren met andere bewakingsgegevens die door Azure Monitor zijn verzameld met behulp van krachtige logboekquery's en ook andere Azure Monitor-functies zoals logboekwaarschuwingen en visualisaties gebruiken. 
- [Gebeurtenishubs](../platform/resource-logs-stream-event-hubs.md) om gegevens te streamen naar externe systemen, zoals SM's van derden en andere oplossingen voor logboekanalyse. 
- [Azure-opslagaccount](../platform/resource-logs-collect-storage.md) dat handig is voor controle, statische analyse of back-up.

Volg de procedure in [Diagnostische instelling maken om platformlogboeken en -statistieken in Azure](../platform/diagnostic-settings.md) te verzamelen om diagnostische instellingen te maken en te beheren via de Azure-portal. Zie [Diagnostische instelling maken in Azure met behulp van een resourcemanagersjabloon](../platform/diagnostic-settings-template.md) om deze in een sjabloon te definiëren en volledige bewaking voor een resource in te schakelen wanneer deze wordt gemaakt.


## <a name="monitoring-in-the-azure-portal"></a>Controle in de Azure-portal
 U hebt toegang tot bewakingsgegevens voor de meeste Azure-bronnen in het menu van de bron in de Azure-portal. Dit geeft u toegang tot de gegevens van één bron met behulp van standaard Azure Monitor-hulpprogramma's. Sommige Azure-services bieden verschillende opties, dus u moet verwijzen naar de documentatie voor die service voor aanvullende informatie. Gebruik het menu **Azure Monitor** om gegevens van alle bewaakte resources te analyseren. 

### <a name="overview"></a>Overzicht
Veel services bevatten monitoringgegevens op hun **overzichtspagina** als een snelle blik op hun werking. Dit is meestal gebaseerd op een subset van platformstatistieken die zijn opgeslagen in Azure Monitor Metrics. Andere bewakingsopties zijn doorgaans beschikbaar in een **sectie Monitoring** van de services. te openen.

![Overzichtspagina](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Inzichten en oplossingen 
Sommige services bieden hulpprogramma's die verder gaan dan de standaardfuncties van Azure Monitor. [Inzichten](../insights/insights-overview.md) bieden een aangepaste monitoringervaring die is gebaseerd op het Azure Monitor-gegevensplatform en standaardfuncties. [Oplossingen](../insights/solutions.md) bieden vooraf gedefinieerde bewakingslogica die is gebouwd op Azure Monitor-logboeken. 

Als een service een Azure Monitor-inzicht heeft, u deze openen **via Monitoring** in het menu van elke bron. Krijg toegang tot alle inzichten en oplossingen in het menu **Azure Monitor.**

![Inzichten](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Metrische gegevens
Analyseer statistieken in de Azure-portal met behulp van [metrics explorer](../platform/metrics-getting-started.md) die beschikbaar is in het **menu-item Metrics** voor de meeste services. Met deze tool u werken met afzonderlijke statistieken of meerdere combineren om correlaties en trends te identificeren. 

- Zie [Aan de slag met Azure Metrics Explorer](../platform/metrics-getting-started.md) voor de basisprincipes van het gebruik van metrics explorer.
- Zie [Geavanceerde functies van Azure Metrics Explorer](../platform/metrics-charts.md) voor geavanceerde functies van metrics explorer, zoals het gebruik van meerdere statistieken en het toepassen van filters en splitsen.

![Metrische gegevens](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Activiteitenlogboek 
Bekijk vermeldingen in het activiteitenlogboek in de Azure-portal met het eerste filter ingesteld op de huidige bron. Kopieer het activiteitenlogboek naar een werkruimte Log Analytics om toegang te krijgen tot het logboek query's en werkmappen. 

- Zie [Azure Activity Log-gebeurtenissen weergeven en ophalen](../platform/activity-log-view.md) voor meer informatie over het weergeven van het activiteitenlogboek en het ophalen van items met verschillende methoden.
- Zie de documentatie voor uw Azure-service voor de specifieke gebeurtenissen die worden geregistreerd.

![Activiteitenlogboek](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken
Azure Monitor Logs consolideert logboeken en statistieken van meerdere services en andere gegevensbronnen voor analyse met een krachtig queryhulpprogramma. Zoals hierboven beschreven, maakt u een diagnostische instelling voor het verzamelen van platformstatistieken, activiteitenlogboeken en bronlogboeken in een Log Analytics-werkruimte in Azure Monitor.

[Met Log Analytics](../log-query/get-started-portal.md) u werken met [logboekquery's,](../log-query/log-query-overview.md)een krachtige functie van Azure Monitor waarmee u geavanceerde analyse van logboekgegevens uitvoeren met behulp van een volledig uitgeruste querytaal. Open Logboekanalyse uit **logboeken** in het menu **Controle** voor een Azure-bron om te werken met logboekquery's met behulp van de bron als [querybereik](../log-query/scope.md#query-scope). Hiermee u gegevens analyseren in meerdere tabellen voor alleen die bron. Gebruik **Logboeken** in het menu Azure Monitor om toegang te krijgen tot logboeken voor alle bronnen. 

- Zie [Aan de slag met logboekquery's in Azure Monitor](../log-query/get-started-queries.md) voor een zelfstudie over het gebruik van de querytaal die wordt gebruikt om logboekquery's te schrijven.
- Zie [Azure-bronlogboeken verzamelen in de werkruimte Log Analytics in Azure Monitor](../platform/resource-logs-collect-workspace.md) voor informatie over hoe resourcelogboeken worden verzameld in Azure Monitor-logboeken en details voor hoe u deze openen in een query.
- Zie [De modus Verzameling](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode) voor een uitleg over de manier waarop resourcelogboekgegevens zijn gestructureerd in Azure Monitor-logboeken.
- Zie de documentatie voor elke Azure-service voor meer informatie over de tabel in Azure Monitor-logboeken.

![Logboeken](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Controle vanaf opdrachtregel
U hebt toegang tot monitoringgegevens die vanuit een opdrachtregel uit uw bron zijn verzameld of u opnemen in een script met [Azure PowerShell](/powershell/azure/) of [Azure Command Line Interface.](/cli/azure/) 

- Zie [CLI-statistiekenreferentie](/cli/azure/monitor/metrics) voor toegang tot metrische gegevens vanuit CLI.
- Zie [CLI Log Analytics-verwijzing](/cli/azure/ext/log-analytics/monitor/log-analytics) voor toegang tot Azure Monitor Logs-gegevens met behulp van een logboekquery van CLI.
- Zie [azure PowerShell-metrische gegevens](/powershell/module/azurerm.insights/get-azurermmetric) voor toegang tot metrische gegevens van Azure PowerShell.
- Zie [Azure PowerShell-queryqueryverwijzing](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) voor toegang tot Azure Monitor-logboekengegevens met behulp van een logboekquery van Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Monitoring vanuit REST API
Neem bewakingsgegevens op die zijn verzameld uit uw bron in een aangepaste toepassing met behulp van een REST-API.

- Zie [Azure Monitoring REST API-walkthrough](../platform/rest-api-walkthrough.md) voor meer informatie over de toegang tot statistieken vanuit de Azure Monitor REST API.
- Zie [Azure Log Analytics REST API](https://dev.loganalytics.io/) voor informatie over de toegang tot Azure Monitor Logs-gegevens met behulp van een logboekquery van Azure PowerShell.

## <a name="alerts"></a>Waarschuwingen
[Waarschuwingen](../platform/alerts-overview.md) stellen u proactief op de hoogte en ondernemen mogelijk actie wanneer belangrijke voorwaarden worden gevonden in uw bewakingsgegevens. U maakt een waarschuwingsregel die een doel voor de waarschuwing definieert, de voorwaarden voor het maken van een waarschuwing en eventuele acties die als reactie moeten worden uitgevoerd.

Verschillende soorten monitoringgegevens worden gebruikt voor verschillende soorten waarschuwingsregels.

- [Waarschuwing voor activiteitenlogboeken](../platform/alerts-activity-log.md) : maak een waarschuwing wanneer een vermelding wordt gemaakt in het activiteitenlogboek dat overeenkomt met specifieke criteria. Hiermee u bijvoorbeeld op de hoogte worden gesteld wanneer een bepaald type resource wordt gemaakt of als een configuratiewijziging mislukt.
- [Metrische waarschuwing](../platform/alerts-metric.md) : maak een waarschuwing wanneer een metrische waarde een bepaalde drempelwaarde overschrijdt. Metrische waarschuwingen reageren sneller dan andere waarschuwingen en kunnen automatisch worden opgelost wanneer het probleem is verholpen.
- [Waarschuwing voor logboekquery](../platform/alerts-log.md) - Voer regelmatig een logboekquery uit en maak een waarschuwing als er een bepaalde voorwaarde wordt gevonden. Hiermee u complexe analyses uitvoeren in meerdere sets gegevens en.

Gebruik **Waarschuwingen** uit het menu van een resource om waarschuwingen weer te geven en waarschuwingsregels voor die bron te beheren. Alleen waarschuwingen voor activiteitenlogboeken en metrische waarschuwingen gebruiken afzonderlijke Azure-bronnen als doel. Logboekquerywaarschuwingen gebruiken een Log Analytics-werkruimte als doel en zijn gebaseerd op een query die toegang heeft tot logboeken die in die werkruimte zijn opgeslagen. Gebruik het menu Azure Monitor om waarschuwingen voor alle resources en de waarschuwingsregels voor logboekquery's weer te geven en te beheren.

- Zie de artikelen voor de verschillende soorten waarschuwingen hierboven voor meer informatie over het maken van waarschuwingsregels.
- Zie [Actiegroepen maken en beheren in de Azure-portal](../platform/action-groups.md) voor meer informatie over het maken van een actiegroep waarmee u reacties op waarschuwingen beheren.



## <a name="next-steps"></a>Volgende stappen

* Zie [Ondersteunde services, schema's en categorieën voor Azure Resource Logs](../platform/diagnostic-logs-schema.md) voor meer informatie over bronlogboeken voor verschillende Azure-services.  
