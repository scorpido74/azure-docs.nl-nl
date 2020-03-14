---
title: 'ExpressRoute: een VNet koppelen aan een circuit: Azure PowerShell'
description: Dit document bevat een overzicht van virtuele netwerken (VNets) koppelen aan ExpressRoute-circuits met behulp van de Resource Manager-implementatiemodel en PowerShell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 05/20/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 2685b9b519eaac453726f4923c46f1604cbd4681
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280857"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Een virtueel netwerk verbinden met een ExpressRoute-circuit
> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video-Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-linkvnet-classic.md)
>

Dit artikel helpt u virtuele netwerken (VNets) koppelen aan Azure ExpressRoute-circuits met behulp van de Resource Manager-implementatiemodel en PowerShell. Virtuele netwerken kunnen ofwel zich in het hetzelfde abonnement of een deel van een ander abonnement. In dit artikel leest u ook hoe de koppeling van een virtueel netwerk bijwerken.

* U kunt maximaal 10 virtuele netwerken koppelen aan een standard ExpressRoute-circuit. Alle virtuele netwerken moeten zich in dezelfde geopolitieke regio bij het gebruik van een standard ExpressRoute-circuit. 

* Een enkel VNet kan worden gekoppeld aan maximaal vier ExpressRoute-circuits. Gebruik de stappen in dit artikel om te maken van een nieuw verbindingsobject voor elk ExpressRoute-circuit dat u verbinding maakt. De ExpressRoute-circuits kunnen zich in hetzelfde abonnement, verschillende abonnementen of een combinatie van beide.

* U kunt virtuele netwerken buiten de geopolitieke regio van het ExpressRoute-circuit koppelen of een groter aantal virtuele netwerken verbinden met uw ExpressRoute-circuit als u de premium-invoegtoepassing voor ExpressRoute hebt ingeschakeld. Raadpleeg de [Veelgestelde vragen](expressroute-faqs.md) voor meer informatie over de Premium-invoeg toepassing.


## <a name="before-you-begin"></a>Voordat u begint

* Bekijk de [](expressroute-prerequisites.md) [vereisten, routerings behoeften](expressroute-routing.md)en [werk stromen](expressroute-workflows.md) voordat u begint met de configuratie.

* U moet een actief ExpressRoute-circuit hebben. 
  * Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit ingeschakeld door uw connectiviteits provider. 
  * Zorg ervoor dat u Azure private peering is geconfigureerd voor uw circuit hebt. Zie het artikel [route ring configureren](expressroute-howto-routing-arm.md) voor instructies voor route ring. 
  * Zorg ervoor dat de persoonlijke Azure-peering is geconfigureerd en van de BGP-peering tussen uw netwerk en Microsoft is, zodat u end-to-end-connectiviteit kunt inschakelen.
  * Zorg ervoor dat u hebt een virtueel netwerk en een virtuele netwerkgateway gemaakt en volledig is ingericht. Volg de instructies voor het [maken van een virtuele netwerk gateway voor ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Het GatewayType 'ExpressRoute', niet VPN maakt gebruik van een virtuele netwerkgateway voor ExpressRoute.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Een virtueel netwerk in hetzelfde abonnement verbinden met een circuit
U kunt verbinding maken met een virtuele netwerkgateway aan een ExpressRoute-circuit met behulp van de volgende cmdlet. Zorg ervoor dat de virtuele netwerkgateway is gemaakt en gereed is voor het koppelen voordat u de cmdlet uitvoeren:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Een virtueel netwerk in een ander abonnement verbinden met een circuit
U kunt een ExpressRoute-circuit delen voor meerdere abonnementen. De volgende afbeelding toont een eenvoudig schema van hoe delen werkt voor ExpressRoute-circuits voor meerdere abonnementen.

Elk van de kleinere clouds binnen de grote cloud wordt gebruikt voor abonnementen die deel uitmaken van verschillende afdelingen binnen een organisatie. Elk van de afdelingen binnen de organisatie kan hun eigen abonnement gebruiken voor het implementeren van hun services-- maar één ExpressRoute-circuit terugverbinding maken met uw on-premises netwerk kunnen delen. Één afdeling (in dit voorbeeld: IT) kunt eigenaar van het ExpressRoute-circuit. Andere abonnementen binnen de organisatie kunnen het ExpressRoute-circuit gebruiken.

> [!NOTE]
> Connectiviteit en de bandbreedte kosten in rekening gebracht voor het ExpressRoute-circuit wordt toegepast op de eigenaar van het abonnement. Alle virtuele netwerken delen de dezelfde bandbreedte.
> 
> 

![Abonnementoverschrijdende connectiviteit](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Beheer - circuit eigenaren en circuitgebruikers

De circuiteigenaar is een geautoriseerde gebruiker van de kracht van de resource van ExpressRoute-circuit. De circuiteigenaar van het kunt autorisaties maken die kunnen worden ingewisseld door 'circuitgebruikers'. Circuitgebruikers zijn eigenaren van virtuele netwerkgateways die zich niet binnen hetzelfde abonnement bevinden als het ExpressRoute-circuit. Circuitgebruikers kunnen autorisaties willen (één autorisatie per virtueel netwerk) inwisselen.

De circuiteigenaar van het heeft de mogelijkheid om te wijzigen en autorisaties op elk gewenst moment intrekken. Intrekken van de resultaten van een autorisatie in alle koppeling verbindingen wordt verwijderd uit het abonnement waarvan toegang is ingetrokken.

### <a name="circuit-owner-operations"></a>Circuit eigenaar bewerkingen

**Een autorisatie maken**

De circuiteigenaar van het maakt een autorisatie. Dit resulteert in het maken van een autorisatiesleutel die door de gebruiker van een circuit kan worden gebruikt om hun virtuele netwerkgateways aan ExpressRoute-circuit. Een autorisatie is geldig voor slechts één verbinding.

De volgende cmdlet-fragment ziet over het maken van een autorisatie:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


Het antwoord op deze bevat de autorisatiesleutel en -status:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Autorisaties controleren**

De circuiteigenaar van het kunt bekijken van alle machtigingen die op een bepaalde circuit zijn uitgegeven door de volgende cmdlet uit te voeren:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Autorisaties toevoegen**

De circuiteigenaar van het kunt autorisaties toevoegen met behulp van de volgende cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Autorisaties verwijderen**

De circuiteigenaar van het kunt intrekken/verwijderen autorisaties voor de gebruiker door de volgende cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Circuit gebruikersbewerkingen

De gebruiker circuit moet de peer-ID en een autorisatiesleutel van de circuiteigenaar van het. De autorisatiesleutel is een GUID.

Peer-ID kan worden gecontroleerd met de volgende opdracht:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Een verbindings autorisatie inwisselen**

De gebruiker circuit kan de volgende cmdlet als u wilt een koppeling autorisatie inwisselen uitvoeren:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Een verbindings autorisatie vrijgeven**

U kunt een autorisatie vrijgeven door het verwijderen van de verbinding die het ExpressRoute-circuit aan het virtuele netwerk is gekoppeld.

## <a name="modify-a-virtual-network-connection"></a>Een virtueel netwerkverbinding wijzigen
U kunt bepaalde eigenschappen van de verbinding van een virtueel netwerk kunt bijwerken. 

**Het verbindings gewicht bijwerken**

Het virtuele netwerk kan worden verbonden met meerdere ExpressRoute-circuits. U kunt hetzelfde voorvoegsel van meer dan één ExpressRoute-circuit ontvangen. U kunt de *RoutingWeight* van een verbinding wijzigen om te kiezen welke verbinding moet worden verzonden naar het verkeer dat bestemd is voor dit voor voegsel. Verkeer wordt verzonden via de verbinding met de hoogste *RoutingWeight*.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Het bereik van *RoutingWeight* is 0 tot en met 32000. De standaardwaarde is 0.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute FastPath configureren 
U kunt [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) inschakelen als uw ExpressRoute-circuit zich op [ExpressRoute direct](expressroute-erdirect-about.md) bevindt en de gateway van uw virtuele netwerk Super prestaties of ErGw3AZ. FastPath verbetert de prestaties van het gegevenspad, zoals pakketten per seconde en verbindingen per seconde tussen uw on-premises netwerk en het virtuele netwerk. 

**FastPath configureren voor een nieuwe verbinding**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**Een bestaande verbinding bijwerken om FastPath in te scha kelen**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="next-steps"></a>Volgende stappen
Voor meer informatie over ExpressRoute raadpleegt u de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md).
