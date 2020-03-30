---
title: 'Azure ExpressRoute: BFD configureren'
description: In dit artikel vindt u instructies over het configureren van BFD (Bidirectional Forwarding Detection) via private-peering van een ExpressRoute-circuit.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: rambala
ms.openlocfilehash: 378b639e89ffd46f6b32d7004f934104dd4b5407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064841"
---
# <a name="configure-bfd-over-expressroute"></a>BFD configureren via ExpressRoute

ExpressRoute ondersteunt Bidirectional Forwarding Detection (BFD) zowel via private als Microsoft peering. Door BFD in te schakelen via ExpressRoute, u de detectie van koppelingen door Microsoft Enterprise edge (MSEE) en de routers waarop u het ExpressRoute-circuit (CE/PE) beëindigt, versnellen. U ExpressRoute beëindigen via customer edge-routeringsapparaten of Partner Edge-routeringsapparaten (als u met beheerde Layer 3-verbindingsservice bent gegaan). Dit document leidt u door de behoefte aan BFD, en hoe BFD over ExpressRoute in te schakelen.

## <a name="need-for-bfd"></a>Behoefte aan BFD

Het volgende diagram toont het voordeel van het inschakelen van BFD via expressroutecircuit: [![1]][1 1]

U expressroute-circuit inschakelen via Layer 2-verbindingen of beheerde Layer 3-verbindingen. In beide gevallen, als er een of meer Layer-2-apparaten in het Verbindingspad ExpressRoute zijn, ligt de verantwoordelijkheid voor het detecteren van verbindingsfouten in het pad bij de bovenliggende BGP.

Op de MSEE-apparaten worden BGP keepalive en hold-time meestal geconfigureerd als respectievelijk 60 en 180 seconden. Daarom zou het na een koppelingsfout tot drie minuten duren voordat een koppelingsfout wordt gedetecteerd en het verkeer naar een alternatieve verbinding wordt overgeschakeld.

U de BGP-timers bedienen door lagere BGP-keepalive- en hold-time te configureren op het peering-apparaat van de klantrand. Als de BGP-timers niet overeenkomen tussen de twee peering-apparaten, gebruikt de BGP-sessie tussen de peers de lagere timerwaarde. De BGP keepalive kan worden ingesteld als laag als drie seconden, en de hold-time in de orde van tientallen seconden. Het agressief instellen van BGP-timers heeft echter minder de voorkeur omdat het protocol procesintensief is.

In dit scenario kan BFD helpen. BFD biedt detectie van low-overhead koppelingen in een subtweede tijdsinterval. 


## <a name="enabling-bfd"></a>BFD inschakelen

BFD is standaard geconfigureerd onder alle nieuw gecreëerde ExpressRoute private peering interfaces op de MEEs. Daarom moet u BFD configureren op uw CE's/PO's (zowel op uw primaire als secundaire apparaten om BFD in te schakelen). Bfd configureren is een proces in twee stappen: u moet de BFD op de interface configureren en deze koppelen aan de BGP-sessie.

Hieronder ziet u een voorbeeld ce/pe (met cisco IOS XE) configuratie. 

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

>[!NOTE]
>Bfd inschakelen onder een reeds bestaande private peering; je nodig hebt om het peering resetten. Zie [ExpressRoute-peerings opnieuw instellen][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD-timeronderhandeling

Tussen BFD-peers bepaalt de langzamere van de twee peers de transmissiesnelheid. BOF-transmissie-/ontvangstintervallen zijn ingesteld op 300 milliseconden. In bepaalde scenario's kan het interval worden ingesteld op een hogere waarde van 750 milliseconden. Door hogere waarden te configureren, u deze intervallen langer forceren. maar niet korter.

>[!NOTE]
>Als u Geo-redundante ExpressRoute-circuits hebt geconfigureerd of site-to-site IPSec VPN-connectiviteit als back-up gebruikt; BFD inschakelen zou helpen failover sneller na een ExpressRoute-verbindingsfout. 
>

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie of hulp naar de volgende links:

- [Een ExpressRoute-circuit maken en wijzigen][CreateCircuit]
- [Routering voor een ExpressRoute-circuit maken en wijzigen][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD versnelt link fout aftrek tijd"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






