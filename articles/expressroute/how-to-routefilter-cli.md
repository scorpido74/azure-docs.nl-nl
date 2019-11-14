---
title: 'ExpressRoute: route filters-micro soft-peering: Azure CLI'
description: In dit artikel wordt beschreven hoe u routefilters voor Microsoft-Peering met behulp van Azure CLI configureren
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman
ms.openlocfilehash: c3c50a005e119890fb17fcf7b3114a747bbe34bf
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033420"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Routefilters voor Microsoft-peering configureren: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Met routefilters kunt u een subset van ondersteunde services gebruiken via Microsoft-peering. De stappen in dit artikel kunt u configureren en beheren van routefilters voor ExpressRoute-circuits.

Office 365-Services, zoals Exchange Online, share point online en Skype voor bedrijven, zijn toegankelijk via micro soft-peering. Wanneer Microsoft-peering is geconfigureerd in een ExpressRoute-circuit, worden alle voorvoegsels die betrekking hebben op deze services worden geadverteerd via de BGP-sessies worden tot stand gebracht. Er wordt aan elk voorvoegsel een BGP-communitywaarde gekoppeld om de service te identificeren die via het voorvoegsel wordt aangeboden. Zie voor een lijst van de BGP-Communitywaarden en deze worden toegewezen aan services, [BGP-community's](expressroute-routing.md#bgp).

Als u de verbinding met alle services vereist, wordt een groot aantal voorvoegsels worden geadverteerd via BGP. Dit verhoogt aanzienlijk de grootte van de routetabellen onderhouden door routers in uw netwerk. Als u van plan alleen een subset van services die worden aangeboden via Microsoft bent-peering, kunt u de grootte van uw routetabellen op twee manieren verkleinen. U kunt:

* Ongewenste voorvoegsels filteren door routefilters zijn toegepast op de BGP-community's. Dit is een standaardprocedure voor netwerken en wordt vaak gebruikt in veel netwerken.

* Routefilters definiëren en deze toepassen op uw ExpressRoute-circuit. Een routefilter is een nieuwe resource waarmee u de lijst met services die u van plan bent om te gebruiken via Microsoft-peering te selecteren. ExpressRoute-routers verzenden alleen de lijst met voorvoegsels die deel uitmaken van de services die zijn geïdentificeerd in de routefilter.

### <a name="about"></a>Over routefilters

Wanneer Microsoft-peering is geconfigureerd op uw ExpressRoute-circuit, stel de Microsoft-randrouters een combinatie van een BGP-sessies met de randrouters (kunt kiezen of uw connectiviteitsprovider). Er worden geen routes geadverteerd naar uw netwerk. Als u dit wel wilt doen, moet u een routefilter koppelen.

Via een routefilter kunt u services identificeren die u wilt gebruiken via Microsoft-peering op uw ExpressRoute-circuit. Het is in feite een lijst met alle toegestane BGP-communitywaarden. Zodra er een routefilter is gedefinieerd en aan een ExpressRoute-circuit is gekoppeld, worden alle voorvoegsels die overeenkomen met de BGP-communitywaarden naar uw netwerk geadverteerd.

Als u routefilters met Office 365-services op deze koppelen, moet u gemachtigd zijn om te gebruiken Office 365-services via ExpressRoute. Als u bent niet gemachtigd om te gebruiken Office 365-services via ExpressRoute, mislukt de bewerking routefilters koppelen. Zie voor meer informatie over het autorisatieproces [Azure ExpressRoute voor Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Microsoft-peering van ExpressRoute-circuits die zijn geconfigureerd vóór 1 augustus 2017 hebben alle service-voorvoegsels geadverteerd via Microsoft-peering, zelfs als routefilters zijn niet gedefinieerd. Microsoft-peering van ExpressRoute-circuits die zijn geconfigureerd op of na 1 augustus 2017 hebben geen voorvoegsels geadverteerd totdat een routefilter is gekoppeld aan het circuit.
> 
> 

### <a name="workflow"></a>Werkstroom

Als u verbinding kan maken met services via Microsoft-peering, moet u de volgende configuratiestappen voltooien:

* U moet een actief ExpressRoute-circuit met Microsoft-peering ingerichte hebben. U kunt de volgende instructies gebruiken voor het uitvoeren van deze taken:
  * [Maken van een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit ingeschakeld door de connectiviteitsprovider voordat u doorgaat. Het ExpressRoute-circuit moet zich in een status ingericht en zijn ingeschakeld.
  * [Microsoft-peering maken](howto-routing-cli.md) als u de BGP-sessie rechtstreeks beheert. Of vraag uw connectiviteitsprovider Microsoft-peering voor uw circuit inrichten.

* U moet maken en configureren van een routefilter.
  * Identificeer de services die u verbruikt via Microsoft-peering
  * De lijst met BGP-Communitywaarden die zijn gekoppeld aan de services identificeren
  * Een regel voor het toestaan van de lijst met voorvoegsels die overeenkomt met de BGP-Communitywaarden maken

* U moet de routefilter koppelen aan het ExpressRoute-circuit.

## <a name="before-you-begin"></a>Voordat u begint

Installeer eerst de meest recente versie van de CLI-opdrachten (2.0 of hoger). Zie [Azure CLI installeren](/cli/azure/install-azure-cli) en [Aan de slag met Azure CLI](/cli/azure/get-started-with-azure-cli) voor meer informatie over de CLI-opdrachten.

* Controleer de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.

* U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verder gaat. Het ExpressRoute-circuit moet zich in een status ingericht en zijn ingeschakeld.

* U moet een actieve Microsoft-peering. Volg de instructies op [maken en de peering-configuratie wijzigen](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement

Als u wilt de configuratie begint, moet u zich aanmelden bij uw Azure-account. Als u het 'Try It' gebruikt, kunt u automatisch bent aangemeld en de aanmeldings-stap kunt overslaan. Gebruik de volgende voorbeelden kunt u verbinding maken:

```azurecli
az login
```

Controleer de abonnementen voor het account.

```azurecli-interactive
az account list
```

Selecteer het abonnement waarvoor u wenst te maken van een ExpressRoute-circuit.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="prefixes"></a>Stap 1: Een lijst met voorvoegsels en BGP-Communitywaarden ophalen

### <a name="1-get-a-list-of-bgp-community-values"></a>1. een lijst met BGP Community-waarden ophalen

Gebruik de volgende cmdlet om de lijst met BGP-Communitywaarden die zijn gekoppeld aan services die toegankelijk zijn via Microsoft-peering en de lijst met voorvoegsels die zijn gekoppeld aan deze te verkrijgen:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Maak een lijst met de waarden die u wilt gebruiken

Maak een lijst van BGP-Communitywaarden die u wilt gebruiken in de routefilter.

## <a name="filter"></a>Stap 2: Maak een routefilter en een filterregel

Een routefilter kan slechts één regel, en de regel moet van het type 'Toestaan'. Deze regel kan een lijst met BGP-Communitywaarden die zijn gekoppeld aan deze hebben.

### <a name="1-create-a-route-filter"></a>1. een route filter maken

Maak eerst de routefilter. Met de opdracht `az network route-filter create` wordt alleen een resource voor een route filter gemaakt. Nadat u de resource gemaakt, moet u vervolgens een regel maken en koppelen aan het object route-filter. Voer de volgende opdracht om een route-filter-resource te maken:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. een filter regel maken

Voer de volgende opdracht om een nieuwe regel te maken:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>Stap 3: De routefilter koppelen aan een ExpressRoute-circuit

Voer de volgende opdracht uit om de routefilter koppelen aan het ExpressRoute-circuit:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>Algemene taken

### <a name="getproperties"></a>Ophalen van de eigenschappen van een routefilter

Als u de eigenschappen van een routefilter, gebruikt u de volgende opdracht uit:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>De eigenschappen van een routefilter bijwerken

Als de routefilter is al gekoppeld aan een circuit, updates aan de lijst met BGP-community automatisch wijzigingen worden doorgegeven voorvoegsel aankondiging via de tot stand gebrachte BGP-sessies. U kunt de lijst van de BGP-community van uw routefilter met de volgende opdracht bijwerken:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>Een routefilter van een ExpressRoute-circuit ontkoppelen

Zodra een routefilter is losgekoppeld van het ExpressRoute-circuit, worden er geen voorvoegsels worden geadverteerd via de BGP-sessie. U kunt een routefilter van een ExpressRoute-circuit met de volgende opdracht loskoppelen:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>Een routefilter verwijderen

U kunt een routefilter alleen verwijderen als deze niet is gekoppeld aan een circuit. Zorg ervoor dat de routefilter niet is gekoppeld aan een circuit voordat u probeert te verwijderen. U kunt een routefilter met de volgende opdracht verwijderen:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over ExpressRoute raadpleegt u de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md).
