---
title: 'ExpressRoute: routefilters - Microsoft-peering:Azure CLI'
description: In dit artikel wordt beschreven hoe u routefilters voor Microsoft Peering configureert met Azure CLI
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman
ms.openlocfilehash: c3c50a005e119890fb17fcf7b3114a747bbe34bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033420"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Routefilters configureren voor Microsoft-peering: Azure CLI

> [!div class="op_single_selector"]
> * [Azure-portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure-CLI](how-to-routefilter-cli.md)
> 

Met routefilters kunt u een subset van ondersteunde services gebruiken via Microsoft-peering. Met de stappen in dit artikel u routefilters voor ExpressRoute-circuits configureren en beheren.

Office 365-services zoals Exchange Online, SharePoint Online en Skype voor Bedrijven zijn toegankelijk via microsoft-peering. Wanneer Microsoft-peering is geconfigureerd in een ExpressRoute-circuit, worden alle voorvoegsels met betrekking tot deze services geadverteerd via de BGP-sessies die zijn ingesteld. Er wordt aan elk voorvoegsel een BGP-communitywaarde gekoppeld om de service te identificeren die via het voorvoegsel wordt aangeboden. Zie [BGP-community's](expressroute-routing.md#bgp)voor een lijst met de waarden van de BGP-community en de services die ze in kaart brengen.

Als u connectiviteit met alle services nodig hebt, wordt een groot aantal voorvoegsels geadverteerd via BGP. Dit verhoogt aanzienlijk de grootte van de routetabellen die worden onderhouden door routers binnen uw netwerk. Als u van plan bent om slechts een subset van services te gebruiken die via Microsoft-peering worden aangeboden, u de grootte van uw routetabellen op twee manieren verkleinen. U kunt:

* Filter ongewenste voorvoegsels door routefilters toe te passen op BGP-community's. Dit is een standaard netwerkpraktijk en wordt vaak gebruikt binnen veel netwerken.

* Definieer routefilters en pas ze toe op uw ExpressRoute-circuit. Een routefilter is een nieuwe bron waarmee u de lijst met services selecteren die u wilt gebruiken via Microsoft-peering. ExpressRoute-routers verzenden alleen de lijst met voorvoegsels die behoren tot de services die in het routefilter zijn geïdentificeerd.

### <a name="about-route-filters"></a><a name="about"></a>Routefilters

Wanneer Microsoft-peering is geconfigureerd op uw ExpressRoute-circuit, stellen de Microsoft edge-routers een paar BGP-sessies op met de edge-routers (de uwe of die van uw connectiviteitsprovider). Er worden geen routes geadverteerd naar uw netwerk. Als u dit wel wilt doen, moet u een routefilter koppelen.

Via een routefilter kunt u services identificeren die u wilt gebruiken via Microsoft-peering op uw ExpressRoute-circuit. Het is in feite een lijst met alle toegestane BGP-communitywaarden. Zodra er een routefilter is gedefinieerd en aan een ExpressRoute-circuit is gekoppeld, worden alle voorvoegsels die overeenkomen met de BGP-communitywaarden naar uw netwerk geadverteerd.

Als u routefilters met Office 365-services erop wilt koppelen, moet u toestemming hebben om Office 365-services te gebruiken via ExpressRoute. Als u niet gemachtigd bent om Office 365-services via ExpressRoute te gebruiken, mislukt de bewerking voor het koppelen van routefilters. Zie [Azure ExpressRoute voor Office 365 voor](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)meer informatie over het autorisatieproces.

> [!IMPORTANT]
> Microsoft-peering van ExpressRoute-circuits die vóór 1 augustus 2017 zijn geconfigureerd, hebben alle servicevoorvoegsels die worden geadverteerd via Microsoft-peering, zelfs als routefilters niet zijn gedefinieerd. Microsoft-peering van ExpressRoute-circuits die op of na 1 augustus 2017 zijn geconfigureerd, worden pas geadverteerd als er een routefilter aan het circuit is gekoppeld.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Werkstroom

Als u verbinding wilt maken met services via Microsoft-peering, moet u de volgende configuratiestappen uitvoeren:

* U moet beschikken over een actief ExpressRoute-circuit waarop Microsoft peering is ingericht. U de volgende instructies gebruiken om deze taken uit te voeren:
  * [Maak een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit inschakelen door uw connectiviteitsprovider voordat u verdergaat. Het ExpressRoute-circuit moet in een ingerichte en ingeschakelde staat zijn.
  * [Microsoft-peering maken](howto-routing-cli.md) als u de BGP-sessie rechtstreeks beheert. Of laat uw connectiviteitsprovider Microsoft-peering voor uw circuit inrichten.

* U moet een routefilter maken en configureren.
  * Identificeren van de services waarmee u gebruiken om te gebruiken via Microsoft-peering
  * De lijst met BGP-communitywaarden identificeren die aan de services zijn gekoppeld
  * Een regel maken waarmee de voorvoegsellijst overeenkomt met de waarden van de BGP-community

* U moet het routefilter aan het ExpressRoute-circuit bevestigen.

## <a name="before-you-begin"></a>Voordat u begint

Installeer eerst de meest recente versie van de CLI-opdrachten (2.0 of hoger). Zie [Azure CLI installeren](/cli/azure/install-azure-cli) en [Aan de slag met Azure CLI](/cli/azure/get-started-with-azure-cli) voor meer informatie over de CLI-opdrachten.

* Bekijk de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u met de configuratie begint.

* U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verder gaat. Het ExpressRoute-circuit moet in een ingerichte en ingeschakelde staat zijn.

* U moet een actieve Microsoft-peering hebben. Instructies volgen bij [Peering-configuratie maken en wijzigen](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement

Als u uw configuratie wilt starten, meldt u zich aan bij uw Azure-account. Als u de Try It gebruikt, bent u automatisch aangemeld en u de inlogstap overslaan. Gebruik de volgende voorbeelden om verbinding te maken:

```azurecli
az login
```

Controleer de abonnementen voor het account.

```azurecli-interactive
az account list
```

Selecteer het abonnement waarvoor u een ExpressRoute-circuit wilt maken.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Stap 1: Een lijst met voorvoegsels en BGP-communitywaarden

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Ontvang een lijst met BGP-communitywaarden

Gebruik de volgende cmdlet om de lijst met BGP-communitywaarden te krijgen die zijn gekoppeld aan services die toegankelijk zijn via Microsoft-peering en de lijst met voorvoegsels die eraan zijn gekoppeld:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Maak een lijst met de waarden die u wilt gebruiken

Maak een lijst met BGP-communitywaarden die u wilt gebruiken in het routefilter.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Stap 2: Een routefilter en een filterregel maken

Een routefilter kan slechts één regel hebben en de regel moet van het type 'Toestaan' zijn. Met deze regel kan een lijst met BGP-communitywaarden zijn gekoppeld.

### <a name="1-create-a-route-filter"></a>1. Een routefilter maken

Maak eerst het routefilter. De `az network route-filter create` opdracht maakt alleen een routefilterbron. Nadat u de resource hebt gemaakt, moet u een regel maken en deze koppelen aan het routefilterobject. Voer de volgende opdracht uit om een routefilterbron te maken:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Een filterregel maken

Voer de volgende opdracht uit om een nieuwe regel te maken:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Stap 3: Bevestig het routefilter aan een ExpressRoute-circuit

Voer de volgende opdracht uit om het routefilter aan het ExpressRoute-circuit te koppelen:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Algemene taken

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>De eigenschappen van een routefilter opte krijgen

Als u de eigenschappen van een routefilter wilt opdoen, gebruikt u de volgende opdracht:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>De eigenschappen van een routefilter bijwerken

Als het routefilter al aan een circuit is gekoppeld, worden de juiste voorvoegselwijzigingen automatisch door updates van de BGP-communitylijst gepropageerd via de vastgestelde BGP-sessies. U de BGP-communitylijst van uw routefilter bijwerken met de volgende opdracht:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Een routefilter loskoppelen van een ExpressRoute-circuit

Zodra een routefilter is losgekoppeld van het ExpressRoute-circuit, worden er geen voorvoegsels geadverteerd via de BGP-sessie. U een routefilter loskoppelen van een ExpressRoute-circuit met de volgende opdracht:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Een routefilter verwijderen

U een routefilter alleen verwijderen als het niet aan een circuit is gekoppeld. Zorg ervoor dat het routefilter niet aan een circuit is gekoppeld voordat u probeert het te verwijderen. U een routefilter verwijderen met de volgende opdracht:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Zie de Veelgestelde vragen over [ExpressRoute voor](expressroute-faqs.md)meer informatie over ExpressRoute.
