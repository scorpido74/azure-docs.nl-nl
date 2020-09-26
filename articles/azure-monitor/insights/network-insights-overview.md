---
title: Azure Monitor voor netwerken (preview-versie)
description: Een kort overzicht van Azure Monitor voor het netwerk, dat een uitgebreide weer gave van de status en metrische gegevens voor alle geïmplementeerde netwerk bronnen biedt zonder enige configuratie.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/24/2020
ms.openlocfilehash: 2559c4f54aa19df248ddf756e376809dea516997
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330949"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor voor netwerken (preview-versie)
Azure Monitor voor netwerk biedt een uitgebreid overzicht van de [status](https://docs.microsoft.com/azure/service-health/resource-health-checks-resource-types) en [metrische gegevens](../platform/metrics-supported.md) voor alle geïmplementeerde netwerk bronnen zonder enige configuratie.  Het biedt ook toegang tot alle mogelijkheden voor netwerk bewaking, zoals [verbindings monitor](../../network-watcher/connection-monitor-preview.md), [flow logboek registratie voor netwerk beveiligings groepen (nsg's)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md), [Traffic Analytics](../../network-watcher/traffic-analytics.md)en andere [Diagnostische](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) functies voor het netwerk.

Azure Monitor voor netwerken is gestructureerd rond de volgende belang rijke onderdelen van bewaking:
- [Netwerk status en-metrische gegevens](#networkhealth)
- [Connectiviteit](#connectivity)
- [Verkeer](#traffic)
- [Diagnose Toolkit](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Netwerk status en-metrische gegevens

De **overzichts** pagina Azure monitor voor netwerken biedt een probleemloze manier om de inventaris van uw netwerk bronnen samen met de resource status en-waarschuwingen te visualiseren. Het is onderverdeeld in vier functionele gebieden: zoeken en filteren, Resource Health en metrische gegevens, waarschuwingen. en afhankelijkheids weergave

![Overzichtspagina](media/network-insights-overview/overview.png)

### <a name="search-and-filtering"></a>Zoeken en filteren
De weer gave resource status en waarschuwingen kan worden aangepast met filters zoals het **abonnement**, de **resource groep** en het **resource type**. Het zoekvak biedt de mogelijkheid om de resource-eigenschappen te doorzoeken.

Het zoekvak kan worden gebruikt om te zoeken naar resources en gekoppelde resources. Een openbaar IP-adres is bijvoorbeeld gekoppeld aan een Application Gateway. Als u zoekt naar de open bare Ip's DNS-naam, worden zowel het open bare IP-adres als de bijbehorende Application Gateway geïdentificeerd.

![Azure Monitor voor netwerken-zoeken](media/network-insights-overview/search.png)


### <a name="resource-health-and-metric"></a>Resource Health en metrische gegevens
Elke tegel vertegenwoordigt een resource type, met het aantal exemplaren dat is geïmplementeerd in alle abonnementen die zijn geselecteerd samen met de status van de resource. In het onderstaande voor beeld zijn er 45 er-en VPN-verbindingen geïmplementeerd, 44 zijn in orde en 1 niet beschikbaar.

![Azure Monitor voor netwerken-resource status](media/network-insights-overview/resource-health.png)

Als u op de niet-beschik bare geen en VPN-verbindingen klikt, wordt een metrische weer gave gestart. 

![Azure Monitor voor netwerken-metrische weer gave](media/network-insights-overview/metric-view.png)

U kunt klikken op elk element in de raster weergave. Klik op het pictogram status om over te leiden naar de resource status voor die verbinding. Klik op waarschuwingen om respectievelijk de pagina waarschuwingen en metrische gegevens voor die verbinding om te leiden. 

### <a name="alerts"></a>Waarschuwingen
In het raster **waarschuwingen** aan de rechter kant ziet u een overzicht van alle waarschuwingen die zijn gegenereerd voor de geselecteerde resources in alle abonnementen. Klik op het aantal waarschuwingen om naar de pagina gedetailleerde waarschuwingen te gaan.

### <a name="dependency-view"></a>Afhankelijkheids weergave
De weer gave **afhankelijkheid** helpt bij het configureren van de configuratie van de resource. De huidige afhankelijkheids weergave wordt nu ondersteund voor Application Gateway, virtueel WAN en Load Balancer. In het geval van Application Gateway is de afhankelijkheids weergave bijvoorbeeld toegankelijk door te klikken op de naam van de Application Gateway resource in de raster weergave metrische gegevens. Dit geldt ook voor virtuele WAN-en Load Balancer.

![Azure Monitor voor netwerken-Application Gateway weer gave](media/network-insights-overview/application-gateway.png)

De **afhankelijkheids** weergave voor Application Gateway biedt een vereenvoudigde weer gave van de manier waarop de front-end ip's zijn verbonden met de listeners, de regels en de back-end-pool. De verbindings randen worden in kleur gecodeerd en bieden aanvullende informatie op basis van de status van de back-end-pool. De weer gave biedt ook een gedetailleerde weer gave van Application Gateway metrische gegevens en metrische gegevens voor alle gerelateerde back-end-groepen, zoals schaal sets voor virtuele machines en VM-exemplaren.

![Azure Monitor voor de weer gave van een afhankelijkheid van netwerken](media/network-insights-overview/dependency-view.png)

Met de afhankelijkheids grafiek kunt u eenvoudig navigeren naar configuratie-instellingen. Klik met de rechter muisknop op een back-end-groep voor toegang tot andere functionaliteit. Als de back-end-pool bijvoorbeeld een virtuele machine is, hebt u rechtstreeks toegang tot VM Insights en Network Watcher verbindings problemen oplossen.

![Azure Monitor voor het menu weergave van de afhankelijkheid van netwerken](media/network-insights-overview/dependency-view-menu.png)

De zoek-en filter balk in de afhankelijkheids weergave bieden een probleemloze manier om de grafiek te doorzoeken. Als u bijvoorbeeld zoekt naar *AppGWTestRule* in het voor beeld hieronder, wordt de grafische weer gave beperkt tot alle knoop punten die zijn verbonden via *AppGWTestRule*.

![Azure Monitor voor netwerken-voor beeld van zoeken](media/network-insights-overview/search-example.png)

Verschillende filters bieden hulp om te beperken tot een specifiek pad en de status. Selecteer bijvoorbeeld alleen *slechte* status van de vervolg keuzelijst **status** om alle randen weer te geven waar *de status slecht is.*

Klik op **gedetailleerde metrische weer gave** om een vooraf geconfigureerde werkmap te openen met gedetailleerde metrische gegevens voor de Application Gateway, alle bronnen van de back-endadresgroep en front-end ip's. 

## <a name="connectivity"></a><a name="connectivity"></a>Connectiviteit

Het tabblad **connectiviteit** biedt een moeiteloze manier om alle tests te visualiseren die zijn geconfigureerd met behulp van verbindings monitor en [verbindings monitor (preview)](../../network-watcher/connection-monitor-preview.md) voor de geselecteerde set abonnementen.

![Tabblad connectiviteit in Azure Monitor voor netwerken](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

Tests worden gegroepeerd op bron-en doel tegels en de bereik baarheids status voor elke test weer gegeven. Bereik bare instellingen bieden een eenvoudige toegang voor het configureren van de bereik baarheids criteria op basis van mislukte controles (%) en RTT (MS). Zodra de waarden zijn ingesteld, wordt de status voor elke test bijgewerkt op basis van de selectie criteria.

![Connectiviteits tests in Azure Monitor voor netwerken](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)

Als u op een tegel bron of doel klikt, wordt een metrische weer gave geopend.

![Verbindings gegevens in Azure Monitor voor netwerken](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)


U kunt klikken op elk element in de raster weergave. Klik op het pictogram **bereik baarheid** om door te sturen naar de portal-pagina van de **verbindings monitor** om de hop weer te geven op basis van de hop-topologie en problemen die invloed hebben op de connectiviteit. Klik op **waarschuwingen** om naar waarschuwingen te sturen en controleer de **tijd van mislukte percentages/retour tijden** om om te leiden naar de pagina metrische gegevens voor de geselecteerde verbindings monitor.

Het raster **waarschuwingen**   aan de rechter kant biedt een overzicht van alle waarschuwingen die zijn gegenereerd voor de connectiviteits tests die zijn geconfigureerd voor alle abonnementen. Klik op het aantal waarschuwingen om naar de pagina gedetailleerde waarschuwingen te gaan.

## <a name="traffic"></a><a name="traffic"></a>Verkeer
Het tabblad verkeer biedt toegang tot alle Nsg's die zijn geconfigureerd voor [NSG-stroom logboeken](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) en [Traffic Analytics](../../network-watcher/traffic-analytics.md) voor de geselecteerde set abonnementen en gegroepeerd op locaties. Met de zoek functionaliteit op dit tabblad kunt u de Nsg's die zijn geconfigureerd voor het gezochte IP-adres identificeren. U kunt zoeken naar een wille keurig IP-adres in uw omgeving. in de weer gave naast elkaar worden alle Nsg's weer gegeven samen met de NSG-stroom logboeken en de configuratie status van Traffic Analytics.

![Verkeers weergave in Azure Monitor voor netwerken](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)

Als u op een tegel gebied klikt, wordt een raster weergave geopend waarmee u NSG-stroom logboeken en Traffic Analytics eenvoudig kunt weer geven en configureren.  

![Weer gave van verkeers regio's in Azure Monitor voor netwerken](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)

U kunt klikken op elk element in de raster weergave. Klik op configuratie status om het NSG-stroom logboek en de configuratie van de Traffic Analytics te bewerken. Klik op waarschuwingen om over te leiden naar de verkeers waarschuwingen die zijn geconfigureerd voor de geselecteerde NSG. U kunt ook naar de Traffic Analytics weer gave gaan door te klikken op de werk ruimte.  

In het raster **waarschuwingen**   aan de rechter kant ziet u een overzicht van alle waarschuwingen op basis van Traffic Analytics werk ruimte in alle abonnementen. Klik op het aantal waarschuwingen om naar de pagina gedetailleerde waarschuwingen te gaan.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Diagnose Toolkit
Diagnostische Toolkit biedt toegang tot alle diagnostische functies die beschikbaar zijn voor het oplossen van problemen met het netwerk. In deze vervolg keuzelijst krijgt u toegang tot functies als [pakket opname](../../network-watcher/network-watcher-packet-capture-overview.md), [VPN-probleem oplossing](../../network-watcher/network-watcher-troubleshoot-overview.md), [probleem oplossing voor verbindingen](../../network-watcher/network-watcher-connectivity-overview.md), [volgende hop](../../network-watcher/network-watcher-next-hop-overview.md) en [IP-stroom controleren](../../network-watcher/network-watcher-ip-flow-verify-overview.md).

![Het tabblad Diagnostische Toolkit](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over netwerk bewaking bij [Wat is Azure Network Watcher?](../../network-watcher/network-watcher-monitoring-overview.md).
