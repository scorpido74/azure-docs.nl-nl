---
title: 'Azure-ExpressRoute: BFD configureren'
description: In dit artikel vindt u instructies voor het configureren van BFD (bidirectionele doorstuur detectie) via particuliere peering van een ExpressRoute-circuit.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: rambala
ms.openlocfilehash: ec9c6248f4054329bd3cd9b74855964c4acf72c4
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85855152"
---
# <a name="configure-bfd-over-expressroute"></a>BFD configureren via ExpressRoute

ExpressRoute biedt ondersteuning voor bidirectionele forwarding-detectie (BFD) via persoonlijke en micro soft-peering. Door BFD in te scha kelen via ExpressRoute, kunt u de detectie van koppelings fouten tussen de micro soft Enter prise Edge (MSEE)-apparaten verkorten en de routers waarop u het ExpressRoute circuit (CE/PE) beëindigt. U kunt ExpressRoute beëindigen via routerings apparaten van klanten of partner Edge-routerings apparaten (als u met Managed Layer 3 Connection service hebt gewerkt). In dit document wordt uitgelegd hoe u BFD nodig hebt en hoe u BFD inschakelt via ExpressRoute.

## <a name="need-for-bfd"></a>Benodigde BFD

In het volgende diagram ziet u het voor deel van het inschakelen van BFD over ExpressRoute-circuit: [![1]][1]

U kunt het ExpressRoute-circuit inschakelen via laag-2-verbindingen of beheerde laag-3-verbindingen. Als er sprake is van een of meer laag-2-apparaten in het pad van de ExpressRoute-verbinding, is de verantwoordelijkheid van het detecteren van eventuele koppelings fouten in het pad ook de bovenliggende BGP.

Op de MSEE-apparaten worden BGP keepalive en hold-time doorgaans geconfigureerd als 60 en 180 seconden. Als gevolg van het mislukken van een koppeling kan het drie minuten duren voordat een koppelings fout is gedetecteerd en verkeer naar een andere verbinding wordt geswitcheerd.

U kunt de BGP-timers beheren door lagere BGP-keepalive en-wacht tijd op het peering-apparaat van de klant te configureren. Als de BGP-timers niet overeenkomen tussen de twee peering-apparaten, zou de BGP-sessie tussen de peers de lagere timer waarde gebruiken. De BGP-keepalive kan worden ingesteld op Maxi maal drie seconden en de wacht tijd in de volg orde van tien seconden. Het instellen van BGP-timers is echter minder wenselijk, omdat het protocol proces intensief is.

In dit scenario kan BFD u helpen. BFD biedt de detectie van een lage overhead van een koppeling in een subseconde tijd interval. 


## <a name="enabling-bfd"></a>BFD inschakelen

BFD wordt standaard geconfigureerd onder alle zojuist gemaakte ExpressRoute-particuliere peering-interfaces op de Msee's. Om BFD in te scha kelen, moet u daarom BFD op uw CEs/PEs (op uw primaire en secundaire apparaten) configureren. Het configureren van BFD is een proces dat uit twee stappen bestaat: u moet de BFD op de interface configureren en vervolgens koppelen aan de BGP-sessie.

Hieronder ziet u een voor beeld van een CE/PE-configuratie (met Cisco IOS XE). 

```console
interface TenGigabitEthernet2/0/0.150
   description private peering to Azure
   encapsulation dot1Q 15 second-dot1q 150
   ip vrf forwarding 15
   ip address 192.168.15.17 255.255.255.252
   bfd interval 300 min_rx 300 multiplier 3


router bgp 65020
   address-family ipv4 vrf 15
      network 10.1.15.0 mask 255.255.255.128
      neighbor 192.168.15.18 remote-as 12076
      neighbor 192.168.15.18 fall-over bfd
      neighbor 192.168.15.18 activate
      neighbor 192.168.15.18 soft-reconfiguration inbound
   exit-address-family
```

>[!NOTE]
>BFD inschakelen onder een al bestaande persoonlijke peering; u moet de peering opnieuw instellen. Zie [ExpressRoute-peerings opnieuw instellen][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD timer-onderhandeling

Tussen BFD-peers wordt de verzend frequentie bepaald door de langzamere van de twee peers. Msee's BFD-verzen ding/ontvangst intervallen worden ingesteld op 300 milliseconden. In bepaalde scenario's kan het interval worden ingesteld op een hogere waarde van 750 milliseconden. Door hogere waarden te configureren, kunt u deze intervallen langer laten afdwingen. maar niet korter.

>[!NOTE]
>Als u geografisch redundante ExpressRoute-circuits hebt geconfigureerd of VPN-verbinding tussen sites als back-up gebruikt. door BFD in te scha kelen, kunt u de failover sneller volgen door een ExpressRoute-verbindings fout. 
>

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende koppelingen voor meer informatie of hulp:

- [Een ExpressRoute-circuit maken en wijzigen][CreateCircuit]
- [Routering voor een ExpressRoute-circuit maken en wijzigen][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD versnelt de tijd" voor het ongedaan maken van de koppelings fout

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






