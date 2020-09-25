---
title: 'Azure-ExpressRoute: bewaking, metrische gegevens en waarschuwingen'
description: Meer informatie over Azure ExpressRoute-bewaking, metrische gegevens en waarschuwingen met behulp van Azure Monitor, de ene stop-shop voor alle metrische gegevens, waarschuwingen en Diagnostische logboeken in Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 08/25/2020
ms.author: duau
ms.openlocfilehash: 6f502b8ad8ac268cc937150f4effdf9edf8eef15
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91252626"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Bewaking, metrische gegevens en waarschuwingen voor ExpressRoute

Dit artikel helpt u bij het begrijpen van ExpressRoute bewaking, metrische gegevens en waarschuwingen met behulp van Azure Monitor. Azure Monitor is één stop shop voor alle metrische gegevens, waarschuwingen en Diagnostische logboeken over Azure.
 
>[!NOTE]
>Het gebruik van **klassieke metrische gegevens** wordt niet aanbevolen.
>

## <a name="expressroute-metrics"></a>Metrische gegevens van ExpressRoute

Als u **gegevens**wilt weer geven, gaat u naar de pagina *Azure monitor* en klikt u op *metrische gegevens*. Als u metrische gegevens voor **ExpressRoute** wilt weer geven, filtert u op *ExpressRoute-circuits*van het resource type. Als u **Global Reach** metrische gegevens wilt weer geven, filtert u op resource type *ExpressRoute-circuits* en selecteert u een ExpressRoute-circuit resource waarvoor Global REACH is ingeschakeld. Als u **ExpressRoute direct** -metrische gegevens wilt weer geven, filtert u resource type op *ExpressRoute-poorten*. 

Zodra een metriek is geselecteerd, wordt de standaard aggregatie toegepast. U kunt eventueel splitsen Toep assen, waardoor de metriek wordt weer gegeven met verschillende dimensies.

### <a name="available-metrics"></a>Beschik bare metrische gegevens

|**Meting**|**Categorie**|**Dimensie (s)**|**Functie (s)**|
| --- | --- | --- | --- |
|ARP-Beschik baarheid|Beschikbaarheid|<ui><li>Peer (primaire/secundaire ExpressRoute-router)</ui></li><ui><li> Peering-type (privé/openbaar/micro soft)</ui></li>|ExpressRoute|
|BGP-Beschik baarheid|Beschikbaarheid|<ui><li> Peer (primaire/secundaire ExpressRoute-router)</ui></li><ui><li> Peering-type</ui></li>|ExpressRoute|
|BitsInPerSecond|Verkeer|<ui><li> Peering-type (ExpressRoute)</ui></li><ui><li>Koppeling (ExpressRoute direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Verkeer| <ui><li>Peering-type (ExpressRoute)</ui></li><ui><li> Koppeling (ExpressRoute direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|CPU-gebruik|Prestaties| <ui><li>Exemplaar</ui></li>|Virtual Network gateway ExpressRoute|
|Pakketten per seconde|Prestaties| <ui><li>Exemplaar</ui></li>|Virtual Network gateway ExpressRoute|
|GlobalReachBitsInPerSecond|Verkeer|<ui><li>Skey van gekoppeld circuit (Service sleutel)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|Verkeer|<ui><li>Skey van gekoppeld circuit (Service sleutel)</ui></li>|Global Reach|
|AdminState|Fysieke connectiviteit|Koppeling|ExpressRoute Direct|
|LineProtocol|Fysieke connectiviteit|Koppeling|ExpressRoute Direct|
|RxLightLevel|Fysieke connectiviteit|<ui><li>Gekoppeld</ui></li><ui><li>Straat</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fysieke connectiviteit|<ui><li>Gekoppeld</ui></li><ui><li>Straat</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Het gebruik van *GlobalGlobalReachBitsInPerSecond* en *GlobalGlobalReachBitsOutPerSecond* wordt alleen weer gegeven als er ten minste één Global Reach verbinding tot stand is gebracht.
>

## <a name="circuits-metrics"></a>Meet gegevens circuits

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits in en uit-metrische gegevens voor alle peerings

U kunt metrische gegevens weer geven voor alle peerings op een bepaald ExpressRoute-circuit.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="metrische gegevens van circuit":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Bits in en out-metrische gegevens per peering

U kunt metrische gegevens weer geven voor privé-, open bare en micro soft-peering in bits per seconde.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="metrische gegevens per peering":::

### <a name="bgp-availability---split-by-peer"></a>BGP-Beschik baarheid-splitsen op peer  

U kunt bijna de real-time Beschik baarheid van BGP over Peerings en peers (primaire en secundaire ExpressRoute-routers) bekijken. Dit dash board toont de primaire BGP-sessie voor privé-peering en de tweede BGP-sessie voor persoonlijke peering. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="BGP-Beschik baarheid per peer":::

### <a name="arp-availability---split-by-peering"></a>Beschik baarheid van ARP-splitsen door peering  

U kunt bijna de real-time Beschik baarheid van [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) over Peerings en peers (primaire en secundaire ExpressRoute-routers) bekijken. Dit dash board toont de sessie van de privé-peering-ARP voor beide peers, maar is voltooid voor micro soft-peering tussen peerings. De standaard aggregatie (Average) is gebruikt voor beide peers.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="ARP-Beschik baarheid per peer":::

## <a name="expressroute-direct-metrics"></a>Directe metrische gegevens voor ExpressRoute

### <a name="admin-state---split-by-link"></a>Beheer status-splitsen op koppeling

U kunt de status van de beheerder voor elke koppeling van het directe poort paar ExpressRoute bekijken.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="Directe beheerders status":::

### <a name="bits-in-per-second---split-by-link"></a>Bits per seconde-splitsen op koppeling

U kunt de bits per seconde weer geven voor beide koppelingen van het directe poort paar ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="Er direct-bits per seconde":::

### <a name="bits-out-per-second---split-by-link"></a>Aantal bits per seconde-splitsen op koppeling

U kunt ook de bits per seconde weer geven over beide koppelingen van het directe poort paar ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="Er is geen directe-bits per seconde":::

### <a name="line-protocol---split-by-link"></a>Lijn protocol-splitsen op koppeling

U kunt het regel protocol weer geven op elke koppeling van het directe poort paar ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="Er is geen direct line-protocol":::

### <a name="rx-light-level---split-by-link"></a>RX licht niveau-splitsen op koppeling

U kunt het RX-licht niveau (het licht niveau dat de ExpressRoute directe poort **ontvangt**) weer geven voor elke poort. Gezonde RX Light-niveaus vallen doorgaans binnen een bereik van-10 tot 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="Lampje directe lijn RX licht niveau":::

### <a name="tx-light-level---split-by-link"></a>TX licht niveau-splitsen op koppeling

U kunt het TX licht niveau (het licht niveau dat de ExpressRoute directe poort **verzendt**) weer geven voor elke poort. Gezonde TX Light-niveaus vallen doorgaans binnen een bereik van-10 tot 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="Lampje directe regel TX licht niveau":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>Metrische gegevens voor ExpressRoute-Virtual Network gateway

### <a name="cpu-utilization---split-instance"></a>CPU-gebruik-exemplaar splitsen

U kunt het CPU-gebruik van de gateway-exemplaren weer geven.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="CPU-splitsing":::

### <a name="packets-per-second---split-by-instance"></a>Pakketten per seconde-splitsen op exemplaar

U kunt pakketten per seconde weer geven die de gateway door lopen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Pakketten per seconde splitsen":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute-gateway verbindingen in bits/seconden

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Gateway verbindingen":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Waarschuwingen voor ExpressRoute gateway-verbindingen

1. Als u waarschuwingen wilt configureren, gaat u naar **Azure monitor**en selecteert u **waarschuwingen**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="waarschuwingen":::
2. Klik op **+ doel selecteren** en selecteer de bron van de ExpressRoute gateway verbinding.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="stemming":::
3. Definieer de details van de waarschuwing.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="actie groep":::
4. Definieer en voeg de actie groep toe.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="actie groep toevoegen":::

## <a name="alerts-based-on-each-peering"></a>Waarschuwingen op basis van elke peering

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="elke peering":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Waarschuwingen configureren voor activiteiten logboeken op circuits

In de **waarschuwings criteria**kunt u het **activiteiten logboek** voor het signaal type selecteren en het signaal selecteren.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="activiteiten logboeken":::

## <a name="additional-metrics-in-log-analytics"></a>Aanvullende metrische gegevens in Log Analytics

U kunt ook ExpressRoute-metrische gegevens weer geven door te navigeren naar uw ExpressRoute-circuit resource en het tabblad *Logboeken* te selecteren. Voor alle metrische gegevens die u opvraagt, bevat de uitvoer de onderstaande kolommen.

|**Kolom**|**Type**|**Beschrijving**|
| --- | --- | --- |
|TimeGrain|tekenreeks|PT1M (metrische waarden worden elke minuut gepusht)|
|Aantal|werkelijk|Meestal gelijk aan 2 (elke MSEE pusht elke minuut één metrische waarde)|
|Minimum|werkelijk|Het minimum van de twee metrische waarden die door de twee Msee's worden gepusht|
|Maximum|werkelijk|De Maxiumum van de twee metrische waarden die door de twee Msee's worden gepusht|
|Average|werkelijk|Gelijk aan (mini maal + maximum)/2|
|Totaal|werkelijk|Som van de twee metrieke waarden van beide Msee's (de belangrijkste waarde waarop wordt gefocust voor de opgevraagde metrische gegevens)|
  
## <a name="next-steps"></a>Volgende stappen

Configureer uw ExpressRoute-verbinding.
  
* [Een circuit maken en wijzigen](expressroute-howto-circuit-arm.md)
* [Een peeringconfiguratie maken en wijzigen](expressroute-howto-routing-arm.md)
* [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
