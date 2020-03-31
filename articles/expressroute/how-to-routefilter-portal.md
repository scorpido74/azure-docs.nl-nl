---
title: 'ExpressRoute: routefilters - Microsoft-peering:Azure-portal'
description: In dit artikel wordt beschreven hoe u routefilters voor Microsoft-peering configureert met behulp van de Azure-portal.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 07/01/2019
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 0b8e06ad5688374e5ab4aaa72d8485e6da797afe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037443"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Routefilters configureren voor Microsoft-peering: Azure-portal
> [!div class="op_single_selector"]
> * [Azure-portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure-CLI](how-to-routefilter-cli.md)
> 

Met routefilters kunt u een subset van ondersteunde services gebruiken via Microsoft-peering. Met de stappen in dit artikel u routefilters voor ExpressRoute-circuits configureren en beheren.

Office 365-services zoals Exchange Online, SharePoint Online en Skype voor Bedrijven en Azure-services zoals opslag en SQL DB zijn toegankelijk via Microsoft-peering. Wanneer Microsoft-peering is geconfigureerd in een ExpressRoute-circuit, worden alle voorvoegsels met betrekking tot deze services geadverteerd via de BGP-sessies die zijn ingesteld. Er wordt aan elk voorvoegsel een BGP-communitywaarde gekoppeld om de service te identificeren die via het voorvoegsel wordt aangeboden. Zie [BGP-community's](expressroute-routing.md#bgp)voor een lijst met de waarden van de BGP-community en de services die ze in kaart brengen.

Als u connectiviteit met alle services nodig hebt, wordt een groot aantal voorvoegsels geadverteerd via BGP. Dit verhoogt aanzienlijk de grootte van de routetabellen die worden onderhouden door routers binnen uw netwerk. Als u van plan bent om slechts een subset van services te gebruiken die via Microsoft-peering worden aangeboden, u de grootte van uw routetabellen op twee manieren verkleinen. U kunt:

- Filter ongewenste voorvoegsels door routefilters toe te passen op BGP-community's. Dit is een standaard netwerkpraktijk en wordt vaak gebruikt binnen veel netwerken.

- Definieer routefilters en pas ze toe op uw ExpressRoute-circuit. Een routefilter is een nieuwe bron waarmee u de lijst met services selecteren die u wilt gebruiken via Microsoft-peering. ExpressRoute-routers verzenden alleen de lijst met voorvoegsels die behoren tot de services die in het routefilter zijn geïdentificeerd.

### <a name="about-route-filters"></a><a name="about"></a>Routefilters

Wanneer Microsoft-peering is geconfigureerd op uw ExpressRoute-circuit, stellen de Microsoft edge-routers een paar BGP-sessies op met de edge-routers (de uwe of die van uw connectiviteitsprovider). Er worden geen routes geadverteerd naar uw netwerk. Als u dit wel wilt doen, moet u een routefilter koppelen.

Via een routefilter kunt u services identificeren die u wilt gebruiken via Microsoft-peering op uw ExpressRoute-circuit. Het is in wezen een lijst van alle BGP-communitywaarden die u wilt toestaan. Zodra er een routefilter is gedefinieerd en aan een ExpressRoute-circuit is gekoppeld, worden alle voorvoegsels die overeenkomen met de BGP-communitywaarden naar uw netwerk geadverteerd.

Als u routefilters met Office 365-services erop wilt koppelen, moet u toestemming hebben om Office 365-services te gebruiken via ExpressRoute. Als u niet gemachtigd bent om Office 365-services via ExpressRoute te gebruiken, mislukt de bewerking voor het koppelen van routefilters. Zie [Azure ExpressRoute voor Office 365 voor](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)meer informatie over het autorisatieproces.

> [!IMPORTANT]
> Microsoft-peering van ExpressRoute-circuits die vóór 1 augustus 2017 zijn geconfigureerd, hebben alle servicevoorvoegsels die worden geadverteerd via Microsoft-peering, zelfs als routefilters niet zijn gedefinieerd. Microsoft-peering van ExpressRoute-circuits die op of na 1 augustus 2017 zijn geconfigureerd, worden pas geadverteerd als er een routefilter aan het circuit is gekoppeld.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Werkstroom

Als u verbinding wilt maken met services via Microsoft-peering, moet u de volgende configuratiestappen uitvoeren:

- U moet beschikken over een actief ExpressRoute-circuit waarop Microsoft peering is ingericht. U de volgende instructies gebruiken om deze taken uit te voeren:
  - [Maak een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) en laat het circuit inschakelen door uw connectiviteitsprovider voordat u verdergaat. Het ExpressRoute-circuit moet in een ingerichte en ingeschakelde staat zijn.
  - [Microsoft-peering maken](expressroute-howto-routing-portal-resource-manager.md) als u de BGP-sessie rechtstreeks beheert. Of laat uw connectiviteitsprovider Microsoft-peering voor uw circuit inrichten.

-  U moet een routefilter maken en configureren.
    - Identificeren van de services waarmee u gebruiken om te gebruiken via Microsoft-peering
    - De lijst met BGP-communitywaarden identificeren die aan de services zijn gekoppeld
    - Een regel maken waarmee de voorvoegsellijst overeenkomt met de waarden van de BGP-community

-  U moet het routefilter aan het ExpressRoute-circuit bevestigen.

## <a name="before-you-begin"></a>Voordat u begint

Controleer voordat u met de configuratie begint of u aan de volgende criteria voldoet:

 - Bekijk de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u met de configuratie begint.

 - U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verder gaat. Het ExpressRoute-circuit moet in een ingerichte en ingeschakelde staat zijn.

 - U moet een actieve Microsoft-peering hebben. Instructies volgen bij [Peering-configuratie maken en wijzigen](expressroute-howto-routing-portal-resource-manager.md)


## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Stap 1: Een lijst met voorvoegsels en BGP-communitywaarden

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Ontvang een lijst met BGP-communitywaarden

BGP-communitywaarden die zijn gekoppeld aan services die toegankelijk zijn via Microsoft-peering, zijn beschikbaar op de pagina [Route-routeringsvereisten.](expressroute-routing.md)

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Maak een lijst met de waarden die u wilt gebruiken

Maak een lijst met [BGP-communitywaarden](expressroute-routing.md#bgp) die u wilt gebruiken in het routefilter. 

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Stap 2: Een routefilter en een filterregel maken

Een routefilter kan slechts één regel hebben en de regel moet van het type 'Toestaan' zijn. Met deze regel kan een lijst met BGP-communitywaarden zijn gekoppeld.

### <a name="1-create-a-route-filter"></a>1. Een routefilter maken
U een routefilter maken door de optie te selecteren om een nieuwe bron te maken. Klik op > Een resource**Networking** > **RouteFilter maken,** zoals in de volgende afbeelding wordt weergegeven: **Create a resource**

![Een routefilter maken](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

U moet het routefilter in een resourcegroep plaatsen. 

![Een routefilter maken](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Een filterregel maken

U regels toevoegen en bijwerken door het tabblad Regel beheren voor uw routefilter te selecteren.

![Een routefilter maken](./media/how-to-routefilter-portal/ManageRouteFilter.png)


U de services selecteren waarmee u verbinding wilt maken in de vervolgkeuzelijst en de regel opslaan wanneer deze is uitgevoerd.

![Een routefilter maken](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Stap 3: Bevestig het routefilter aan een ExpressRoute-circuit

U het routefilter aan een circuit koppelen door de knop 'Circuit toevoegen' te selecteren en het ExpressRoute-circuit te selecteren in de vervolgkeuzelijst.

![Een routefilter maken](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Als de connectiviteitsprovider peering voor uw ExpressRoute-circuit configureert, vernieuwt u het circuit van het circuitblad van ExpressRoute voordat u de knop 'Circuit toevoegen' selecteert.

![Een routefilter maken](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="common-tasks"></a><a name="tasks"></a>Algemene taken

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>De eigenschappen van een routefilter opte krijgen

U eigenschappen van een routefilter weergeven wanneer u de bron in de portal opent.

![Een routefilter maken](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>De eigenschappen van een routefilter bijwerken

U de lijst met BGP-communitywaarden die aan een circuit zijn gekoppeld bijwerken door de knop Regel beheren te selecteren.


![Een routefilter maken](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Een routefilter maken](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Een routefilter loskoppelen van een ExpressRoute-circuit

Als u een circuit losmaakt van het routefilter, klikt u met de rechtermuisknop op het circuit en klikt u op "loskoppelen".

![Een routefilter maken](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Een routefilter verwijderen

U een routefilter verwijderen door de knop Verwijderen te selecteren. 

![Een routefilter maken](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Volgende stappen

* Zie de Veelgestelde vragen over [ExpressRoute voor](expressroute-faqs.md)meer informatie over ExpressRoute.

* Zie Routerconfiguratievoorbeelden voor informatie over routerconfiguratievoorbeelden [voor het instellen en beheren van routering.](expressroute-config-samples-routing.md) 
