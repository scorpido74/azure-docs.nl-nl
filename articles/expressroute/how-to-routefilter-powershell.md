---
title: 'Route filters configureren voor micro soft-peering-ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: In dit artikel wordt beschreven hoe u routefilters voor Microsoft-Peering met behulp van PowerShell configureren
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: c5a5ca4949ca223e9123d59c9578a2628dacd351
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123410"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Route filters voor micro soft-peering configureren: PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure-CLI](how-to-routefilter-cli.md)
> 

Met routefilters kunt u een subset van ondersteunde services gebruiken via Microsoft-peering. De stappen in dit artikel kunt u configureren en beheren van routefilters voor ExpressRoute-circuits.

Office 365-services zoals Exchange Online, share point online, Skype voor bedrijven en open bare Azure-Services, zoals opslag en SQL data base, zijn toegankelijk via micro soft-peering. Openbare Azure-services kunnen worden geselecteerd op basis van de per regio en per openbare service kan niet worden gedefinieerd.

Wanneer Microsoft-peering is geconfigureerd op een ExpressRoute-circuit en een routefilter is gekoppeld, worden alle voorvoegsels die zijn geselecteerd voor deze services worden geadverteerd via de BGP-sessies worden tot stand gebracht. Er wordt aan elk voorvoegsel een BGP-communitywaarde gekoppeld om de service te identificeren die via het voorvoegsel wordt aangeboden. Zie voor een lijst van de BGP-Communitywaarden en deze worden toegewezen aan services, [BGP-community's](expressroute-routing.md#bgp).

Als u de verbinding met alle services vereist, wordt een groot aantal voorvoegsels worden geadverteerd via BGP. Dit verhoogt aanzienlijk de grootte van de routetabellen onderhouden door routers in uw netwerk. Als u van plan alleen een subset van services die worden aangeboden via Microsoft bent-peering, kunt u de grootte van uw routetabellen op twee manieren verkleinen. U kunt:

- Ongewenste voorvoegsels filteren door routefilters zijn toegepast op de BGP-community's. Dit is een standaardprocedure voor netwerken en wordt vaak gebruikt in veel netwerken.

- Routefilters definiëren en deze toepassen op uw ExpressRoute-circuit. Een routefilter is een nieuwe resource waarmee u de lijst met services die u van plan bent om te gebruiken via Microsoft-peering te selecteren. ExpressRoute-routers verzenden alleen de lijst met voorvoegsels die deel uitmaken van de services die zijn geïdentificeerd in de routefilter.

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

- U moet een actief ExpressRoute-circuit met Microsoft-peering ingerichte hebben. U kunt de volgende instructies gebruiken voor het uitvoeren van deze taken:
  - [Maken van een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit ingeschakeld door de connectiviteitsprovider voordat u doorgaat. Het ExpressRoute-circuit moet zich in een status ingericht en zijn ingeschakeld.
  - [Microsoft-peering maken](expressroute-circuit-peerings.md) als u de BGP-sessie rechtstreeks beheert. Of vraag uw connectiviteitsprovider Microsoft-peering voor uw circuit inrichten.

-  U moet maken en configureren van een routefilter.
    - Identificeer de services die u verbruikt via Microsoft-peering
    - De lijst met BGP-Communitywaarden die zijn gekoppeld aan de services identificeren
    - Een regel voor het toestaan van de lijst met voorvoegsels die overeenkomt met de BGP-Communitywaarden maken

-  U moet de routefilter koppelen aan het ExpressRoute-circuit.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u begint met de configuratie, moet u voldoen aan de volgende criteria:

 - Controleer de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.

 - U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verder gaat. Het ExpressRoute-circuit moet zich in een status ingericht en zijn ingeschakeld.

 - U moet een actieve Microsoft-peering. Volg de instructies in de [maken en wijzigen van een peeringconfiguratie](expressroute-circuit-peerings.md) artikel.


### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Meld u aan bij uw Azure-account

Voordat u begint met deze configuratie, moet u zich aanmelden bij uw Azure-account. De cmdlet vraagt u om de aanmeldingsreferenties voor uw Azure-account. Na het aanmelden, worden de instellingen van uw account gedownload zodat ze beschikbaar zijn voor Azure PowerShell.

Open de PowerShell-console met verhoogde rechten en maak verbinding met uw account. Met het volgende voorbeeld kunt u verbinding maken. Als u Azure Cloud Shell gebruikt, hoeft u deze cmdlet uitvoeren zoals u automatisch afgemeld.

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

## <a name="prefixes"></a>Stap 1: Een lijst met voor voegsels en BGP Community-waarden ophalen

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Een overzicht van BGP-Communitywaarden

Gebruik de volgende cmdlet om de lijst met BGP-Communitywaarden die zijn gekoppeld aan services die toegankelijk zijn via Microsoft-peering en de lijst met voorvoegsels die zijn gekoppeld aan deze te verkrijgen:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Maak een lijst van de waarden die u wilt gebruiken

Maak een lijst van BGP-Communitywaarden die u wilt gebruiken in de routefilter.

## <a name="filter"></a>Stap 2: Een route filter en een filter regel maken

Een routefilter kan slechts één regel, en de regel moet van het type 'Toestaan'. Deze regel kan een lijst met BGP-Communitywaarden die zijn gekoppeld aan deze hebben.

### <a name="1-create-a-route-filter"></a>1. Een routefilter maken

Maak eerst de routefilter. De opdracht ' New-AzRouteFilter ' maakt alleen een resource voor een route filter. Nadat u de resource gemaakt, moet u vervolgens een regel maken en koppelen aan het object route-filter. Voer de volgende opdracht om een route-filter-resource te maken:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Een filterregel maken

U kunt een set van BGP-community's opgeven als een lijst met door komma's gescheiden zoals wordt weergegeven in het voorbeeld. Voer de volgende opdracht om een nieuwe regel te maken:
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. De regel aan de routefilter toevoegen

Voer de volgende opdracht de filterregel toevoegen aan het routefilter:
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="attach"></a>Stap 3: Het route filter koppelen aan een ExpressRoute-circuit

Voer de volgende opdracht uit om de routefilter koppelen aan het ExpressRoute-circuit, ervan uitgaande dat u hebt alleen Microsoft-peering:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="tasks"></a>Algemene taken

### <a name="getproperties"></a>Ophalen van de eigenschappen van een routefilter

Als u de eigenschappen van een routefilter, gebruikt u de volgende stappen uit:

1. Voer de volgende opdracht om op te halen van de resource route-filter:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. De route filterregels voor de route-filter resource ophalen met de volgende opdracht:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="updateproperties"></a>De eigenschappen van een routefilter bijwerken

Als de routefilter is al gekoppeld aan een circuit, updates aan de lijst met BGP-community automatisch wijzigingen worden doorgegeven voorvoegsel aankondiging via de tot stand gebrachte BGP-sessies. U kunt de lijst van de BGP-community van uw routefilter met de volgende opdracht bijwerken:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="detach"></a>Een routefilter van een ExpressRoute-circuit ontkoppelen

Zodra een routefilter is losgekoppeld van het ExpressRoute-circuit, worden er geen voorvoegsels worden geadverteerd via de BGP-sessie. U kunt een routefilter van een ExpressRoute-circuit met de volgende opdracht loskoppelen:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="delete"></a>Een routefilter verwijderen

U kunt een routefilter alleen verwijderen als deze niet is gekoppeld aan een circuit. Zorg ervoor dat de routefilter niet is gekoppeld aan een circuit voordat u probeert te verwijderen. U kunt een routefilter met de volgende opdracht verwijderen:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over ExpressRoute raadpleegt u de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md).
