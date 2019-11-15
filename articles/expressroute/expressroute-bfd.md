---
title: 'Azure-ExpressRoute: BFD configureren'
description: In dit artikel vindt u instructies over het configureren van BFD (in twee richtingen doorsturen detectie) via privé-peering van een ExpressRoute-circuit.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: rambala
ms.openlocfilehash: 608b5e0011d4ed656ff61fec84a23f2fb22373b3
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080802"
---
# <a name="configure-bfd-over-expressroute"></a>BFD via ExpressRoute configureren

ExpressRoute biedt ondersteuning voor bidirectionele forwarding-detectie (BFD) via persoonlijke en micro soft-peering. Door BFD in te scha kelen via ExpressRoute, kunt u de detectie van koppelings fouten tussen de micro soft Enter prise Edge (MSEE)-apparaten verkorten en de routers waarop u het ExpressRoute circuit (CE/PE) beëindigt. (Als u met beheerde laag-3-verbinding-service), kunt u ExpressRoute beëindigen via Routering klant-Edge-apparaten of routing Partner-Edge-apparaten. Dit document helpt u bij de noodzaak van BFD en BFD inschakelen via ExpressRoute.

## <a name="need-for-bfd"></a>Noodzaak van BFD

Het volgende diagram toont het voordeel van het inschakelen van BFD via ExpressRoute-circuit: [ ![1]][1]

U kunt ExpressRoute-circuit door een Layer 2-verbindingen of beheerde laag-3-verbindingen. In beide gevallen als er een of meer Layer-2-apparaten in het pad van ExpressRoute-verbinding, ligt de verantwoordelijkheid van de koppeling fouten opsporen in het pad met de bovenliggende BGP.

Op de apparaten MSEE worden keepalive-BGP en wacht-tijd doorgaans geconfigureerd als 60 en 180 seconden respectievelijk. Daarom na een fout in het tot duren zou drie minuten voor het detecteren van een mislukte koppeling verkeer naar en alternatieve verbinding.

U kunt de BGP-timers beheren door het configureren van lagere BGP-keepalive en wacht-tijd op het apparaat van klant-edge-peering. Als de BGP-timers niet tussen de twee peering apparaten overeen komen, gebruikt de BGP-sessie tussen de peers. de laagste timerwaarde. De BGP-keepalive kan slechts drie seconden, en de wachtstand-tijd in volgorde van tientallen seconden worden ingesteld. Het instellen van BGP-timers is echter minder wenselijk, omdat het protocol proces intensief is.

In dit scenario kunt BFD. BFD biedt weinig overhead koppeling foutdetectie in een tijdsinterval subsecond. 


## <a name="enabling-bfd"></a>BFD inschakelen

BFD is onder alle de zojuist gemaakte ExpressRoute persoonlijke peering interfaces op de msee's standaard geconfigureerd. Om BFD in te scha kelen, moet u daarom BFD op uw CEs/PEs (op uw primaire en secundaire apparaten) configureren. Het configureren van BFD is een proces dat uit twee stappen bestaat: u moet de BFD op de interface configureren en vervolgens koppelen aan de BGP-sessie.

Hieronder ziet u een voor beeld van een CE/PE-configuratie (met Cisco IOS XE). 

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
>Om in te schakelen BFD onder een bestaande persoonlijke peering; u moet de peering instellen. Zie [ExpressRoute-peerings opnieuw instellen][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD Timer-onderhandeling

Tussen BFD peers bepalen de tragere van de twee computers de verzending van fouten. Msee's BFD verzenden/ontvangen intervallen zijn ingesteld op 300 milliseconden. In bepaalde scenario's, kan het interval worden ingesteld op een hogere waarde van 750 milliseconden. Door het configureren van hogere waarden, kunt u afdwingen dat deze intervallen moet meer zijn; maar, geen korter.

>[!NOTE]
>Als u geografisch redundante ExpressRoute-circuits hebt geconfigureerd of VPN-verbinding tussen sites als back-up gebruikt. door BFD in te scha kelen, kunt u de failover sneller volgen door een ExpressRoute-verbindings fout. 
>

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende koppelingen voor meer informatie en hulp:

- [Een ExpressRoute-circuit maken en wijzigen][CreateCircuit]
- [Route ring voor een ExpressRoute-circuit maken en wijzigen][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD versnelt tijd aftrek van koppeling"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






