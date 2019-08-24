---
title: 'Controleren, metrische gegevens en waarschuwingen: Azure ExpressRoute | Microsoft Docs'
description: Deze pagina bevat informatie over het controleren van ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 92ec03e20fb6e681a0afd14048449ad004ebca0c
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991503"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Bewaking, metrische gegevens en waarschuwingen voor ExpressRoute

Dit artikel helpt u inzicht in de ExpressRoute-bewaking, metrische gegevens en waarschuwingen met behulp van Azure Monitor. Azure Monitor is een centrale locatie voor alle metrische gegevens, waarschuwingen, diagnostische logboeken in heel Azure.
 
>[!NOTE]
>Met behulp van **klassieke metrische gegevens** wordt niet aanbevolen.
>

## <a name="circuit-metrics"></a>Circuit metrische gegevens

Om te navigeren naar **metrische gegevens**, klikt u op het ExpressRoute-pagina voor het circuit die u wilt bewaken. Onder **bewaking**, vindt u de **metrische gegevens**. Selecteer een van de onderstaande metrische gegevens. De standaard aggregatie wordt toegepast. U kunt eventueel splitsen Toep assen, waardoor de metrische gegevens met verschillende dimensies worden weer gegeven.

### <a name="metrics-available"></a>Beschik bare metrische gegevens: 
* **Beschikbaarheid** 
    * ARP-Beschik baarheid
      * Beschik bare dimensies:
        * Peer (primaire/secundaire ExpressRoute-router)
        * Peering-type (privé/openbaar/micro soft)
    * BGP-Beschik baarheid
      * Beschik bare dimensies:
        * Peer (primaire/secundaire ExpressRoute-router)
        * Peering-type (privé/openbaar/micro soft)
* **Vervoer**
    * BitsInPerSecond
      * Beschik bare dimensies:
        * Peering-type (privé/openbaar/micro soft)
    * BitsOutPerSecond
      * Beschik bare dimensies:
        * Peering-type (privé/openbaar/micro soft)
    * GlobalReachBitsInPerSecond
      * Beschik bare dimensies:
        * Skey van gekoppeld circuit (Service sleutel)
    * GlobalReachBitsOutPerSecond
      * Beschik bare dimensies:
        * Skey van gekoppeld circuit (Service sleutel)

>[!NOTE]
>Het gebruik van *GlobalGlobalReachBitsInPerSecond* en *GlobalGlobalReachBitsOutPerSecond* wordt alleen weer gegeven als er ten minste één Global Reach verbinding tot stand is gebracht.
>

## <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits in en uit-metrische gegevens voor alle peerings

U kunt metrische gegevens weer geven voor alle peerings op een bepaald ExpressRoute-circuit.

![Circuit metrische gegevens](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="bits-in-and-out---metrics-per-peering"></a>Bits in en out-metrische gegevens per peering

Hier vindt u metrische gegevens voor persoonlijke, openbare en Microsoft-peering in bits per seconde.

![Metrische gegevens per peering](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="bgp-availability---split-by-peer"></a>BGP-Beschik baarheid-splitsen op peer  

U kunt bijna de real-time Beschik baarheid van BGP over Peerings en peers (primaire en secundaire ExpressRoute-routers) bekijken. Dit dash board toont de primaire BGP-sessie voor privé-peering en de tweede BGP-sessie voor persoonlijke peering. 

![BGP-Beschik baarheid per peer](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

## <a name="arp-availability---split-by-peering"></a>Beschik baarheid van ARP-splitsen door peering  

U kunt bijna de real-time Beschik baarheid van [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) over Peerings en peers (primaire en secundaire ExpressRoute-routers) bekijken. Dit dash board toont de sessie van de privé-peering-ARP voor beide peers, maar is voltooid voor micro soft-peering tussen peerings. De standaard aggregatie (Average) is gebruikt voor beide peers.  

![ARP-Beschik baarheid per peer](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

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
