---
title: 'Azure ExpressRoute: monitoring, statistieken en waarschuwingen'
description: Op deze pagina vindt u informatie over het monitoren van ExpressRoute
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436916"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Bewaking, metrische gegevens en waarschuwingen voor ExpressRoute

In dit artikel u informatie krijgen over de controle, statistieken en waarschuwingen van ExpressRoute met Azure Monitor. Azure Monitor is one stop shop voor alle metrics, waarschuwingen, diagnostische logboeken in heel Azure.
 
>[!NOTE]
>Het gebruik **van klassieke statistieken** wordt niet aanbevolen.
>

## <a name="expressroute-metrics"></a>ExpressRoute-statistieken

Als u **statistieken wilt weergeven,** navigeert u naar de *azure-monitorpagina* en klikt u op *Metrische gegevens*. Als u **ExpressRoute-statistieken** wilt weergeven, filtert u op circuits van Resourcetype *ExpressRoute*. Als u **Global Reach-statistieken wilt** weergeven, filtert u op *expressroute-circuits* van resourcetype en selecteert u een ExpressRoute-circuitbron die Global Reach heeft ingeschakeld. Als u **ExpressRoute Direct-statistieken wilt** weergeven, filtert u Resourcetype op *ExpressRoute-poorten*. 

Zodra een statistiek is geselecteerd, wordt de standaardaggregatie toegepast. Optioneel u splitsen toepassen, waardoor de statistiek met verschillende afmetingen wordt weergegeven.

### <a name="available-metrics"></a>Beschikbare statistieken
|**Gegevens**|**Categorie**|**Dimensie(en)**|**Functie(en)**|
| --- | --- | --- | --- |
|ARP-beschikbaarheid|Beschikbaarheid|<ui><li>Peer (primaire/secundaire ExpressRoute-router)</ui></li><ui><li> Peeringtype (privé/openbaar/Microsoft)</ui></li>|ExpressRoute|
|Beschikbaarheid van Bgp|Beschikbaarheid|<ui><li> Peer (primaire/secundaire ExpressRoute-router)</ui></li><ui><li> Peeringtype</ui></li>|ExpressRoute|
|BitsinperSeconde|Verkeer|<ui><li> Peering Type (ExpressRoute)</ui></li><ui><li>Link (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutperSeconde|Verkeer| <ui><li>Peering Type (ExpressRoute)</ui></li><ui><li> Link (ExpressRoute Direct) | <ui><li>ExpressRoute ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|GlobalReachBitsInPerSeconde|Verkeer|<ui><li>Peered Circuit Skey (servicesleutel)</ui></li>|Globaal bereik|
|GlobalReachBitsOutPerSeconde|Verkeer|<ui><li>Peered Circuit Skey (servicesleutel)</ui></li>|Globaal bereik|
|AdminState|Fysieke connectiviteit|Koppeling|ExpressRoute Direct|
|LineProtocol|Fysieke connectiviteit|Koppeling|ExpressRoute Direct|
|RxLightLevel RxLightLevel|Fysieke connectiviteit|<ui><li>Koppeling</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|TxLightLevel TxLightLevel|Fysieke connectiviteit|<ui><li>Koppeling</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Het gebruik van *GlobalGlobalReachBitsInPerSeconde* en *GlobalGlobalReachBitsOutPerSecond* is alleen zichtbaar als er ten minste één Global Reach-verbinding tot stand komt.
>

## <a name="circuits-metrics"></a>Statistieken over circuits

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits In en Out - Statistieken voor alle peerings

U statistieken bekijken voor alle peerings op een bepaald ExpressRoute-circuit.

![circuitstatistieken](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Bits In en Out - Statistieken per peering

U statistieken voor privé-, openbare en Microsoft-peering in bits/seconde bekijken.

![metrics per peering](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>BGP-beschikbaarheid - Splitsen per peer  

U bijna realtime beschikbaarheid van BGP bekijken voor peerings en peers (Primary and Secondary ExpressRoute routers). Dit dashboard toont de Primaire BGP-sessie voor privé-peering en de tweede BGP-sessie naar beneden voor privé-peering. 

![BGP-beschikbaarheid per peer](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>ARP-beschikbaarheid - Splitsen door peering  

U bijna realtime de beschikbaarheid van [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) bekijken voor peerings en peers (Primary and Secondary ExpressRoute routers). Dit dashboard toont de ARP-privé-peering-sessie voor beide peers, maar compleet voor Microsoft-peering in peerings. De standaardaggregatie (Gemiddelde) is voor beide peers gebruikt.  

![ARP-beschikbaarheid per peer](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct Metrics

### <a name="admin-state---split-by-link"></a>Beheerdersstatus - Splitsen op koppeling
U de beheerdersstatus voor elke koppeling van het ExpressRoute Direct-poortpaar bekijken.

![er directe admin staat](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Bits in per seconde - Gesplitst op koppeling
U de bits per seconde bekijken via beide koppelingen van het ExpressRoute Direct-poortpaar. 

![er directe bits in per seconde](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Bits Out per seconde - Gesplitst op koppeling
U de bits ook per seconde bekijken via beide koppelingen van het ExpressRoute Direct-poortpaar. 

![er directe bits uit per seconde](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Lijnprotocol - Splitsen op koppeling
U het lijnprotocol bekijken op elke link van het ExpressRoute Direct-poortpaar.

![er direct line protocol](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Rx Light Level - Split per link
U het Rx-lichtniveau (het lichtniveau dat de ExpressRoute Direct-poort **ontvangt)** voor elke poort bekijken. Gezonde Rx lichtniveaus vallen over het algemeen binnen een bereik van -10 tot 0 dBm

![er directe lijn Rx Light Level](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Tx Lichtniveau - Splitsen per link
U het Tx-lichtniveau (het lichtniveau dat de ExpressRoute Direct-poort **uitzendt)** voor elke poort bekijken. Gezonde Tx lichtniveaus vallen over het algemeen binnen een bereik van -10 tot 0 dBm

![er directe lijn Rx Light Level](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute-gatewayverbindingen in bits/seconden

![gatewayverbindingen](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>Waarschuwingen voor ExpressRoute-gatewayverbindingen

1. Als u waarschuwingen wilt configureren, navigeert u naar **Azure Monitor**en klikt u op **Waarschuwingen**.

   ![waarschuwingen](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Klik **op +Selecteer Doel** en selecteer de verbindingsbron van de ExpressRoute-gateway.

   ![Doel]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Definieer de waarschuwingsgegevens.

   ![actiegroep](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Definieer en voeg de actiegroep toe.

   ![actiegroep toevoegen](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Waarschuwingen op basis van elke peering

 ![Wat](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Waarschuwingen configureren voor activiteitslogboeken op circuits

In de **waarschuwingscriteria**u **activiteitslogboek** selecteren voor het signaaltype en het signaal selecteren.

  ![Andere](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Volgende stappen

Configureer uw ExpressRoute-verbinding.
  
  * [Een circuit maken en wijzigen](expressroute-howto-circuit-arm.md)
  * [Een peeringconfiguratie maken en wijzigen](expressroute-howto-routing-arm.md)
  * [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
