---
title: 'Azure-ExpressRoute: Global Reach configureren'
description: Dit artikel helpt u koppelen van ExpressRoute-circuits samen om te maken van een particulier netwerk tussen uw on-premises netwerken en globale bereiken inschakelen.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.openlocfilehash: 76de7a8854a58deb924cbbe3177ad5a7b5fd57a2
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083471"
---
# <a name="configure-expressroute-global-reach"></a>ExpressRoute Global Reach configureren

Dit artikel helpt u bij het configureren van ExpressRoute globale bereiken met behulp van PowerShell. Zie voor meer informatie, [ExpressRouteRoute globaal bereik](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Voordat u begint

Bevestig het volgende voordat u begint met de configuratie:

* U begrijpt [werk stromen](expressroute-workflows.md)voor het inrichten van ExpressRoute-circuits.
* Uw ExpressRoute-circuits bevinden zich in een Provisioning-status.
* Persoonlijke Azure-peering is geconfigureerd op uw ExpressRoute-circuits.
* Als u Power shell lokaal wilt uitvoeren, controleert u of de meest recente versie van Azure PowerShell op uw computer is geïnstalleerd.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Circuits identificeren

1. Als u de configuratie wilt starten, meldt u zich aan bij uw Azure-account en selecteert u het abonnement dat u wilt gebruiken.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Identificeer de ExpressRoute-circuits die u wilt gebruiken. U kunt ExpressRoute Global Reach tussen twee ExpressRoute-circuits inschakelen, zolang ze zich in de ondersteunde landen/regio's bevinden en op verschillende peering locaties zijn gemaakt. 

   * Als uw abonnement eigenaar is van beide circuits, kunt u een van beide circuits kiezen om de configuratie uit te voeren in de volgende secties.
   * Als de twee circuits zich in verschillende Azure-abonnementen bevinden, hebt u autorisatie nodig van een Azure-abonnement. Vervolgens geeft u de autorisatie sleutel door wanneer u de configuratie opdracht uitvoert in het andere Azure-abonnement.

## <a name="enable-connectivity"></a>Connectiviteit inschakelen

Connectiviteit tussen uw on-premises netwerken inschakelen. Er zijn afzonderlijke sets instructies voor circuits die zich in hetzelfde Azure-abonnement bevinden en circuits die verschillende abonnementen zijn.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>ExpressRoute-circuits in hetzelfde Azure-abonnement

1. Gebruik de volgende opdrachten om op te halen van circuit 1 en 2-circuit. De twee circuits zich in hetzelfde abonnement.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Voer de volgende opdracht uit op Circuit 1 en geef de privé-peering-ID van circuit 2 door. Wanneer u de opdracht uitvoert, moet u rekening houden met het volgende:

   * De ID van de privé-peering ziet er ongeveer uit als in het volgende voor beeld: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* moet een/29 IPv4-subnet zijn, bijvoorbeeld "10.0.0.0/29". We gebruiken IP-adressen in dit subnet om verbinding te maken tussen de twee ExpressRoute-circuits. Gebruik niet de adressen in dit subnet in uw virtuele Azure-netwerken of in uw on-premises netwerk.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Sla de configuratie op Circuit 1 als volgt op:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Wanneer de vorige bewerking is voltooid, hebt u verbinding tussen uw on-premises netwerken aan beide zijden via uw twee ExpressRoute-circuits.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>ExpressRoute-circuits in verschillende Azure-abonnementen

Als de twee circuits zich niet in hetzelfde Azure-abonnement bevinden, hebt u autorisatie nodig. In de volgende configuratie wordt autorisatie gegenereerd in het circuit 2-abonnement en wordt de autorisatie sleutel door gegeven aan Circuit 1.

1. Genereer een autorisatiesleutel.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Noteer de privé-peering-ID van circuit 2, evenals de autorisatie sleutel.
2. Voer de volgende opdracht op basis van circuit 1. Geef een persoonlijke peering-ID van circuit 2 en de autorisatie sleutel door.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Sla de configuratie op Circuit 1 op.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Wanneer de vorige bewerking is voltooid, hebt u verbinding tussen uw on-premises netwerken aan beide zijden via uw twee ExpressRoute-circuits.

## <a name="verify-the-configuration"></a>De configuratie controleren

Gebruik de volgende opdracht om de configuratie te controleren van het circuit waarin de configuratie is gemaakt (bijvoorbeeld Circuit 1 in het vorige voor beeld).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Als u alleen *$CKT 1* in Power shell uitvoert, ziet u *CircuitConnectionStatus* in de uitvoer. Hiermee wordt aangegeven of de verbinding tot stand is gebracht, verbonden of verbroken. 

## <a name="disable-connectivity"></a>Connectiviteit uitschakelen

Als u de verbinding tussen uw on-premises netwerken wilt uitschakelen, voert u de opdrachten uit op het circuit waarin de configuratie is gemaakt (bijvoorbeeld Circuit 1 in het vorige voor beeld).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

U kunt de Get-bewerking om te controleren of de status uitvoeren.

Nadat de vorige bewerking is voltooid, hebt u niet langer de connectiviteit tussen uw on-premises netwerk via uw ExpressRoute-circuits.

## <a name="next-steps"></a>Volgende stappen
1. [Meer informatie over ExpressRoute globaal bereik](expressroute-global-reach.md)
2. [Controleer de ExpressRoute-verbinding](expressroute-troubleshooting-expressroute-overview.md)
3. [Een ExpressRoute-circuit koppelen aan een virtueel Azure-netwerk](expressroute-howto-linkvnet-arm.md)
