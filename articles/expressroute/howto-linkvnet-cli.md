---
title: 'Azure-ExpressRoute: een VNet koppelen aan het circuit: CLI'
description: In dit artikel wordt beschreven hoe u virtuele netwerken (VNets) koppelen aan ExpressRoute-circuits met behulp van de Resource Manager-implementatiemodel en de CLI.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: a8814030e6c4345227ec05ea1554104e0b21efbc
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076545"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Een virtueel netwerk verbinden met een ExpressRoute-circuit met behulp van CLI

Dit artikel helpt u virtuele netwerken (VNets) koppelen aan Azure ExpressRoute-circuits met behulp van CLI. Als u wilt koppelen met behulp van Azure CLI, moeten de virtuele netwerken worden gemaakt met het Resource Manager-implementatiemodel. Ze kunnen zijn in het hetzelfde abonnement of onderdeel van een ander abonnement. Als u een andere methode gebruiken wilt voor uw VNet verbinden met een ExpressRoute-circuit, kunt u een artikel in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Configuratievereisten

* U moet de nieuwste versie van de opdrachtregelinterface (CLI). Zie voor meer informatie, [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).

* U moet nemen de [vereisten](expressroute-prerequisites.md), [routeringsvereisten](expressroute-routing.md), en [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.

* U moet een actief ExpressRoute-circuit hebben. 
  * Volg de instructies voor [maken van een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit inschakelen door de connectiviteitsprovider. 
  * Zorg ervoor dat u Azure private peering is geconfigureerd voor uw circuit hebt. Zie de [routering configureren](howto-routing-cli.md) artikel voor routeringsinstructies. 
  * Zorg ervoor dat de persoonlijke Azure-peering is geconfigureerd. De BGP-peering tussen uw netwerk en Microsoft moet omhoog zodat u end-to-end-connectiviteit kunt inschakelen.
  * Zorg ervoor dat u hebt een virtueel netwerk en een virtuele netwerkgateway gemaakt en volledig is ingericht. Volg de instructies voor [een virtuele netwerkgateway configureren voor ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Zorg ervoor dat u `--gateway-type ExpressRoute`.

* U kunt maximaal 10 virtuele netwerken koppelen aan een standard ExpressRoute-circuit. Alle virtuele netwerken moeten zich in dezelfde geopolitieke regio bij het gebruik van een standard ExpressRoute-circuit. 

* Een enkel VNet kan worden gekoppeld aan maximaal vier ExpressRoute-circuits. De procedure hieronder gebruiken om te maken van een nieuw verbindingsobject voor elk ExpressRoute-circuit dat u verbinding maakt. De ExpressRoute-circuits kunnen zich in hetzelfde abonnement, verschillende abonnementen of een combinatie van beide.

* Als u de invoegtoepassing ExpressRoute premium inschakelt, kunt u een virtueel netwerk buiten de geopolitieke regio van het ExpressRoute-circuit koppelen of een groter aantal virtuele netwerken verbinden met uw ExpressRoute-circuit. Zie voor meer informatie over de premium-invoegtoepassing voor de [Veelgestelde vragen over](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Een virtueel netwerk in hetzelfde abonnement verbinden met een circuit

U kunt verbinding maken met een virtuele netwerkgateway aan een ExpressRoute-circuit met behulp van het voorbeeld. Zorg ervoor dat de virtuele netwerkgateway is gemaakt en gereed is voor het koppelen voordat u de opdracht uitvoert.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Een virtueel netwerk in een ander abonnement verbinden met een circuit

U kunt een ExpressRoute-circuit delen voor meerdere abonnementen. De afbeelding hieronder ziet een eenvoudige schematische van hoe delen werkt voor ExpressRoute-circuits voor meerdere abonnementen.

Elk van de kleinere clouds binnen de grote cloud wordt gebruikt voor abonnementen die deel uitmaken van verschillende afdelingen binnen een organisatie. Elk van de afdelingen binnen de organisatie kan hun eigen abonnement gebruiken voor het implementeren van hun services-- maar één ExpressRoute-circuit terugverbinding maken met uw on-premises netwerk kunnen delen. Één afdeling (in dit voorbeeld: IT) kunt eigenaar van het ExpressRoute-circuit. Andere abonnementen binnen de organisatie kunnen het ExpressRoute-circuit gebruiken.

> [!NOTE]
> Connectiviteit en de bandbreedte kosten in rekening gebracht voor het toegewezen circuit wordt toegepast op de eigenaar van het ExpressRoute-Circuit. Alle virtuele netwerken delen de dezelfde bandbreedte.
> 
> 

![Abonnementoverschrijdende connectiviteit](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Beheer - Circuit eigenaren en Circuitgebruikers

De eigenaar van het Circuit is een geautoriseerde gebruiker van de kracht van de resource van ExpressRoute-circuit. De eigenaar van het Circuit kunt autorisaties maken die kunnen worden ingewisseld door 'Circuitgebruikers'. Circuitgebruikers zijn eigenaren van virtuele netwerkgateways die zich niet binnen hetzelfde abonnement bevinden als het ExpressRoute-circuit. Circuitgebruikers kunnen autorisaties willen (één autorisatie per virtueel netwerk) inwisselen.

De eigenaar van het Circuit heeft de mogelijkheid om te wijzigen en autorisaties op elk gewenst moment intrekken. Wanneer een machtiging is ingetrokken, worden alle koppeling verbindingen verwijderd uit het abonnement waarvan toegang is ingetrokken.

### <a name="circuit-owner-operations"></a>Circuit eigenaar bewerkingen

**Maken van een autorisatieregels**

De eigenaar van het Circuit maakt een autorisatie, waarmee een autorisatiesleutel die door de gebruiker van een Circuit kan worden gebruikt om hun virtuele netwerkgateways aan ExpressRoute-circuit wordt gemaakt. Een autorisatie is geldig voor slechts één verbinding.

Het volgende voorbeeld laat zien hoe een autorisatie maken:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Het antwoord bevat de autorisatiesleutel en -status:

```azurecli
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Om te controleren van autorisaties**

De eigenaar van het Circuit kunt bekijken van alle machtigingen die op een bepaalde circuit zijn uitgegeven door het volgende voorbeeld uitvoert:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Om toe te voegen autorisaties**

De eigenaar van het Circuit kunt autorisaties toevoegen met behulp van het volgende voorbeeld:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Verwijderen van autorisaties**

De eigenaar van het Circuit kunt intrekken/verwijderen autorisaties voor de gebruiker door het volgende voorbeeld uitvoert:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Circuit gebruikersbewerkingen

De gebruiker Circuit moet de peer-ID en een autorisatiesleutel van de eigenaar van het Circuit. De autorisatiesleutel is een GUID.

```azurecli
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Een verbinding-autorisatie inwisselen**

De gebruiker Circuit kan het volgende voorbeeld voor een koppeling autorisatie inwisselen uitvoeren:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Vrij te geven van autorisatie om een verbinding**

U kunt een autorisatie vrijgeven door het verwijderen van de verbinding die het ExpressRoute-circuit aan het virtuele netwerk is gekoppeld.

## <a name="modify-a-virtual-network-connection"></a>Een virtueel netwerkverbinding wijzigen
U kunt bepaalde eigenschappen van de verbinding van een virtueel netwerk kunt bijwerken. 

**Het gewicht van de verbinding bijwerken**

Het virtuele netwerk kan worden verbonden met meerdere ExpressRoute-circuits. U kunt hetzelfde voorvoegsel van meer dan één ExpressRoute-circuit ontvangen. Om te kiezen welke verbinding voor het verzenden van verkeer dat is bestemd voor dit voorvoegsel, kunt u *RoutingWeight* van een verbinding. Verkeer wordt verzonden via de verbinding met de hoogste *RoutingWeight*.

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Het bereik van de *RoutingWeight* is 0 tot en met 32.000. De standaardwaarde is 0.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute FastPath configureren 
U kunt [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) inschakelen als uw ExpressRoute-circuit zich op [ExpressRoute direct](expressroute-erdirect-about.md) bevindt en uw virtuele netwerk-gateway zeer presteert of ErGw3AZ. FastPath verbetert de preformiteit van het gegevenspad, zoals pakketten per seconde, en verbindingen per seconde tussen uw on-premises netwerk en het virtuele netwerk. 

> [!NOTE] 
> Als u al een virtuele netwerk verbinding hebt, maar FastPath nog niet hebt ingeschakeld, moet u de virtuele netwerk verbinding verwijderen en een nieuwe maken. 
> 
>  

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```


## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over ExpressRoute raadpleegt u de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md).
