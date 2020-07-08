---
title: 'ExpressRoute: route filters-micro soft-peering: Azure Portal'
description: In dit artikel wordt beschreven hoe u route filters configureert voor micro soft-peering met behulp van de Azure Portal.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 07/01/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 54674be0010bd062cfe6263db4167a24805a9e5a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84727123"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Route filters voor micro soft-peering configureren: Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure-CLI](how-to-routefilter-cli.md)
> 

Met routefilters kunt u een subset van ondersteunde services gebruiken via Microsoft-peering. Met de stappen in dit artikel kunt u route filters configureren en beheren voor ExpressRoute-circuits.

Office 365-services zoals Exchange Online, share point online en Skype voor bedrijven, en Azure-Services, zoals Storage en SQL DB, zijn toegankelijk via micro soft-peering. Wanneer micro soft-peering is geconfigureerd in een ExpressRoute-circuit, worden alle voor voegsels die betrekking hebben op deze services, geadverteerd via de BGP-sessies die zijn ingesteld. Er wordt aan elk voorvoegsel een BGP-communitywaarde gekoppeld om de service te identificeren die via het voorvoegsel wordt aangeboden. Zie [BGP-community's](expressroute-routing.md#bgp)voor een lijst met de waarden van de BGP-Community en de services waaraan ze zijn toegewezen.

Als u verbinding met alle services nodig hebt, worden er een groot aantal voor voegsels via BGP geadverteerd. Dit verg root de grootte van de route tabellen die worden beheerd door routers in uw netwerk. Als u van plan bent slechts een subset van services te gebruiken die worden aangeboden via micro soft-peering, kunt u de grootte van uw route tabellen op twee manieren verkleinen. U kunt:

- Ongewenste voor voegsels uitfilteren door route filters op BGP-community's toe te passen. Dit is een standaard netwerk praktijk en wordt meestal gebruikt in veel netwerken.

- Definieer route filters en pas deze toe op uw ExpressRoute-circuit. Een route filter is een nieuwe resource waarmee u de lijst met Services kunt selecteren die u wilt gebruiken via micro soft-peering. ExpressRoute-routers verzenden alleen de lijst met voor voegsels die horen bij de services die zijn geïdentificeerd in het route filter.

### <a name="about-route-filters"></a><a name="about"></a>Over route filters

Wanneer micro soft-peering is geconfigureerd op uw ExpressRoute-circuit, worden door de micro soft Edge-routers een paar BGP-sessies tot stand gebracht met de Edge-routers (of uw connectiviteits provider). Er worden geen routes geadverteerd naar uw netwerk. Als u dit wel wilt doen, moet u een routefilter koppelen.

Via een routefilter kunt u services identificeren die u wilt gebruiken via Microsoft-peering op uw ExpressRoute-circuit. Het is in feite een lijst met alle BGP-Community-waarden die u wilt toestaan. Zodra er een routefilter is gedefinieerd en aan een ExpressRoute-circuit is gekoppeld, worden alle voorvoegsels die overeenkomen met de BGP-communitywaarden naar uw netwerk geadverteerd.

Als u route filters met Office 365-Services wilt koppelen, moet u gemachtigd zijn om Office 365-services te gebruiken via ExpressRoute. Als u niet gemachtigd bent om Office 365-services te gebruiken via ExpressRoute, mislukt de bewerking voor het koppelen van route filters. Zie voor meer informatie over het autorisatie proces [Azure ExpressRoute voor Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Micro soft-peering van ExpressRoute-circuits die zijn geconfigureerd vóór 1 augustus 2017, heeft alle service voorvoegsels die worden geadverteerd via micro soft-peering, zelfs als er geen route filters zijn gedefinieerd. Micro soft-peering van ExpressRoute-circuits die zijn geconfigureerd op of na 1 augustus 2017, heeft geen voor voegsels die worden geadverteerd totdat een route filter aan het circuit is gekoppeld.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Werkstroom

Om verbinding te kunnen maken met Services via micro soft-peering, moet u de volgende configuratie stappen uitvoeren:

- U moet een actief ExpressRoute-circuit hebben dat micro soft-peering heeft ingericht. U kunt de volgende instructies gebruiken om deze taken uit te voeren:
  - [Maak een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) en laat het circuit ingeschakeld door uw connectiviteits provider voordat u verdergaat. Het ExpressRoute-circuit moet de status ingericht en ingeschakeld hebben.
  - [Maak micro soft-peering](expressroute-howto-routing-portal-resource-manager.md) als u de BGP-sessie rechtstreeks beheert. Of zorg ervoor dat uw connectiviteits provider micro soft-peering voor uw circuit inricht.

-  U moet een route filter maken en configureren.
    - Identificeer de services die u wilt gebruiken via micro soft-peering
    - De lijst met BGP Community-waarden identificeren die zijn gekoppeld aan de services
    - Een regel maken om de voorvoegsel lijst toe te staan die overeenkomt met de waarden van de BGP-Community

-  U moet het route filter koppelen aan het ExpressRoute-circuit.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u begint met de configuratie, moet u voldoen aan de volgende criteria:

 - Controleer de [vereisten](expressroute-prerequisites.md) en [werk stromen](expressroute-workflows.md) voordat u begint met de configuratie.

 - U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verder gaat. Het ExpressRoute-circuit moet de status ingericht en ingeschakeld hebben.

 - U moet een actieve micro soft-peering hebben. Volg de instructies bij het [maken en wijzigen van de peering-configuratie](expressroute-howto-routing-portal-resource-manager.md)


## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Stap 1: een lijst met voor voegsels en waarden voor de BGP-Community ophalen

### <a name="1-get-a-list-of-bgp-community-values"></a>1. een lijst met BGP Community-waarden ophalen

BGP Community-waarden die zijn gekoppeld aan services die toegankelijk zijn via micro soft-peering, zijn beschikbaar op de pagina [ExpressRoute-routerings vereisten](expressroute-routing.md) .

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Maak een lijst met de waarden die u wilt gebruiken

Maak een lijst met de [BGP-Community-waarden](expressroute-routing.md#bgp) die u wilt gebruiken in het route filter. 

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Stap 2: een route filter en een filter regel maken

Een route filter kan slechts één regel bevatten en de regel moet van het type toestaan zijn. Aan deze regel kan een lijst met BGP Community-waarden zijn gekoppeld.

### <a name="1-create-a-route-filter"></a>1. een route filter maken
U kunt een route filter maken door de optie voor het maken van een nieuwe resource te selecteren. Klik op **een resource maken**  >  **netwerk**  >  -**RouteFilter**, zoals wordt weer gegeven in de volgende afbeelding:

![Een route filter maken](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

U moet het route filter in een resource groep plaatsen. 

![Een route filter maken](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. een filter regel maken

U kunt regels toevoegen en bijwerken door het tabblad regel beheren voor uw route filter te selecteren.

![Een route filter maken](./media/how-to-routefilter-portal/ManageRouteFilter.png)


U kunt de services selecteren waarmee u verbinding wilt maken in de vervolg keuzelijst en de regel opslaan wanneer u klaar bent.

![Een route filter maken](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Stap 3: het route filter koppelen aan een ExpressRoute-circuit

U kunt het route filter koppelen aan een circuit door de knop circuit toevoegen te selecteren en het ExpressRoute-circuit te selecteren in de vervolg keuzelijst.

![Een route filter maken](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Als de connectiviteits provider peering voor uw ExpressRoute-circuit configureert, vernieuwt u het circuit van de Blade ExpressRoute circuit voordat u de knop circuit toevoegen selecteert.

![Een route filter maken](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="common-tasks"></a><a name="tasks"></a>Algemene taken

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>De eigenschappen van een route filter ophalen

U kunt de eigenschappen van een route filter weer geven wanneer u de resource in de portal opent.

![Een route filter maken](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>De eigenschappen van een route filter bijwerken

U kunt de lijst met BGP Community-waarden die zijn gekoppeld aan een circuit bijwerken door de knop regel beheren te selecteren.


![Een route filter maken](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Een route filter maken](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Een route filter loskoppelen van een ExpressRoute-circuit

Als u een circuit wilt ontkoppelen van het route filter, klikt u met de rechter muisknop op het circuit en klikt u op de koppeling loskoppelen.

![Een route filter maken](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Een route filter verwijderen

U kunt een route filter verwijderen door de knop verwijderen te selecteren. 

![Een route filter maken](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Volgende stappen

* Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md)voor meer informatie over ExpressRoute.

* Zie voor meer informatie over router configuratie voorbeelden router [configuratie-voor beelden voor het instellen en beheren van route ring](expressroute-config-samples-routing.md). 
