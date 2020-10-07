---
title: 'Zelf studie: een VNet koppelen aan een ExpressRoute-circuit-Azure PowerShell'
description: Deze zelf studie biedt een overzicht van hoe u virtuele netwerken (VNets) koppelt aan ExpressRoute-circuits met behulp van het Resource Manager-implementatie model en Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: b536b0e2601ce1ae9dd3d40723f4cab09a3a4c48
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91772954"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Een virtueel netwerk verbinden met een ExpressRoute-circuit
> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure-CLI](howto-linkvnet-cli.md)
> * [Video: Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-linkvnet-classic.md)
>

Dit artikel helpt u virtuele netwerken (VNets) te koppelen aan Azure ExpressRoute-circuits met behulp van het Resource Manager-implementatie model en Power shell. Virtuele netwerken kunnen zich in hetzelfde abonnement benemen of deel uitmaken van een ander abonnement. In dit artikel wordt ook beschreven hoe u een virtuele netwerk koppeling bijwerkt.

* U kunt Maxi maal 10 virtuele netwerken koppelen aan een standaard ExpressRoute-circuit. Alle virtuele netwerken moeten zich in dezelfde geopolitieke regio bevinden wanneer een standaard ExpressRoute-circuit wordt gebruikt. 

* Eén VNet kan worden gekoppeld aan Maxi maal vier ExpressRoute-circuits. Volg de stappen in dit artikel om een nieuw verbindings object te maken voor elk ExpressRoute-circuit waarmee u verbinding maakt. De ExpressRoute-circuits kunnen zich in hetzelfde abonnement, verschillende abonnementen of een combi natie van beide bevindt.

* Als u de Premium-invoeg toepassing ExpressRoute inschakelt, kunt u virtuele netwerken koppelen buiten de geopolitieke regio van het ExpressRoute-circuit. De Premium-invoeg toepassing biedt u ook de mogelijkheid om meer dan 10 virtuele netwerken te verbinden met uw ExpressRoute-circuit, afhankelijk van de gekozen band breedte. Raadpleeg de [Veelgestelde vragen](expressroute-faqs.md) voor meer informatie over de Premium-invoeg toepassing.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Een virtueel netwerk in hetzelfde abonnement verbinden met een circuit
> - Een virtueel netwerk in een ander abonnement verbinden met een circuit
> - Een virtuele netwerk verbinding wijzigen
> - ExpressRoute FastPath configureren

## <a name="prerequisites"></a>Vereisten

* Bekijk de [prerequisites](expressroute-prerequisites.md) [vereisten, routerings behoeften](expressroute-routing.md)en [werk stromen](expressroute-workflows.md) voordat u begint met de configuratie.

* U moet een actief ExpressRoute-circuit hebben. 
  * Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit ingeschakeld door uw connectiviteits provider. 
  * Zorg ervoor dat u persoonlijke Azure-peering voor uw circuit hebt geconfigureerd. Zie het artikel [route ring configureren](expressroute-howto-routing-arm.md) voor instructies voor route ring. 
  * Zorg ervoor dat persoonlijke Azure-peering is geconfigureerd en dat BGP-peering tussen uw netwerk en micro soft wordt ingesteld voor end-to-end-connectiviteit.
  * Zorg ervoor dat u een virtueel netwerk en een virtuele netwerk gateway hebt gemaakt en volledig hebt ingericht. Volg de instructies voor het [maken van een virtuele netwerk gateway voor ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Een virtuele netwerk gateway voor ExpressRoute maakt gebruik van de gateway type ' ExpressRoute ', niet VPN.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Een virtueel netwerk in hetzelfde abonnement verbinden met een circuit
U kunt een virtuele netwerk gateway verbinden met een ExpressRoute-circuit met behulp van de volgende cmdlet. Zorg ervoor dat de virtuele netwerk gateway is gemaakt en gereed is voor koppeling voordat u de cmdlet uitvoert:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Een virtueel netwerk in een ander abonnement verbinden met een circuit
U kunt een ExpressRoute-circuit delen tussen meerdere abonnementen. In de volgende afbeelding ziet u hoe delen werkt voor ExpressRoute-circuits voor meerdere abonnementen.

Elk van de kleinere Clouds in de grote Cloud wordt gebruikt om abonnementen weer te geven die deel uitmaken van verschillende afdelingen binnen een organisatie. Elk van de afdelingen in de organisatie gebruikt een eigen abonnement voor het implementeren van hun services, maar ze kunnen een enkel ExpressRoute-circuit delen om verbinding te maken met uw on-premises netwerk. Eén afdeling (in dit voor beeld: IT) kan eigenaar zijn van het ExpressRoute-circuit. Andere abonnementen binnen de organisatie kunnen gebruikmaken van het ExpressRoute-circuit.

> [!NOTE]
> Connectiviteits-en bandbreedte kosten voor het ExpressRoute-circuit worden toegepast op de eigenaar van het abonnement. Alle virtuele netwerken delen dezelfde band breedte.
> 

:::image type="content" source="./media/expressroute-howto-linkvnet-classic/cross-subscription.png" alt-text="Connectiviteit tussen abonnementen":::

### <a name="administration---circuit-owners-and-circuit-users"></a>Beheer-circuit eigen aren en circuit gebruikers

De ' circuit eigenaar ' is een geautoriseerde hoofd gebruiker van de ExpressRoute-circuit resource. De eigenaar van het circuit kan autorisaties maken die kunnen worden ingewisseld door ' circuit gebruikers '. Circuit gebruikers zijn eigen aren van virtuele netwerk gateways die zich niet in hetzelfde abonnement bevinden als het ExpressRoute-circuit. Circuit gebruikers kunnen autorisaties inwisselen (één autorisatie per virtueel netwerk).

De eigenaar van het circuit heeft de bevoegdheid om autorisaties op elk gewenst moment te wijzigen en in te trekken. Het intrekken van een autorisatie resulteert in alle koppelings verbindingen die worden verwijderd uit het abonnement waarvan de toegang is ingetrokken.

### <a name="circuit-owner-operations"></a>Bewerkingen voor circuit eigenaars

**Een autorisatie maken**

De eigenaar van het circuit maakt een autorisatie, waarmee een autorisatie sleutel wordt gemaakt die door een circuit gebruiker moet worden gebruikt om de virtuele netwerk gateways te verbinden met het ExpressRoute-circuit. Een autorisatie is slechts voor één verbinding geldig.

Het volgende cmdlet-fragment laat zien hoe u een autorisatie maakt:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```

De reactie op de vorige opdrachten bevatten de autorisatie sleutel en de status:

```azurepowershell
Name                   : MyAuthorization1
Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
AuthorizationKey       : ####################################
AuthorizationUseStatus : Available
ProvisioningState      : Succeeded
```

**Autorisaties controleren**

De eigenaar van het circuit kan alle autorisaties controleren die zijn uitgegeven op een bepaald circuit door de volgende cmdlet uit te voeren:

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

### <a name="circuit-user-operations"></a>Gebruikers bewerkingen circuit

De circuit gebruiker heeft de peer-ID en een autorisatie sleutel nodig van de eigenaar van het circuit. De autorisatie sleutel is een GUID.

De peer-ID kan worden gecontroleerd met de volgende opdracht:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Een verbindings autorisatie inwisselen**

De circuit gebruiker kan de volgende cmdlet uitvoeren om een koppelings autorisatie te verwisselen:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Een verbindings autorisatie vrijgeven**

U kunt een autorisatie vrijgeven door de verbinding die het ExpressRoute-circuit koppelt, te verwijderen in het virtuele netwerk.

## <a name="modify-a-virtual-network-connection"></a>Een virtuele netwerk verbinding wijzigen
U kunt bepaalde eigenschappen van een virtuele netwerk verbinding bijwerken. 

**Het verbindings gewicht bijwerken**

Uw virtuele netwerk kan worden verbonden met meerdere ExpressRoute-circuits. U kunt hetzelfde voor voegsel van meer dan één ExpressRoute-circuit ontvangen. U kunt de *RoutingWeight* van een verbinding wijzigen om te kiezen welke verbinding moet worden verzonden naar het verkeer dat bestemd is voor dit voor voegsel. Verkeer wordt verzonden via de verbinding met de hoogste *RoutingWeight*.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Het bereik van *RoutingWeight* is 0 tot en met 32000. De standaardwaarde is 0.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute FastPath configureren 
U kunt [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) inschakelen als uw virtuele netwerk gateway Ultra Performance of ErGw3AZ is. FastPath verbetert de prestaties van het gegevenspad, zoals pakketten per seconde en verbindingen per seconde tussen uw on-premises netwerk en het virtuele netwerk. 

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

## <a name="clean-up-resources"></a>Resources opschonen

Als u de ExpressRoute-verbinding niet meer nodig hebt, gebruikt u de `Remove-AzVirtualNetworkGatewayConnection` opdracht om de koppeling tussen de gateway en het circuit te verwijderen uit het abonnement waarin de gateway zich bevindt.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection "MyConnection" -ResourceGroupName "MyRG"
```

## <a name="next-steps"></a>Volgende stappen
Zie de veelgestelde vragen over ExpressRoute voor meer informatie over ExpressRoute.

> [!div class="nextstepaction"]
> [Veelgestelde vragen ExpressRoute](expressroute-faqs.md)
