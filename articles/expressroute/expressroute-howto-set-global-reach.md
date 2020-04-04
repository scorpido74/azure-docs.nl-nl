---
title: 'Azure ExpressRoute: Globaal bereik configureren'
description: Met dit artikel u ExpressRoute-circuits koppelen om een privénetwerk te maken tussen uw on-premises netwerken en Global Reach mogelijk te maken.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.openlocfilehash: 587a17659a412d6f894faf5a744a7d9c444935c8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656751"
---
# <a name="configure-expressroute-global-reach"></a>ExpressRoute Global Reach configureren

Met dit artikel u ExpressRoute Global Reach configureren met PowerShell. Zie [ExpressRouteRoute Global Reach](expressroute-global-reach.md)voor meer informatie.

 ## <a name="before-you-begin"></a>Voordat u begint

Bevestig het volgende voordat u de configuratie start:

* U begrijpt ExpressRoute-circuitinrichtingsworkflows. [workflows](expressroute-workflows.md)
* Uw ExpressRoute-circuits zijn in een ingerichte staat.
* Azure private peering is geconfigureerd op uw ExpressRoute-circuits.
* Als u PowerShell lokaal wilt uitvoeren, controleert u of de nieuwste versie van Azure PowerShell op uw computer is geïnstalleerd.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Circuits identificeren

1. Als u de configuratie wilt starten, meldt u zich aan bij uw Azure-account en selecteert u het abonnement dat u wilt gebruiken.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Identificeer de ExpressRoute-circuits die u wilt gebruiken. U ExpressRoute Global Reach inschakelen tussen het privé-peering van twee ExpressRoute-circuits, zolang ze zich in de ondersteunde landen/regio's bevinden en op verschillende peeringlocaties zijn gemaakt. 

   * Als uw abonnement eigenaar is van beide circuits, u kiezen voor een van beide circuits om de configuratie in de volgende secties uit te voeren.
   * Als de twee circuits zich in verschillende Azure-abonnementen bevinden, hebt u autorisatie nodig van één Azure-abonnement. Vervolgens geeft u de autorisatiesleutel door wanneer u de configuratieopdracht uitvoert in het andere Azure-abonnement.

## <a name="enable-connectivity"></a>Connectiviteit inschakelen

Schakel connectiviteit tussen uw on-premises netwerken in. Er zijn afzonderlijke sets instructies voor circuits die zich in hetzelfde Azure-abonnement bevinden en circuits die verschillende abonnementen zijn.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>ExpressRoute-circuits in hetzelfde Azure-abonnement

1. Gebruik de volgende opdrachten om circuit 1 en circuit 2 te krijgen. De twee circuits zijn in hetzelfde abonnement.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Voer het volgende commando uit tegen circuit 1 en passeer de privé-peering-ID van circuit 2. Noteer bij het uitvoeren van de opdracht het volgende:

   * De privé-peering-id lijkt op het volgende voorbeeld: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* moet een /29 IPv4-subnet zijn, bijvoorbeeld "10.0.0.0/29". We gebruiken IP-adressen in dit subnet om de connectiviteit tussen de twee ExpressRoute-circuits tot stand te brengen. U mag de adressen in dit subnet niet gebruiken in uw virtuele Azure-netwerken of in uw on-premises netwerk.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Sla de configuratie op circuit 1 als volgt op:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Wanneer de vorige bewerking is voltooid, hebt u verbinding tussen uw on-premises netwerken aan beide zijden via uw twee ExpressRoute-circuits.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>ExpressRoute-circuits in verschillende Azure-abonnementen

Als de twee circuits niet in hetzelfde Azure-abonnement zitten, hebt u autorisatie nodig. In de volgende configuratie wordt de autorisatie gegenereerd in het circuit 2-abonnement en wordt de autorisatiesleutel doorgegeven aan circuit 1.

1. Een autorisatiesleutel genereren.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Noteer de privé-peering-ID van circuit 2 en de autorisatiesleutel.
2. Voer de volgende opdracht uit tegen circuit 1. Geef de privé-peering-ID van circuit 2 en de autorisatiesleutel door.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Sla de configuratie op circuit 1 op.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Wanneer de vorige bewerking is voltooid, hebt u verbinding tussen uw on-premises netwerken aan beide zijden via uw twee ExpressRoute-circuits.

## <a name="verify-the-configuration"></a>De configuratie controleren

Gebruik de volgende opdracht om de configuratie te verifiëren op het circuit waar de configuratie is gemaakt (bijvoorbeeld circuit 1 in het vorige voorbeeld).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Als u gewoon *$ckt1* in PowerShell uitvoert, ziet u *CircuitConnectionStatus* in de uitvoer. Het vertelt u of de connectiviteit is vastgesteld, "Verbonden", of "Losgekoppeld". 

## <a name="disable-connectivity"></a>Connectiviteit uitschakelen

Als u de verbinding tussen uw on-premises netwerken wilt uitschakelen, voert u de opdrachten uit tegen het circuit waar de configuratie is gemaakt (bijvoorbeeld circuit 1 in het vorige voorbeeld).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

U de bewerking Oppakken uitvoeren om de status te verifiëren.

Nadat de vorige bewerking is voltooid, beschikt u niet meer over connectiviteit tussen uw on-premises netwerk via uw ExpressRoute-circuits.

## <a name="next-steps"></a>Volgende stappen
1. [Meer informatie over ExpressRoute Global Reach](expressroute-global-reach.md)
2. [ExpressRoute-connectiviteit verifiëren](expressroute-troubleshooting-expressroute-overview.md)
3. [Een ExpressRoute-circuit koppelen aan een virtueel Azure-netwerk](expressroute-howto-linkvnet-arm.md)
