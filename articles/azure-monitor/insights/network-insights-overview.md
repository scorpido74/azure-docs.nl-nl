---
title: Azure Monitor voor netwerken (preview-versie)
description: Een kort overzicht van Azure Monitor voor het netwerk, dat een uitgebreide weer gave van de status en metrische gegevens voor alle geïmplementeerde netwerk bronnen biedt zonder enige configuratie.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 0f5b2fbd13cb9658e255fde727e115df748aaed9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77654865"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor voor netwerken (preview-versie)
Azure Monitor voor netwerk biedt een uitgebreid overzicht van de status en metrische gegevens voor alle geïmplementeerde netwerk bronnen zonder enige configuratie. De geavanceerde zoek functie helpt u bij het identificeren van bron afhankelijkheden, het inschakelen van scenario's zoals het identificeren van resources die als host fungeren voor uw website door eenvoudigweg te zoeken naar gehoste website naam.

De **overzichts** pagina Azure monitor voor netwerken biedt een probleemloze manier om de inventaris van uw netwerk bronnen samen met de resource status en-waarschuwingen te visualiseren. Het is onderverdeeld in vier functionele gebieden:

- Zoeken en filteren
- Resource Health en metrische gegevens
- Waarschuwingen 
- Afhankelijkheids weergave

![Overzichtspagina](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Zoeken en filteren
De weer gave resource status en waarschuwingen kan worden aangepast met filters zoals het **abonnement**, de **resource groep** en het **resource type**. Het zoekvak biedt de mogelijkheid om de resource-eigenschappen te doorzoeken.

Het zoekvak kan worden gebruikt om te zoeken naar resources en gekoppelde resources. Een openbaar IP-adres is bijvoorbeeld gekoppeld aan een Application Gateway. Als u zoekt naar de open bare Ip's DNS-naam, worden zowel het open bare IP-adres als de bijbehorende Application Gateway geïdentificeerd.

![Search](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Resource Health en metrische gegevens
Elke tegel vertegenwoordigt een resource type, met het aantal exemplaren dat is geïmplementeerd in alle abonnementen die zijn geselecteerd samen met de status van de resource. In het onderstaande voor beeld zijn er 105 er-en VPN-verbindingen geïmplementeerd, 103 zijn in orde en 2 niet beschikbaar.

![Status van resources](media/network-insights-overview/resource-health.png)

Als u op de twee niet-beschik bare en VPN-verbindingen klikt, wordt er een metrische weer gave geopend. 

![Metrische weer gave](media/network-insights-overview/metric-view.png)

U kunt klikken op elk element in de raster weergave. Klik op het pictogram status om over te leiden naar de resource status voor die verbinding. Klik op waarschuwingen om respectievelijk de pagina waarschuwingen en metrische gegevens voor die verbinding om te leiden. 

## <a name="alerts"></a>Waarschuwingen
In het raster **waarschuwingen** aan de rechter kant ziet u een overzicht van alle waarschuwingen die zijn gegenereerd voor de geselecteerde resources in alle abonnementen. Klik op het aantal waarschuwingen om naar de pagina gedetailleerde waarschuwingen te gaan.

## <a name="dependency-view"></a>Afhankelijkheids weergave
De weer gave **afhankelijkheid** helpt bij het configureren van de configuratie van de resource. Momenteel wordt de afhankelijkheids weergave alleen ondersteund voor Application Gateway. U kunt de afhankelijkheids weergave openen door te klikken op de Application Gateway resource naam in de raster weergave metrische gegevens.

![Application Gateway weer gave](media/network-insights-overview/application-gateway.png)

De **afhankelijkheids** weergave voor Application Gateway biedt een vereenvoudigde weer gave van de manier waarop de front-end ip's zijn verbonden met de listeners, de regels en de back-end-pool. De verbindings randen worden in kleur gecodeerd en bieden aanvullende informatie op basis van de status van de back-end-pool. De weer gave biedt ook een gedetailleerde weer gave van Application Gateway metrische gegevens en metrische gegevens voor alle gerelateerde back-end-groepen, zoals VMSS-en VM-exemplaren.

![Afhankelijkheids weergave](media/network-insights-overview/dependency-view.png)

Met de afhankelijkheids grafiek kunt u eenvoudig navigeren naar configuratie-instellingen. Klik met de rechter muisknop op een back-end-groep voor toegang tot andere functionaliteit. Als de back-end-pool bijvoorbeeld een virtuele machine is, hebt u rechtstreeks toegang tot VM Insights en kunt u problemen met Network Watcher verbinding oplossen om verbindings problemen te identificeren.

![Menu van afhankelijkheids weergave](media/network-insights-overview/dependency-view-menu.png)

De zoek-en filter balk in de afhankelijkheids weergave bieden een probleemloze manier om de grafiek te doorzoeken. Als u bijvoorbeeld zoekt naar *AppGWTestRule* in het voor beeld hieronder, wordt de grafische weer gave beperkt tot alle knoop punten die zijn verbonden via *AppGWTestRule*. 

![Zoek voorbeeld](media/network-insights-overview/search-example.png)

Verschillende filters bieden hulp om te beperken tot een specifiek pad en de status. Selecteer bijvoorbeeld alleen *slechte* status van de vervolg keuzelijst **status** om alle randen weer te geven waar *de status slecht is.*

Klik op **gedetailleerde metrische weer gave** om een vooraf geconfigureerde werkmap te starten met gedetailleerde metrische gegevens voor de toepassings gateway, alle bronnen van de back-endadresgroep en front-end ip's. 

## <a name="next-steps"></a>Volgende stappen 

- Meer informatie over netwerk bewaking bij wat is [Azure Network Watcher?](/azure/network-watcher/network-watcher-monitoring-overview).
