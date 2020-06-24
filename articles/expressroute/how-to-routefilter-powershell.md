---
title: 'ExpressRoute: route filters-micro soft-peering: Azure PowerShell'
description: In dit artikel wordt beschreven hoe u route filters configureert voor micro soft-peering met behulp van Power shell
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: f5a294a051350c4b08b34356abcd883b7580164e
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84729299"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Route filters voor micro soft-peering configureren: Power shell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure-CLI](how-to-routefilter-cli.md)
> 

Met routefilters kunt u een subset van ondersteunde services gebruiken via Microsoft-peering. Met de stappen in dit artikel kunt u route filters configureren en beheren voor ExpressRoute-circuits.

Office 365-services zoals Exchange Online, share point online, Skype voor bedrijven en open bare Azure-Services, zoals opslag en SQL data base, zijn toegankelijk via micro soft-peering. Open bare Azure-Services kunnen per regio worden geselecteerd en kunnen niet per open bare service worden gedefinieerd.

Wanneer micro soft-peering is geconfigureerd voor een ExpressRoute-circuit en er een route filter is gekoppeld, worden alle voor voegsels die voor deze services zijn geselecteerd, geadverteerd via de BGP-sessies die zijn ingesteld. Er wordt aan elk voorvoegsel een BGP-communitywaarde gekoppeld om de service te identificeren die via het voorvoegsel wordt aangeboden. Zie [BGP-community's](expressroute-routing.md#bgp)voor een lijst met de waarden van de BGP-Community en de services waaraan ze zijn toegewezen.

Als u verbinding met alle services nodig hebt, worden er een groot aantal voor voegsels via BGP geadverteerd. Dit verg root de grootte van de route tabellen die worden beheerd door routers in uw netwerk. Als u van plan bent slechts een subset van services te gebruiken die worden aangeboden via micro soft-peering, kunt u de grootte van uw route tabellen op twee manieren verkleinen. U kunt:

- Ongewenste voor voegsels uitfilteren door route filters op BGP-community's toe te passen. Dit is een standaard netwerk praktijk en wordt meestal gebruikt in veel netwerken.

- Definieer route filters en pas deze toe op uw ExpressRoute-circuit. Een route filter is een nieuwe resource waarmee u de lijst met Services kunt selecteren die u wilt gebruiken via micro soft-peering. ExpressRoute-routers verzenden alleen de lijst met voor voegsels die horen bij de services die zijn geïdentificeerd in het route filter.

### <a name="about-route-filters"></a><a name="about"></a>Over route filters

Wanneer micro soft-peering is geconfigureerd op uw ExpressRoute-circuit, maken de micro soft-netwerk Edge-routers een paar BGP-sessies met de Edge-routers (of uw connectiviteits provider). Er worden geen routes geadverteerd naar uw netwerk. Als u dit wel wilt doen, moet u een routefilter koppelen.

Via een routefilter kunt u services identificeren die u wilt gebruiken via Microsoft-peering op uw ExpressRoute-circuit. Het is in feite een lijst met toegestane waarden voor de BGP-community. Zodra er een routefilter is gedefinieerd en aan een ExpressRoute-circuit is gekoppeld, worden alle voorvoegsels die overeenkomen met de BGP-communitywaarden naar uw netwerk geadverteerd.

Als u route filters met Office 365-Services wilt koppelen, moet u gemachtigd zijn om Office 365-services te gebruiken via ExpressRoute. Als u niet gemachtigd bent om Office 365-services te gebruiken via ExpressRoute, mislukt de bewerking voor het koppelen van route filters. Zie voor meer informatie over het autorisatie proces [Azure ExpressRoute voor Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Micro soft-peering van ExpressRoute-circuits die zijn geconfigureerd vóór 1 augustus 2017, heeft alle service voorvoegsels die worden geadverteerd via micro soft-peering, zelfs als er geen route filters zijn gedefinieerd. Micro soft-peering van ExpressRoute-circuits die zijn geconfigureerd op of na 1 augustus 2017, heeft geen voor voegsels die worden geadverteerd totdat een route filter aan het circuit is gekoppeld.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Werkstroom

Om verbinding te kunnen maken met Services via micro soft-peering, moet u de volgende configuratie stappen uitvoeren:

- U moet een actief ExpressRoute-circuit hebben dat micro soft-peering heeft ingericht. U kunt de volgende instructies gebruiken om deze taken uit te voeren:
  - [Maak een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit ingeschakeld door uw connectiviteits provider voordat u verdergaat. Het ExpressRoute-circuit moet de status ingericht en ingeschakeld hebben.
  - [Maak micro soft-peering](expressroute-circuit-peerings.md) als u de BGP-sessie rechtstreeks beheert. Of zorg ervoor dat uw connectiviteits provider micro soft-peering voor uw circuit inricht.

-  U moet een route filter maken en configureren.
    - Identificeer de services die u wilt gebruiken via micro soft-peering
    - De lijst met BGP Community-waarden identificeren die zijn gekoppeld aan de services
    - Een regel maken om de voorvoegsel lijst toe te staan die overeenkomt met de waarden van de BGP-Community

-  U moet het route filter koppelen aan het ExpressRoute-circuit.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u begint met de configuratie, moet u voldoen aan de volgende criteria:

 - Controleer de [vereisten](expressroute-prerequisites.md) en [werk stromen](expressroute-workflows.md) voordat u begint met de configuratie.

 - U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verder gaat. Het ExpressRoute-circuit moet de status ingericht en ingeschakeld hebben.

 - U moet een actieve micro soft-peering hebben. Volg de instructies in het artikel [peering-configuratie maken en wijzigen](expressroute-circuit-peerings.md) .


### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Meld u aan bij uw Azure-account

Voordat u begint met deze configuratie, moet u zich aanmelden bij uw Azure-account. De cmdlet vraagt u om de aanmeldingsreferenties voor uw Azure-account. Na het aanmelden, worden de instellingen van uw account gedownload zodat ze beschikbaar zijn voor Azure PowerShell.

Open de PowerShell-console met verhoogde rechten en maak verbinding met uw account. Gebruik het volgende voor beeld om u te helpen bij het maken van verbinding. Als u Azure Cloud Shell gebruikt, hoeft u deze cmdlet niet uit te voeren, omdat u automatisch wordt aangemeld.

```azurepowershell
Connect-AzAccount
```

Als u meerdere Azure-abonnementen hebt, controleert u de abonnementen voor het account.

```azurepowershell-interactive
Get-AzSubscription
```

Geef het abonnement op dat u wilt gebruiken.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Stap 1: een lijst met voor voegsels en waarden voor de BGP-Community ophalen

### <a name="1-get-a-list-of-bgp-community-values"></a>1. een lijst met BGP Community-waarden ophalen

Gebruik de volgende cmdlet om de lijst met BGP Community-waarden te verkrijgen die zijn gekoppeld aan services die toegankelijk zijn via micro soft-peering en de lijst met voor voegsels die eraan zijn gekoppeld:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Maak een lijst met de waarden die u wilt gebruiken

Maak een lijst met de BGP-Community-waarden die u wilt gebruiken in het route filter.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Stap 2: een route filter en een filter regel maken

Een route filter kan slechts één regel bevatten en de regel moet van het type toestaan zijn. Aan deze regel kan een lijst met BGP Community-waarden zijn gekoppeld.

### <a name="1-create-a-route-filter"></a>1. een route filter maken

Maak eerst het route filter. De opdracht ' New-AzRouteFilter ' maakt alleen een resource voor een route filter. Nadat u de resource hebt gemaakt, moet u vervolgens een regel maken en deze koppelen aan het object route filter. Voer de volgende opdracht uit om een route filter bron te maken:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. een filter regel maken

U kunt een set BGP-community's opgeven als een lijst met door komma's gescheiden waarden, zoals wordt weer gegeven in het voor beeld. Voer de volgende opdracht uit om een nieuwe regel te maken:
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. Voeg de regel toe aan het route filter

Voer de volgende opdracht uit om de filter regel toe te voegen aan het route filter:
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Stap 3: het route filter koppelen aan een ExpressRoute-circuit

Voer de volgende opdracht uit om het route filter te koppelen aan het ExpressRoute-circuit, ervan uitgaande dat u alleen micro soft-peering hebt:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>Algemene taken

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>De eigenschappen van een route filter ophalen

Als u de eigenschappen van een route filter wilt ophalen, gebruikt u de volgende stappen:

1. Voer de volgende opdracht uit om de route filter Resource op te halen:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. De route filter regels ophalen voor de resource route filter door de volgende opdracht uit te voeren:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>De eigenschappen van een route filter bijwerken

Als het route filter al is gekoppeld aan een circuit, worden in de lijst met BGP-community's automatisch de juiste wijzigingen in de aankondiging van de voor voegsels door gegeven aan de ingestelde BGP-sessies. U kunt de lijst met BGP-community's van uw route filter bijwerken met behulp van de volgende opdracht:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Een route filter loskoppelen van een ExpressRoute-circuit

Zodra een route filter is losgekoppeld van het ExpressRoute-circuit, worden er geen voor voegsels geadverteerd via de BGP-sessie. Met de volgende opdracht kunt u een route filter loskoppelen van een ExpressRoute-circuit:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Een route filter verwijderen

U kunt een route filter alleen verwijderen als deze niet aan een circuit is gekoppeld. Zorg ervoor dat het route filter niet is gekoppeld aan een circuit voordat u het probeert te verwijderen. U kunt een route filter verwijderen met de volgende opdracht:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md)voor meer informatie over ExpressRoute.
