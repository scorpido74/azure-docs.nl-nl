---
title: 'ExpressRoute: Routefilters- Microsoft-peering:Azure PowerShell'
description: In dit artikel wordt beschreven hoe u routefilters voor Microsoft Peering configureert met PowerShell
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: cade33e77eb0d3ddd818a6ce3dbd7c6cf72811d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037407"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Routefilters configureren voor Microsoft-peering: PowerShell
> [!div class="op_single_selector"]
> * [Azure-portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure-CLI](how-to-routefilter-cli.md)
> 

Met routefilters kunt u een subset van ondersteunde services gebruiken via Microsoft-peering. Met de stappen in dit artikel u routefilters voor ExpressRoute-circuits configureren en beheren.

Office 365-services zoals Exchange Online, SharePoint Online en Skype voor Bedrijven en openbare Azure-services, zoals opslag en SQL DB, zijn toegankelijk via Microsoft-peering. Azure-openbare services zijn per regio te selecteren en kunnen niet per openbare service worden gedefinieerd.

Wanneer Microsoft-peering is geconfigureerd op een ExpressRoute-circuit en er een routefilter is gekoppeld, worden alle voorvoegsels die voor deze services zijn geselecteerd, geadverteerd via de BGP-sessies die zijn ingesteld. Er wordt aan elk voorvoegsel een BGP-communitywaarde gekoppeld om de service te identificeren die via het voorvoegsel wordt aangeboden. Zie [BGP-community's](expressroute-routing.md#bgp)voor een lijst met de waarden van de BGP-community en de services die ze in kaart brengen.

Als u connectiviteit met alle services nodig hebt, wordt een groot aantal voorvoegsels geadverteerd via BGP. Dit verhoogt aanzienlijk de grootte van de routetabellen die worden onderhouden door routers binnen uw netwerk. Als u van plan bent om slechts een subset van services te gebruiken die via Microsoft-peering worden aangeboden, u de grootte van uw routetabellen op twee manieren verkleinen. U kunt:

- Filter ongewenste voorvoegsels door routefilters toe te passen op BGP-community's. Dit is een standaard netwerkpraktijk en wordt vaak gebruikt binnen veel netwerken.

- Definieer routefilters en pas ze toe op uw ExpressRoute-circuit. Een routefilter is een nieuwe bron waarmee u de lijst met services selecteren die u wilt gebruiken via Microsoft-peering. ExpressRoute-routers verzenden alleen de lijst met voorvoegsels die behoren tot de services die in het routefilter zijn geïdentificeerd.

### <a name="about-route-filters"></a><a name="about"></a>Routefilters

Wanneer Microsoft-peering is geconfigureerd op uw ExpressRoute-circuit, stellen de Microsoft-netwerkedgerouters een paar BGP-sessies op met de edge-routers (die van u of uw connectiviteitsprovider). Er worden geen routes geadverteerd naar uw netwerk. Als u dit wel wilt doen, moet u een routefilter koppelen.

Via een routefilter kunt u services identificeren die u wilt gebruiken via Microsoft-peering op uw ExpressRoute-circuit. Het is in wezen een lijst van alle BGP-communitywaarden. Zodra er een routefilter is gedefinieerd en aan een ExpressRoute-circuit is gekoppeld, worden alle voorvoegsels die overeenkomen met de BGP-communitywaarden naar uw netwerk geadverteerd.

Als u routefilters met Office 365-services erop wilt koppelen, moet u toestemming hebben om Office 365-services te gebruiken via ExpressRoute. Als u niet gemachtigd bent om Office 365-services via ExpressRoute te gebruiken, mislukt de bewerking voor het koppelen van routefilters. Zie [Azure ExpressRoute voor Office 365 voor](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)meer informatie over het autorisatieproces.

> [!IMPORTANT]
> Microsoft-peering van ExpressRoute-circuits die vóór 1 augustus 2017 zijn geconfigureerd, hebben alle servicevoorvoegsels die worden geadverteerd via Microsoft-peering, zelfs als routefilters niet zijn gedefinieerd. Microsoft-peering van ExpressRoute-circuits die op of na 1 augustus 2017 zijn geconfigureerd, worden pas geadverteerd als er een routefilter aan het circuit is gekoppeld.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Werkstroom

Als u verbinding wilt maken met services via Microsoft-peering, moet u de volgende configuratiestappen uitvoeren:

- U moet beschikken over een actief ExpressRoute-circuit waarop Microsoft peering is ingericht. U de volgende instructies gebruiken om deze taken uit te voeren:
  - [Maak een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit inschakelen door uw connectiviteitsprovider voordat u verdergaat. Het ExpressRoute-circuit moet in een ingerichte en ingeschakelde staat zijn.
  - [Microsoft-peering maken](expressroute-circuit-peerings.md) als u de BGP-sessie rechtstreeks beheert. Of laat uw connectiviteitsprovider Microsoft-peering voor uw circuit inrichten.

-  U moet een routefilter maken en configureren.
    - Identificeren van de services waarmee u gebruiken om te gebruiken via Microsoft-peering
    - De lijst met BGP-communitywaarden identificeren die aan de services zijn gekoppeld
    - Een regel maken waarmee de voorvoegsellijst overeenkomt met de waarden van de BGP-community

-  U moet het routefilter aan het ExpressRoute-circuit bevestigen.

## <a name="before-you-begin"></a>Voordat u begint

Controleer voordat u met de configuratie begint of u aan de volgende criteria voldoet:

 - Bekijk de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u met de configuratie begint.

 - U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verder gaat. Het ExpressRoute-circuit moet in een ingerichte en ingeschakelde staat zijn.

 - U moet een actieve Microsoft-peering hebben. Volg de instructies in het artikel [Peering configuration maken en wijzigen.](expressroute-circuit-peerings.md)


### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Meld u aan bij uw Azure-account

Voordat u begint met deze configuratie, moet u zich aanmelden bij uw Azure-account. De cmdlet vraagt u om de aanmeldingsreferenties voor uw Azure-account. Na het aanmelden, worden de instellingen van uw account gedownload zodat ze beschikbaar zijn voor Azure PowerShell.

Open de PowerShell-console met verhoogde rechten en maak verbinding met uw account. Gebruik het volgende voorbeeld om verbinding te maken. Als u Azure Cloud Shell gebruikt, hoeft u deze cmdlet niet uit te voeren, omdat u automatisch wordt aangemeld.

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

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Stap 1: Een lijst met voorvoegsels en BGP-communitywaarden

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Ontvang een lijst met BGP-communitywaarden

Gebruik de volgende cmdlet om de lijst met BGP-communitywaarden te krijgen die zijn gekoppeld aan services die toegankelijk zijn via Microsoft-peering en de lijst met voorvoegsels die eraan zijn gekoppeld:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Maak een lijst met de waarden die u wilt gebruiken

Maak een lijst met BGP-communitywaarden die u wilt gebruiken in het routefilter.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Stap 2: Een routefilter en een filterregel maken

Een routefilter kan slechts één regel hebben en de regel moet van het type 'Toestaan' zijn. Met deze regel kan een lijst met BGP-communitywaarden zijn gekoppeld.

### <a name="1-create-a-route-filter"></a>1. Een routefilter maken

Maak eerst het routefilter. Met de opdracht 'Nieuw-AzRouteFilter' wordt alleen een routefilterbron gemaakt. Nadat u de resource hebt gemaakt, moet u een regel maken en deze koppelen aan het routefilterobject. Voer de volgende opdracht uit om een routefilterbron te maken:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Een filterregel maken

U een set BGP-community's opgeven als een door komma's gescheiden lijst, zoals in het voorbeeld wordt weergegeven. Voer de volgende opdracht uit om een nieuwe regel te maken:
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. De regel toevoegen aan het routefilter

Voer de volgende opdracht uit om de filterregel toe te voegen aan het routefilter:
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Stap 3: Bevestig het routefilter aan een ExpressRoute-circuit

Voer de volgende opdracht uit om het routefilter aan het ExpressRoute-circuit toe te voegen, ervan uitgaande dat u alleen Microsoft-peering hebt:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>Algemene taken

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>De eigenschappen van een routefilter opte krijgen

Als u de eigenschappen van een routefilter wilt opdoen, voert u de volgende stappen uit:

1. Voer de volgende opdracht uit om de routefilterbron op te halen:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. Download de regels voor routefilter voor de routefilterbron door de volgende opdracht uit te voeren:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>De eigenschappen van een routefilter bijwerken

Als het routefilter al aan een circuit is gekoppeld, worden de juiste voorvoegselwijzigingen automatisch door updates van de BGP-communitylijst gepropageerd via de vastgestelde BGP-sessies. U de BGP-communitylijst van uw routefilter bijwerken met de volgende opdracht:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Een routefilter loskoppelen van een ExpressRoute-circuit

Zodra een routefilter is losgekoppeld van het ExpressRoute-circuit, worden er geen voorvoegsels geadverteerd via de BGP-sessie. U een routefilter loskoppelen van een ExpressRoute-circuit met de volgende opdracht:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Een routefilter verwijderen

U een routefilter alleen verwijderen als het niet aan een circuit is gekoppeld. Zorg ervoor dat het routefilter niet aan een circuit is gekoppeld voordat u probeert het te verwijderen. U een routefilter verwijderen met de volgende opdracht:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

Zie de Veelgestelde vragen over [ExpressRoute voor](expressroute-faqs.md)meer informatie over ExpressRoute.
