---
title: 'ExpressRoute: een VNet koppelen aan een circuit: Azure PowerShell'
description: Dit document geeft een overzicht van hoe u virtuele netwerken (VNets) koppelen aan expressroute-circuits met behulp van het implementatiemodel resourcebeheer en PowerShell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 05/20/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 242f52d643e817730772a7d678a219c2b6149d2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235471"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Een virtueel netwerk verbinden met een ExpressRoute-circuit
> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Powershell](expressroute-howto-linkvnet-arm.md)
> * [Azure-CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-linkvnet-classic.md)
>

Met dit artikel u virtuele netwerken (VNets) koppelen aan Azure ExpressRoute-circuits met behulp van het implementatiemodel Resource Manager en PowerShell. Virtuele netwerken kunnen deel uitmaken van hetzelfde abonnement of een deel van een ander abonnement. In dit artikel ziet u ook hoe u een virtuele netwerkkoppeling bijwerken.

* U maximaal 10 virtuele netwerken koppelen aan een standaard ExpressRoute-circuit. Alle virtuele netwerken moeten zich in dezelfde geopolitieke regio bevinden wanneer ze een standaard ExpressRoute-circuit gebruiken. 

* Een enkele VNet kan worden gekoppeld aan maximaal vier ExpressRoute-circuits. Gebruik de stappen in dit artikel om een nieuw verbindingsobject te maken voor elk ExpressRoute-circuit waartoe u verbinding maakt. De ExpressRoute-circuits kunnen in hetzelfde abonnement, verschillende abonnementen of een mix van beide zijn.

* U virtuele netwerken buiten de geopolitieke regio van het ExpressRoute-circuit koppelen of een groter aantal virtuele netwerken aansluiten op uw ExpressRoute-circuit als u de ExpressRoute premium-add-on hebt ingeschakeld. Bekijk de [FAQ](expressroute-faqs.md) voor meer informatie over de premium add-on.


## <a name="before-you-begin"></a>Voordat u begint

* Controleer de [vereisten,](expressroute-prerequisites.md) [routeringsvereisten](expressroute-routing.md)en [werkstromen](expressroute-workflows.md) voordat u met de configuratie begint.

* U moet een actief ExpressRoute-circuit hebben. 
  * Volg de instructies om [een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) te maken en laat het circuit inschakelen door uw connectiviteitsprovider. 
  * Zorg ervoor dat Azure private peering is geconfigureerd voor uw circuit. Zie het [routeringsartikel configureren](expressroute-howto-routing-arm.md) voor routeringsinstructies. 
  * Controleer of Azure private peering is geconfigureerd en dat het BGP-peering tussen uw netwerk en Microsoft is ingeschakeld, zodat u end-to-end-connectiviteit inschakelen.
  * Zorg ervoor dat u een virtueel netwerk en een virtuele netwerkgateway hebt gemaakt en volledig is ingericht. Volg de instructies om [een virtuele netwerkgateway voor ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)te maken. Een virtuele netwerkgateway voor ExpressRoute maakt gebruik van de GatewayType 'ExpressRoute', niet VPN.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Een virtueel netwerk in hetzelfde abonnement aansluiten op een circuit
U een virtuele netwerkgateway aansluiten op een ExpressRoute-circuit met behulp van de volgende cmdlet. Zorg ervoor dat de virtuele netwerkgateway is gemaakt en klaar is voor koppeling voordat u de cmdlet uitvoert:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Een virtueel netwerk in een ander abonnement verbinden met een circuit
U een ExpressRoute-circuit delen via meerdere abonnementen. De volgende afbeelding toont een eenvoudig schema van hoe delen werkt voor ExpressRoute-circuits in meerdere abonnementen.

Elk van de kleinere clouds binnen de grote cloud wordt gebruikt om abonnementen weer te geven die behoren tot verschillende afdelingen binnen een organisatie. Elk van de afdelingen binnen de organisatie kan hun eigen abonnement gebruiken voor het implementeren van hun services - maar ze kunnen één ExpressRoute-circuit delen om verbinding te maken met uw on-premises netwerk. Eén afdeling (in dit voorbeeld: IT) kan eigenaar zijn van het ExpressRoute circuit. Andere abonnementen binnen de organisatie kunnen gebruik maken van het ExpressRoute circuit.

> [!NOTE]
> Connectiviteits- en bandbreedtekosten voor het ExpressRoute-circuit worden toegepast op de eigenaar van het abonnement. Alle virtuele netwerken delen dezelfde bandbreedte.
> 
> 

![Connectiviteit met abonnementen voor abonnementen](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Administratie - circuiteigenaren en circuitgebruikers

De 'circuit eigenaar' is een geautoriseerde Power User van de ExpressRoute circuit resource. De eigenaar van het circuit kan autorisaties maken die kunnen worden ingewisseld door 'circuitgebruikers'. Circuitgebruikers zijn eigenaars van virtuele netwerkgateways die niet binnen hetzelfde abonnement vallen als het ExpressRoute-circuit. Circuitgebruikers kunnen autorisaties inwisselen (één autorisatie per virtueel netwerk).

De eigenaar van het circuit heeft de bevoegdheid om autorisaties op elk gewenst moment te wijzigen en in te trekken. Als u een autorisatie intrekt, worden alle koppelingsverbindingen verwijderd uit het abonnement waarvan de toegang is ingetrokken.

### <a name="circuit-owner-operations"></a>Circuit eigenaar operaties

**Een autorisatie maken**

De eigenaar van het circuit maakt een autorisatie. Dit resulteert in het maken van een autorisatiesleutel die door een circuitgebruiker kan worden gebruikt om hun virtuele netwerkgateways aan te sluiten op het ExpressRoute-circuit. Een autorisatie is geldig voor slechts één verbinding.

In het volgende cmdletfragment ziet u hoe u een autorisatie maakt:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


Het antwoord hierop bevat de autorisatiesleutel en de status:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Autorisaties controleren**

De eigenaar van het circuit kan alle autorisaties die op een bepaald circuit zijn afgegeven, bekijken door de volgende cmdlet uit te voeren:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Autorisaties toevoegen**

De eigenaar van het circuit kan autorisaties toevoegen met behulp van de volgende cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Autorisaties verwijderen**

De eigenaar van het circuit kan autorisaties voor de gebruiker intrekken/verwijderen door de volgende cmdlet uit te voeren:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Gebruikersbewerkingen van het circuit

De circuitgebruiker heeft de peer-ID en een autorisatiesleutel van de eigenaar van het circuit nodig. De autorisatiesleutel is een GUID.

Peer ID kan worden gecontroleerd via de volgende opdracht:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Een verbindingsautorisatie inwisselen**

De gebruiker van het circuit kan de volgende cmdlet uitvoeren om een koppelingsautorisatie in te wisselen:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Een verbindingsautorisatie vrijgeven**

U een autorisatie vrijgeven door de verbinding te verwijderen die het ExpressRoute-circuit koppelt aan het virtuele netwerk.

## <a name="modify-a-virtual-network-connection"></a>Een virtuele netwerkverbinding wijzigen
U bepaalde eigenschappen van een virtuele netwerkverbinding bijwerken. 

**Het verbindingsgewicht bijwerken**

Uw virtuele netwerk kan worden aangesloten op meerdere ExpressRoute-circuits. U hetzelfde voorvoegsel van meer dan één ExpressRoute-circuit ontvangen. Als u wilt kiezen welke verbinding verkeer wilt verzenden dat is bestemd voor dit voorvoegsel, u *Routeweggewicht* van een verbinding wijzigen. Verkeer wordt verzonden op de verbinding met het hoogste *routegewicht.*

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Het bereik van *RoutingWeight* is 0 tot 32000. De standaardwaarde is 0.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute FastPath configureren 
U [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) inschakelen als uw virtuele netwerkgateway Ultra Performance of ErGw3AZ is. FastPath verbetert de prestaties van het gegevenspad, zoals pakketten per seconde en verbindingen per seconde tussen uw on-premises netwerk en uw virtuele netwerk. 

**FastPath configureren op een nieuwe verbinding**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**Een bestaande verbinding bijwerken om FastPath in te schakelen**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="next-steps"></a>Volgende stappen
Zie de Veelgestelde vragen over [ExpressRoute voor](expressroute-faqs.md)meer informatie over ExpressRoute.
