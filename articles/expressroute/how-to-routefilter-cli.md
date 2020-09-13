---
title: 'ExpressRoute: route filters-micro soft-peering: Azure CLI'
description: In dit artikel wordt beschreven hoe u route filters configureert voor micro soft-peering met behulp van Azure CLI
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/07/2018
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8fbce15b84371b7b7907deff361e2a2e706bec28
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89567704"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Route filters voor micro soft-peering configureren: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure-CLI](how-to-routefilter-cli.md)
> 

Met routefilters kunt u een subset van ondersteunde services gebruiken via Microsoft-peering. Met de stappen in dit artikel kunt u route filters configureren en beheren voor ExpressRoute-circuits.

Microsoft 365 Services, zoals Exchange Online, share point online en Skype voor bedrijven, zijn toegankelijk via micro soft-peering. Wanneer micro soft-peering is geconfigureerd in een ExpressRoute-circuit, worden alle voor voegsels die betrekking hebben op deze services, geadverteerd via de BGP-sessies die zijn ingesteld. Er wordt aan elk voorvoegsel een BGP-communitywaarde gekoppeld om de service te identificeren die via het voorvoegsel wordt aangeboden. Zie [BGP-community's](expressroute-routing.md#bgp)voor een lijst met de waarden van de BGP-Community en de services waaraan ze zijn toegewezen.

Als u verbinding met alle services nodig hebt, worden er een groot aantal voor voegsels via BGP geadverteerd. Dit verg root de grootte van de route tabellen die worden beheerd door routers in uw netwerk. Als u van plan bent slechts een subset van services te gebruiken die worden aangeboden via micro soft-peering, kunt u de grootte van uw route tabellen op twee manieren verkleinen. U kunt het volgende doen:

* Ongewenste voor voegsels uitfilteren door route filters op BGP-community's toe te passen. Dit is een standaard netwerk praktijk en wordt meestal gebruikt in veel netwerken.

* Definieer route filters en pas deze toe op uw ExpressRoute-circuit. Een route filter is een nieuwe resource waarmee u de lijst met Services kunt selecteren die u wilt gebruiken via micro soft-peering. ExpressRoute-routers verzenden alleen de lijst met voor voegsels die horen bij de services die zijn geïdentificeerd in het route filter.

### <a name="about-route-filters"></a><a name="about"></a>Over route filters

Wanneer micro soft-peering is geconfigureerd op uw ExpressRoute-circuit, worden door de micro soft Edge-routers een paar BGP-sessies tot stand gebracht met de Edge-routers (of uw connectiviteits provider). Er worden geen routes geadverteerd naar uw netwerk. Als u dit wel wilt doen, moet u een routefilter koppelen.

Via een routefilter kunt u services identificeren die u wilt gebruiken via Microsoft-peering op uw ExpressRoute-circuit. Het is in feite een lijst met toegestane waarden van alle BGP-communitywaarde. Zodra er een routefilter is gedefinieerd en aan een ExpressRoute-circuit is gekoppeld, worden alle voorvoegsels die overeenkomen met de BGP-communitywaarden naar uw netwerk geadverteerd.

Als u route filters wilt koppelen met Microsoft 365-Services, moet u autorisatie hebben om Microsoft 365-services te gebruiken via ExpressRoute. Als u niet gemachtigd bent om Microsoft 365-services te gebruiken via ExpressRoute, mislukt de bewerking voor het koppelen van route filters. Zie [Azure ExpressRoute voor Microsoft 365 voor](/microsoft-365/enterprise/azure-expressroute)meer informatie over het autorisatie proces.

> [!IMPORTANT]
> Micro soft-peering van ExpressRoute-circuits die zijn geconfigureerd vóór 1 augustus 2017, heeft alle service voorvoegsels die worden geadverteerd via micro soft-peering, zelfs als er geen route filters zijn gedefinieerd. Micro soft-peering van ExpressRoute-circuits die zijn geconfigureerd op of na 1 augustus 2017, heeft geen voor voegsels die worden geadverteerd totdat een route filter aan het circuit is gekoppeld.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Werkstroom

Om verbinding te kunnen maken met Services via micro soft-peering, moet u de volgende configuratie stappen uitvoeren:

* U moet een actief ExpressRoute-circuit hebben dat micro soft-peering heeft ingericht. U kunt de volgende instructies gebruiken om deze taken uit te voeren:
  * [Maak een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit ingeschakeld door uw connectiviteits provider voordat u verdergaat. Het ExpressRoute-circuit moet de status ingericht en ingeschakeld hebben.
  * [Maak micro soft-peering](howto-routing-cli.md) als u de BGP-sessie rechtstreeks beheert. Of zorg ervoor dat uw connectiviteits provider micro soft-peering voor uw circuit inricht.

* U moet een route filter maken en configureren.
  * Identificeer de services die u wilt gebruiken via micro soft-peering
  * De lijst met BGP Community-waarden identificeren die zijn gekoppeld aan de services
  * Een regel maken om de voorvoegsel lijst toe te staan die overeenkomt met de waarden van de BGP-Community

* U moet het route filter koppelen aan het ExpressRoute-circuit.

## <a name="before-you-begin"></a>Voordat u begint

Installeer eerst de meest recente versie van de CLI-opdrachten (2.0 of hoger). Zie [Azure CLI installeren](/cli/azure/install-azure-cli) en [Aan de slag met Azure CLI](/cli/azure/get-started-with-azure-cli) voor meer informatie over de CLI-opdrachten.

* Bekijk de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.

* U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verder gaat. Het ExpressRoute-circuit moet de status ingericht en ingeschakeld hebben.

* U moet een actieve micro soft-peering hebben. Volg de instructies bij het [maken en wijzigen van de peering-configuratie](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement

Meld u aan bij uw Azure-account om de configuratie te starten. Als u de ' Try ' gebruikt, bent u automatisch aangemeld en kunt u de aanmeldings stap overs Laan. Gebruik de volgende voor beelden om u te helpen bij het maken van verbinding:

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

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Stap 1: een lijst met voor voegsels en waarden voor de BGP-Community ophalen

### <a name="1-get-a-list-of-bgp-community-values"></a>1. een lijst met BGP Community-waarden ophalen

Gebruik de volgende cmdlet om de lijst met BGP Community-waarden te verkrijgen die zijn gekoppeld aan services die toegankelijk zijn via micro soft-peering en de lijst met voor voegsels die eraan zijn gekoppeld:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Maak een lijst met de waarden die u wilt gebruiken

Maak een lijst met de BGP-Community-waarden die u wilt gebruiken in het route filter.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Stap 2: een route filter en een filter regel maken

Een route filter kan slechts één regel bevatten en de regel moet van het type toestaan zijn. Aan deze regel kan een lijst met BGP Community-waarden zijn gekoppeld.

### <a name="1-create-a-route-filter"></a>1. een route filter maken

Maak eerst het route filter. De opdracht `az network route-filter create` maakt alleen een resource voor een route filter. Nadat u de resource hebt gemaakt, moet u vervolgens een regel maken en deze koppelen aan het object route filter. Voer de volgende opdracht uit om een route filter bron te maken:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. een filter regel maken

Voer de volgende opdracht uit om een nieuwe regel te maken:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Stap 3: het route filter koppelen aan een ExpressRoute-circuit

Voer de volgende opdracht uit om het route filter te koppelen aan het ExpressRoute-circuit:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Algemene taken

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>De eigenschappen van een route filter ophalen

Als u de eigenschappen van een route filter wilt ophalen, gebruikt u de volgende opdracht:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>De eigenschappen van een route filter bijwerken

Als het route filter al is gekoppeld aan een circuit, worden in de lijst met BGP-community's automatisch de juiste wijzigingen in de aankondiging van de voor voegsels door gegeven aan de ingestelde BGP-sessies. U kunt de lijst met BGP-community's van uw route filter bijwerken met behulp van de volgende opdracht:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Een route filter loskoppelen van een ExpressRoute-circuit

Zodra een route filter is losgekoppeld van het ExpressRoute-circuit, worden er geen voor voegsels geadverteerd via de BGP-sessie. Met de volgende opdracht kunt u een route filter loskoppelen van een ExpressRoute-circuit:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Een route filter verwijderen

U kunt een route filter alleen verwijderen als deze niet aan een circuit is gekoppeld. Zorg ervoor dat het route filter niet is gekoppeld aan een circuit voordat u het probeert te verwijderen. U kunt een route filter verwijderen met de volgende opdracht:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md)voor meer informatie over ExpressRoute.
