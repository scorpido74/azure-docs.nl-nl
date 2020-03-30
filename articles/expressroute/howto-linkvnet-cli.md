---
title: 'Azure ExpressRoute: een VNet koppelen aan circuit: CLI'
description: In dit artikel ziet u hoe u virtuele netwerken (VNets) koppelt aan expressroute-circuits met behulp van het implementatiemodel Resource Manager en CLI.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: fdd809bcba703dbd8f9ee1e7c18185fd20e4586f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476131"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Een virtueel netwerk aansluiten op een ExpressRoute-circuit met CLI

Met dit artikel u virtuele netwerken (VNets) koppelen aan Azure ExpressRoute-circuits met CLI. Als u een koppeling wilt maken met Azure CLI, moeten de virtuele netwerken worden gemaakt met behulp van het implementatiemodel Resource Beheer. Ze kunnen in hetzelfde abonnement, of een deel van een ander abonnement. Als u een andere methode wilt gebruiken om uw VNet aan een ExpressRoute-circuit te verbinden, u een artikel selecteren in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Powershell](expressroute-howto-linkvnet-arm.md)
> * [Azure-CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Configuratievereisten

* U hebt de nieuwste versie van de command-line interface (CLI) nodig. Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli)voor meer informatie.

* U moet de [vereisten,](expressroute-prerequisites.md) [routeringsvereisten](expressroute-routing.md)en [werkstromen](expressroute-workflows.md) bekijken voordat u met de configuratie begint.

* U moet een actief ExpressRoute-circuit hebben. 
  * Volg de instructies om [een ExpressRoute-circuit](howto-circuit-cli.md) te maken en laat het circuit inschakelen door uw connectiviteitsprovider. 
  * Zorg ervoor dat Azure private peering is geconfigureerd voor uw circuit. Zie het [routeringsartikel configureren](howto-routing-cli.md) voor routeringsinstructies. 
  * Controleer of Azure private peering is geconfigureerd. De BGP-peering tussen uw netwerk en Microsoft moet up zijn, zodat u end-to-end-connectiviteit inschakelen.
  * Zorg ervoor dat u een virtueel netwerk en een virtuele netwerkgateway hebt gemaakt en volledig is ingericht. Volg de instructies voor [het configureren van een virtuele netwerkgateway voor ExpressRoute.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli) Zorg ervoor `--gateway-type ExpressRoute`dat u .

* U maximaal 10 virtuele netwerken koppelen aan een standaard ExpressRoute-circuit. Alle virtuele netwerken moeten zich in dezelfde geopolitieke regio bevinden wanneer ze een standaard ExpressRoute-circuit gebruiken. 

* Een enkele VNet kan worden gekoppeld aan maximaal vier ExpressRoute-circuits. Gebruik het onderstaande proces om een nieuw verbindingsobject te maken voor elk ExpressRoute-circuit waaru verbinding mee maakt. De ExpressRoute-circuits kunnen in hetzelfde abonnement, verschillende abonnementen of een mix van beide zijn.

* Als u de ExpressRoute premium add-on inschakelt, u een virtueel netwerk buiten de geopolitieke regio van het ExpressRoute-circuit koppelen of een groter aantal virtuele netwerken verbinden met uw ExpressRoute-circuit. Zie de [veelgestelde vragen voor](expressroute-faqs.md)meer informatie over de premium add-on.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Een virtueel netwerk in hetzelfde abonnement aansluiten op een circuit

U een virtuele netwerkgateway aansluiten op een ExpressRoute-circuit met behulp van het voorbeeld. Zorg ervoor dat de virtuele netwerkgateway is gemaakt en klaar is voor koppeling voordat u de opdracht uitvoert.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Een virtueel netwerk in een ander abonnement verbinden met een circuit

U een ExpressRoute-circuit delen via meerdere abonnementen. De onderstaande figuur toont een eenvoudig schema van hoe delen werkt voor ExpressRoute-circuits over meerdere abonnementen.

Elk van de kleinere clouds binnen de grote cloud wordt gebruikt om abonnementen weer te geven die behoren tot verschillende afdelingen binnen een organisatie. Elk van de afdelingen binnen de organisatie kan hun eigen abonnement gebruiken voor het implementeren van hun services - maar ze kunnen één ExpressRoute-circuit delen om verbinding te maken met uw on-premises netwerk. Eén afdeling (in dit voorbeeld: IT) kan eigenaar zijn van het ExpressRoute circuit. Andere abonnementen binnen de organisatie kunnen gebruik maken van het ExpressRoute circuit.

> [!NOTE]
> Connectiviteit en bandbreedte kosten voor de dedicated circuit zal worden toegepast op de ExpressRoute Circuit Eigenaar. Alle virtuele netwerken delen dezelfde bandbreedte.
> 
> 

![Connectiviteit met abonnementen voor abonnementen](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Administratie - Circuit eigenaren en circuit gebruikers

De 'Circuit Owner' is een geautoriseerde Power User van de ExpressRoute circuit resource. De circuiteigenaar kan autorisaties maken die kunnen worden ingewisseld door 'Circuit-gebruikers'. Circuit Gebruikers zijn eigenaren van virtuele netwerk gateways die niet binnen hetzelfde abonnement als de ExpressRoute circuit. Circuitgebruikers kunnen autorisaties inwisselen (één autorisatie per virtueel netwerk).

De circuiteigenaar heeft de bevoegdheid om autorisaties op elk gewenst moment te wijzigen en in te trekken. Wanneer een autorisatie wordt ingetrokken, worden alle koppelingsverbindingen verwijderd uit het abonnement waarvan de toegang is ingetrokken.

### <a name="circuit-owner-operations"></a>Circuit eigenaar operaties

**Een autorisatie maken**

De circuiteigenaar maakt een autorisatie, waarmee een autorisatiesleutel wordt gemaakt die door een circuitgebruiker kan worden gebruikt om hun virtuele netwerkgateways aan te sluiten op het ExpressRoute-circuit. Een autorisatie is geldig voor slechts één verbinding.

In het volgende voorbeeld ziet u hoe u een autorisatie maakt:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Het antwoord bevat de autorisatiesleutel en -status:

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

De eigenaar van het circuit kan alle autorisaties die op een bepaald circuit zijn uitgegeven, controleren door het volgende voorbeeld uit te voeren:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Autorisaties toevoegen**

De eigenaar van het circuit kan autorisaties toevoegen met behulp van het volgende voorbeeld:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Autorisaties verwijderen**

De eigenaar van het circuit kan autorisaties voor de gebruiker intrekken/verwijderen door het volgende voorbeeld uit te voeren:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Gebruikersbewerkingen van circuit

De Circuit-gebruiker heeft de peer-ID en een autorisatiesleutel van de circuiteigenaar nodig. De autorisatiesleutel is een GUID.

```powershell
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Een verbindingsautorisatie inwisselen**

De circuitgebruiker kan het volgende voorbeeld uitvoeren om een koppelingsautorisatie in te wisselen:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Een verbindingsautorisatie vrijgeven**

U een autorisatie vrijgeven door de verbinding te verwijderen die het ExpressRoute-circuit koppelt aan het virtuele netwerk.

## <a name="modify-a-virtual-network-connection"></a>Een virtuele netwerkverbinding wijzigen
U bepaalde eigenschappen van een virtuele netwerkverbinding bijwerken. 

**Het verbindingsgewicht bijwerken**

Uw virtuele netwerk kan worden aangesloten op meerdere ExpressRoute-circuits. U hetzelfde voorvoegsel van meer dan één ExpressRoute-circuit ontvangen. Als u wilt kiezen welke verbinding verkeer wilt verzenden dat is bestemd voor dit voorvoegsel, u *Routeweggewicht* van een verbinding wijzigen. Verkeer wordt verzonden op de verbinding met het hoogste *routegewicht.*

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Het bereik van *RoutingWeight* is 0 tot 32000. De standaardwaarde is 0.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute FastPath configureren 
U [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) inschakelen als uw ExpressRoute-circuit zich op [ExpressRoute Direct bevindt](expressroute-erdirect-about.md) en uw virtuele newtork-gateway Ultra Performance of ErGw3AZ is. FastPath verbetert de voorvorm van gegevenspaden, zoals pakketten per seconde en verbindingen per seconde tussen uw on-premises netwerk en uw virtuele netwerk. 

**FastPath configureren op een nieuwe verbinding**

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Een bestaande verbinding bijwerken om FastPath in te schakelen**

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```

## <a name="next-steps"></a>Volgende stappen

Zie de Veelgestelde vragen over [ExpressRoute voor](expressroute-faqs.md)meer informatie over ExpressRoute.
