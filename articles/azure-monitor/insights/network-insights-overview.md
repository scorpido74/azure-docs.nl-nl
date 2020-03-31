---
title: Azure-monitor voor netwerken (voorbeeld)
description: Een snel overzicht voor Azure Monitor for Network met een uitgebreide weergave van status en statistieken voor alle geïmplementeerde netwerkbronnen zonder enige configuratie.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 0f5b2fbd13cb9658e255fde727e115df748aaed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654865"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure-monitor voor netwerken (voorbeeld)
Azure Monitor for Network biedt een uitgebreid overzicht van status en statistieken voor alle geïmplementeerde netwerkbronnen zonder enige configuratie. De geavanceerde zoekmogelijkheid helpt bij het identificeren van bronafhankelijkheden, waardoor scenario's kunnen worden geïdentificeerd, zoals het identificeren van bronnen die uw website hosten door simpelweg te zoeken naar de naam van de gehoste website.

De overzichtspagina **Overview** van Azure Monitor for Networks biedt een moeiteloze manier om de voorraad van uw netwerkbronnen te visualiseren, samen met de status van resources en waarschuwingen. Het is verdeeld in vier belangrijke functionele gebieden:

- Zoeken en filteren
- Resourcestatus en statistieken
- Waarschuwingen 
- Afhankelijkheidsweergave

![Overzichtspagina](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Zoeken en filteren
De weergave resourcestatus en waarschuwingen kan worden aangepast met filters zoals **Abonnement,** **Resourcegroep** en **Resourcetype**. Het zoekvak biedt de mogelijkheid om door resourceeigenschappen te zoeken.

Het zoekvak kan worden gebruikt om te zoeken naar bronnen en bijbehorende bronnen. Een openbaar IP-adres is bijvoorbeeld gekoppeld aan een toepassingsgateway. Als u zoekt naar de DNS-naam openbare IP-gegevens, wordt zowel openbaar IP als de bijbehorende toepassingsgateway geïdentificeerd.

![Search](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Resourcestatus en metrische gegevens
Elke tegel vertegenwoordigt een resourcetype, waarbij het aantal exemplaren dat is geïmplementeerd voor alle abonnementen die zijn geselecteerd, samen met de status van de resourcestatus. In het onderstaande voorbeeld zijn er 105 ER- en VPN-verbindingen geïmplementeerd, 103 zijn gezond en 2 niet beschikbaar.

![Status van resources](media/network-insights-overview/resource-health.png)

Als u op de twee niet beschikbare ER- en VPN-verbindingen klikt, wordt een metrische weergave gestart. 

![Metrische weergave](media/network-insights-overview/metric-view.png)

U op elk element in de rasterweergave klikken. Klik op het pictogram Gezondheid om om te leiden naar resourcestatus voor die verbinding. Klik op Waarschuwingen om voor die verbinding naar respectievelijk de pagina waarschuwingen en statistieken te verwijzen. 

## <a name="alerts"></a>Waarschuwingen
Het **raster Waarschuwingen** aan de rechterkant geeft een overzicht van alle waarschuwingen die zijn gegenereerd voor de geselecteerde bronnen voor alle abonnementen. Klik op het aantal waarschuwingen om naar de pagina met gedetailleerde waarschuwingen te navigeren.

## <a name="dependency-view"></a>Afhankelijkheidsweergave
De **afhankelijkheidsweergave** helpt bij het visualiseren van de manier waarop de resource is geconfigureerd. Momenteel wordt de afhankelijkheidsweergave alleen ondersteund voor Application Gateway. Afhankelijkheidsweergave kan worden geopend door te klikken op de naam van de application gateway-bron vanuit de weergave metrische gegevensraster.

![Weergave Toepassingsgateway](media/network-insights-overview/application-gateway.png)

De **afhankelijkheidsweergave** voor Application Gateway biedt een vereenvoudigde weergave van hoe de front-end IP's zijn verbonden met de listeners, regels en backendpool. De verbindingsranden zijn kleurgecodeerd en bieden aanvullende details op basis van de status van de backendpool. De weergave biedt ook een gedetailleerd overzicht van de statistieken en statistieken van application gateway voor alle gerelateerde backendpools, zoals VMSS- en VM-exemplaren.

![Afhankelijkheidsweergave](media/network-insights-overview/dependency-view.png)

De afhankelijkheidsgrafiek maakt eenvoudige navigatie naar configuratie-instellingen mogelijk. Klik met de rechtermuisknop op een backend pool om toegang te krijgen tot andere functionaliteit. Als de backendpool bijvoorbeeld een vm is, u rechtstreeks toegang krijgen tot de problemen met de VM Insights en de Network Watcher-verbinding om verbindingsproblemen te identificeren.

![Menu Afhankelijkheidsweergave](media/network-insights-overview/dependency-view-menu.png)

De zoek- en filterbalk van de afhankelijkheidsweergave biedt een moeiteloze manier om door de grafiek te zoeken. Als u bijvoorbeeld in het onderstaande voorbeeld naar *AppGWTestRule* zoekt, wordt de grafische weergave beperkt tot alle knooppunten die via *AppGWTestRule*zijn verbonden. 

![Voorbeeld van zoeken](media/network-insights-overview/search-example.png)

Verschillende filters bieden hulp om te beperken tot een specifiek pad en een bepaalde status. Selecteer bijvoorbeeld alleen *Niet in orde zijn* in de vervolgkeuzelijst **Status** om alle randen weer te geven waar de status Niet *in orde*is.

Klik op **Gedetailleerde metrische weergave** om een vooraf geconfigureerde werkmap te starten met gedetailleerde statistieken voor de toepassingsgateway, alle backend-poolbronnen en ip-opslag aan de voorkant. 

## <a name="next-steps"></a>Volgende stappen 

- Meer informatie over netwerkbewaking bij Wat is [Azure Network Watcher?](/azure/network-watcher/network-watcher-monitoring-overview)
