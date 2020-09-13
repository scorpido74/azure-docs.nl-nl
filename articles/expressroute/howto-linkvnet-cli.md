---
title: 'Azure-ExpressRoute: een VNet koppelen aan het circuit: CLI'
description: In dit artikel wordt beschreven hoe u virtuele netwerken (VNets) koppelt aan ExpressRoute-circuits met behulp van het Resource Manager-implementatie model en de CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/27/2020
ms.author: duau
ms.openlocfilehash: ac36e303cbeaf5167b5bbec3cea503c37b276058
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89393204"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Een virtueel netwerk verbinden met een ExpressRoute-circuit met behulp van CLI

Dit artikel helpt u virtuele netwerken (VNets) te koppelen aan Azure ExpressRoute-circuits met behulp van CLI. Als u een koppeling wilt maken met behulp van Azure CLI, moeten de virtuele netwerken worden gemaakt met behulp van het Resource Manager-implementatie model. Ze kunnen zich in hetzelfde abonnement bevinden of deel uitmaken van een ander abonnement. Als u een andere methode wilt gebruiken om uw VNet te verbinden met een ExpressRoute-circuit, kunt u een artikel selecteren in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure-CLI](howto-linkvnet-cli.md)
> * [Video: Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Configuratievereisten

* U hebt de nieuwste versie van de opdracht regel interface (CLI) nodig. Zie [de Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli)voor meer informatie.

* U moet de [vereiste onderdelen](expressroute-prerequisites.md), [routerings vereisten](expressroute-routing.md)en [werk stromen](expressroute-workflows.md) controleren voordat u begint met de configuratie.

* U moet een actief ExpressRoute-circuit hebben. 
  * Volg de instructies voor het [maken van een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit ingeschakeld door uw connectiviteits provider. 
  * Zorg ervoor dat u persoonlijke Azure-peering voor uw circuit hebt geconfigureerd. Zie het artikel [route ring configureren](howto-routing-cli.md) voor instructies voor route ring. 
  * Zorg ervoor dat persoonlijke Azure-peering is geconfigureerd. De BGP-peering tussen uw netwerk en micro soft moet actief zijn, zodat u end-to-end connectiviteit kunt inschakelen.
  * Zorg ervoor dat u een virtueel netwerk en een virtuele netwerk gateway hebt gemaakt en volledig hebt ingericht. Volg de instructies voor het [configureren van een virtuele netwerk gateway voor ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Zorg ervoor dat u gebruikt `--gateway-type ExpressRoute` .

* U kunt Maxi maal 10 virtuele netwerken koppelen aan een standaard ExpressRoute-circuit. Alle virtuele netwerken moeten zich in dezelfde geopolitieke regio bevinden wanneer een standaard ExpressRoute-circuit wordt gebruikt. 

* Eén VNet kan worden gekoppeld aan Maxi maal vier ExpressRoute-circuits. Gebruik de onderstaande procedure om een nieuw verbindings object te maken voor elk ExpressRoute-circuit waarmee u verbinding maakt. De ExpressRoute-circuits kunnen zich in hetzelfde abonnement, verschillende abonnementen of een combi natie van beide bevindt.

* Als u de Premium-invoeg toepassing ExpressRoute inschakelt, kunt u een virtueel netwerk koppelen buiten de geopolitieke regio van het ExpressRoute-circuit of een groter aantal virtuele netwerken verbinden met uw ExpressRoute-circuit. Raadpleeg de [Veelgestelde vragen](expressroute-faqs.md)voor meer informatie over de Premium-invoeg toepassing.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Een virtueel netwerk in hetzelfde abonnement verbinden met een circuit

U kunt een virtuele netwerk gateway verbinden met een ExpressRoute-circuit met behulp van het voor beeld. Zorg ervoor dat de virtuele netwerk gateway is gemaakt en gereed is voor koppeling voordat u de opdracht uitvoert.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Een virtueel netwerk in een ander abonnement verbinden met een circuit

U kunt een ExpressRoute-circuit delen tussen meerdere abonnementen. In de afbeelding hieronder ziet u hoe delen werkt voor ExpressRoute-circuits voor meerdere abonnementen.

Elk van de kleinere Clouds in de grote Cloud wordt gebruikt om abonnementen weer te geven die deel uitmaken van verschillende afdelingen binnen een organisatie. Elk van de afdelingen in de organisatie kan hun eigen abonnement gebruiken voor het implementeren van hun services, maar ze kunnen een enkel ExpressRoute-circuit delen om verbinding te maken met uw on-premises netwerk. Eén afdeling (in dit voor beeld: IT) kan eigenaar zijn van het ExpressRoute-circuit. Andere abonnementen binnen de organisatie kunnen het ExpressRoute-circuit gebruiken.

> [!NOTE]
> Connectiviteits-en bandbreedte kosten voor het specifieke circuit worden toegepast op de eigenaar van het ExpressRoute-circuit. Alle virtuele netwerken delen dezelfde band breedte.
> 
> 

![Connectiviteit tussen abonnementen](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Beheer-circuit eigen aren en circuit gebruikers

De ' circuit eigenaar ' is een geautoriseerde hoofd gebruiker van de ExpressRoute-circuit resource. De eigenaar van het circuit kan autorisaties maken die kunnen worden ingewisseld door ' circuit gebruikers '. Circuit gebruikers zijn eigen aren van virtuele netwerk gateways die zich niet in hetzelfde abonnement bevinden als het ExpressRoute-circuit. Circuit gebruikers kunnen autorisaties inwisselen (één autorisatie per virtueel netwerk).

De eigenaar van het circuit heeft de bevoegdheid om autorisaties op elk gewenst moment te wijzigen en in te trekken. Wanneer een autorisatie wordt ingetrokken, worden alle koppelings verbindingen verwijderd uit het abonnement waarvan de toegang is ingetrokken.

### <a name="circuit-owner-operations"></a>Bewerkingen voor circuit eigenaars

**Een autorisatie maken**

De eigenaar van het circuit maakt een autorisatie, waarmee een autorisatie sleutel wordt gemaakt die door een circuit gebruiker kan worden gebruikt om de virtuele netwerk gateways te verbinden met het ExpressRoute-circuit. Een autorisatie is slechts voor één verbinding geldig.

In het volgende voor beeld ziet u hoe u een autorisatie maakt:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Het antwoord bevat de autorisatie sleutel en-status:

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Autorisaties controleren**

De eigenaar van het circuit kan alle autorisaties controleren die op een bepaald circuit worden uitgegeven door het volgende voor beeld uit te voeren:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Autorisaties toevoegen**

De eigenaar van het circuit kan autorisaties toevoegen met behulp van het volgende voor beeld:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Autorisaties verwijderen**

De eigenaar van het circuit kan autorisaties voor de gebruiker intrekken/verwijderen door het volgende voor beeld uit te voeren:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Gebruikers bewerkingen circuit

De circuit gebruiker heeft de peer-ID en een autorisatie sleutel nodig van de eigenaar van het circuit. De autorisatie sleutel is een GUID.

```powershell
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Een verbindings autorisatie inwisselen**

De circuit gebruiker kan het volgende voor beeld uitvoeren om een koppelings autorisatie in te wisselen:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Een verbindings autorisatie vrijgeven**

U kunt een autorisatie vrijgeven door de verbinding die het ExpressRoute-circuit koppelt, te verwijderen in het virtuele netwerk.

## <a name="modify-a-virtual-network-connection"></a>Een virtuele netwerk verbinding wijzigen
U kunt bepaalde eigenschappen van een virtuele netwerk verbinding bijwerken. 

**Het verbindings gewicht bijwerken**

Uw virtuele netwerk kan worden verbonden met meerdere ExpressRoute-circuits. U kunt hetzelfde voor voegsel van meer dan één ExpressRoute-circuit ontvangen. U kunt de *RoutingWeight* van een verbinding wijzigen om te kiezen welke verbinding moet worden verzonden naar het verkeer dat bestemd is voor dit voor voegsel. Verkeer wordt verzonden via de verbinding met de hoogste *RoutingWeight*.

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Het bereik van *RoutingWeight* is 0 tot en met 32000. De standaardwaarde is 0.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute FastPath configureren 
U kunt [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) inschakelen als uw virtuele netwerk gateway Ultra Performance of ErGw3AZ is. FastPath verbetert de preformiteit van het gegevenspad, zoals pakketten per seconde, en verbindingen per seconde tussen uw on-premises netwerk en het virtuele netwerk. 

**FastPath configureren voor een nieuwe verbinding**

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Een bestaande verbinding bijwerken om FastPath in te scha kelen**

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```

## <a name="next-steps"></a>Volgende stappen

Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md)voor meer informatie over ExpressRoute.
