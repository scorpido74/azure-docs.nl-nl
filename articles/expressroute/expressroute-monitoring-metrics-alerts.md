---
title: 'Azure-ExpressRoute: bewaking, metrische gegevens en waarschuwingen'
description: Deze pagina bevat informatie over het controleren van ExpressRoute
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 95083809f8fcfe42ae76faa0af507c7ce4acd709
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083435"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Bewaking, metrische gegevens en waarschuwingen voor ExpressRoute

Dit artikel helpt u inzicht in de ExpressRoute-bewaking, metrische gegevens en waarschuwingen met behulp van Azure Monitor. Azure Monitor is een centrale locatie voor alle metrische gegevens, waarschuwingen, diagnostische logboeken in heel Azure.
 
>[!NOTE]
>Met behulp van **klassieke metrische gegevens** wordt niet aanbevolen.
>

## <a name="expressroute-metrics"></a>Metrische gegevens van ExpressRoute

Als u **gegevens**wilt weer geven, gaat u naar de pagina *Azure monitor* en klikt u op *metrische gegevens*. Voor het weer geven van **ExpressRoute** -metrische gegevens, de bestands extensie per bron type *ExpressRoute-circuits*. Als u **Global Reach** metrische gegevens wilt weer geven, filtert u op resource type *ExpressRoute-circuits* en selecteert u een ExpressRoute-circuit resource waarvoor Global REACH is ingeschakeld. Als u **ExpressRoute direct** -metrische gegevens wilt weer geven, filtert u resource type op *ExpressRoute-poorten*. 

Zodra een metriek is geselecteerd, wordt de standaard aggregatie toegepast. U kunt eventueel splitsen Toep assen, waardoor de metriek wordt weer gegeven met verschillende dimensies.

### <a name="available-metrics"></a>Beschik bare metrische gegevens
|**Gegevens**|**Categorie**|**Dimensie (s)**|**Functie (s)**|
| --- | --- | --- | --- |
|ARP-Beschik baarheid|Beschikbaarheid|<ui><li>Peer (primaire/secundaire ExpressRoute-router)</ui></li><ui><li> Peering-type (privé/openbaar/micro soft)</ui></li>|ExpressRoute|
|BGP-Beschik baarheid|Beschikbaarheid|<ui><li> Peer (primaire/secundaire ExpressRoute-router)</ui></li><ui><li> Peering-type</ui></li>|ExpressRoute|
|BitsInPerSecond|Verkeer|<ui><li> Peering-type (ExpressRoute)</ui></li><ui><li>Koppeling (ExpressRoute direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Verkeer| <ui><li>Peering-type (ExpressRoute)</ui></li><ui><li> Koppeling (ExpressRoute direct) | <ui><li>ExpressRoute<ui><li>ExpressRoute direct</ui></li> |
|GlobalReachBitsInPerSecond|Verkeer|<ui><li>Skey (Service sleutel) gepeerd circuit</ui></li>|Globaal bereik|
|GlobalReachBitsOutPerSecond|Verkeer|<ui><li>Skey (Service sleutel) gepeerd circuit</ui></li>|Globaal bereik|
|AdminState|Fysieke connectiviteit|Koppeling|ExpressRoute Direct|
|LineProtocol|Fysieke connectiviteit|Koppeling|ExpressRoute Direct|
|RxLightLevel|Fysieke connectiviteit|<ui><li></ui> koppelen</li><ui><li>Lane-</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fysieke connectiviteit|<ui><li></ui> koppelen</li><ui><li>Lane-</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Het gebruik van *GlobalGlobalReachBitsInPerSecond* en *GlobalGlobalReachBitsOutPerSecond* wordt alleen weer gegeven als er ten minste één Global Reach verbinding tot stand is gebracht.
>

## <a name="circuits-metrics"></a>Meet gegevens circuits

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits in en uit-metrische gegevens voor alle peerings

U kunt metrische gegevens weer geven voor alle peerings op een bepaald ExpressRoute-circuit.

![Circuit metrische gegevens](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Bits in en out-metrische gegevens per peering

Hier vindt u metrische gegevens voor persoonlijke, openbare en Microsoft-peering in bits per seconde.

![Metrische gegevens per peering](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>BGP-Beschik baarheid-splitsen op peer  

U kunt bijna de real-time Beschik baarheid van BGP over Peerings en peers (primaire en secundaire ExpressRoute-routers) bekijken. Dit dash board toont de primaire BGP-sessie voor privé-peering en de tweede BGP-sessie voor persoonlijke peering. 

![BGP-Beschik baarheid per peer](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>Beschik baarheid van ARP-splitsen door peering  

U kunt bijna de real-time Beschik baarheid van [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) over Peerings en peers (primaire en secundaire ExpressRoute-routers) bekijken. Dit dash board toont de sessie van de privé-peering-ARP voor beide peers, maar is voltooid voor micro soft-peering tussen peerings. De standaard aggregatie (Average) is gebruikt voor beide peers.  

![ARP-Beschik baarheid per peer](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>Directe metrische gegevens voor ExpressRoute

### <a name="admin-state---split-by-link"></a>Beheer status-splitsen op koppeling
U kunt de status van de beheerder voor elke koppeling van het directe poort paar ExpressRoute bekijken.

![directe beheerders status](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Bits per seconde-splitsen op koppeling
U kunt de bits per seconde weer geven voor beide koppelingen van het directe poort paar ExpressRoute. 

![er direct-bits per seconde](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Aantal bits per seconde-splitsen op koppeling
U kunt ook de bits per seconde weer geven over beide koppelingen van het directe poort paar ExpressRoute. 

![Er is geen directe-bits per seconde](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Lijn protocol-splitsen op koppeling
U kunt het regel protocol weer geven op elke koppeling van het directe poort paar ExpressRoute.

![Er is geen direct line-protocol](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>RX licht niveau-splitsen op koppeling
U kunt het RX-licht niveau (het licht niveau dat de ExpressRoute directe poort **ontvangt**) weer geven voor elke poort. Gezonde RX Light-niveaus vallen doorgaans binnen een bereik van-10 tot 0 dBm

![Lampje directe lijn RX licht niveau](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>TX licht niveau-splitsen op koppeling
U kunt het TX licht niveau (het licht niveau dat de ExpressRoute directe poort **verzendt**) weer geven voor elke poort. Gezonde TX Light-niveaus vallen doorgaans binnen een bereik van-10 tot 0 dBm

![Lampje directe lijn RX licht niveau](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute-gateway-verbindingen in bits/seconden

![gateway-verbindingen](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>Waarschuwingen voor ExpressRoute-gateway-verbindingen

1. Als u wilt configureren van waarschuwingen, gaat u naar **Azure Monitor**, klikt u vervolgens op **waarschuwingen**.

   ![waarschuwingen](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Klik op **+ doel selecteren** en selecteer de verbindingsresource van de ExpressRoute-gateway.

   ![doel]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Definieer de details van de waarschuwing.

   ![Actiegroep](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Definiëren en toevoegen van de actiegroep.

   ![actiegroep toevoegen](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Waarschuwingen op basis van elke peering

 ![Wat](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Waarschuwingen voor activiteitenlogboeken voor circuits configureren

In de **waarschuwingscriteria**, kunt u **activiteitenlogboek** signaaltype en selecteer het signaal.

  ![een andere](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Volgende stappen

Configureer uw ExpressRoute-verbinding.
  
  * [Een circuit maken en wijzigen](expressroute-howto-circuit-arm.md)
  * [Een peeringconfiguratie maken en wijzigen](expressroute-howto-routing-arm.md)
  * [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
