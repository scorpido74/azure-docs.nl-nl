---
title: 'Efficiënte routes van een virtuele hub weer geven: Azure Virtual WAN | Microsoft Docs'
description: Efficiënte routes voor een virtuele hub in azure Virtual WAN door lopen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73515848"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Efficiënte routes van een virtuele hub weer geven

U kunt alle routes van uw virtuele WAN-hub weer geven in de Azure Portal. Als u de routes wilt weer geven, gaat u naar de virtuele hub en selecteert u vervolgens **route ring-> efficiënte routes weer geven**.

## <a name="understanding-routes"></a><a name="understand"></a>Over routes

In het volgende voor beeld kunt u beter begrijpen hoe virtuele WAN-route ring wordt weer gegeven.

In dit voor beeld hebben we een virtueel WAN met drie hubs. De eerste hub bevindt zich in de regio VS-Oost, de tweede hub bevindt zich in de Europa-west regio en de derde hub bevindt zich in de regio vs-West. In een virtueel WAN zijn alle hubs onderling verbonden. In dit voor beeld gaan we ervan uit dat de VS-Oost en de Europa-west hubs verbindingen hebben van on-premises vertakkingen (spokes) en virtuele Azure-netwerken (spokes).

Een Azure VNet-spoke (10.4.0.0/16) met een virtueel netwerk apparaat (10.4.0.6) is verder gekoppeld aan een VNet (10.5.0.0/16). Meer [informatie vindt u verderop in](#abouthubroute) dit artikel voor meer informatie over de route tabel van de hub.

In dit voor beeld gaan we ervan uit dat de Europa-west vertakking 1 is verbonden met de VS-Oost-hub, evenals aan de Europa-west hub. Een ExpressRoute-circuit in VS-Oost verbindt vertakking 2 met de VS-Oost-hub.

![schema](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>Efficiënte routes weer geven

Wanneer u ' werkelijke routes weer geven ' in de portal selecteert, wordt de uitvoer gemaakt die wordt weer gegeven in de [route tabel](#routetable) van de hub voor de VS-Oost-hub.

Om dit in perspectief te brengen, impliceert de eerste regel dat de VS-hub de route van 10.20.1.0/24 (branch 1) heeft geleerd als gevolg van de VPN-verbinding van de *volgende type* (' volgende hop ' VPN gateway Instance0 IP 10.1.0.6, exemplaar1 IP 10.1.0.7). *Oorsprong van route* verwijst naar de resource-id. *As Path* geeft het as-pad voor vertakking 1 aan.

### <a name="hub-route-table"></a><a name="routetable"></a>Route tabel van de hub

Gebruik de schuif balk aan de onderkant van de tabel om het "AS-pad" weer te geven.

| **Beleids** |  **Volgend hoptype** | **Volgende hop** |  **Route oorsprong** |**ALS pad** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw| 20.000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d85555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw|23000|
|10.4.0.0/16|Virtual Network verbinding| On-link |  |  |
|10.5.0.0/16| IP-adres| 10.4.0.6|/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualhubs/easthub_1/routetables/table_1| |
|0.0.0.0/0| IP-adres| `<Azure Firewall IP>` |/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualhubs/easthub_1/routetables/table_1| |
|10.22.1.0/16| Externe hub|10.8.0.6, 10.8.0.7|/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualhubs/westhub_| 4848-22000 |
|10.9.0.0/16| Externe hub|  On-link |/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualhubs/westhub_1| |

>[!NOTE]
> Als het VS-Oost en de Europa-west hubs niet communiceren met elkaar in de voorbeeld topologie, is de door u geleerde route (10.9.0.0/16) niet aanwezig. Hubs adverteren alleen netwerken die rechtstreeks met hen zijn verbonden.
>

## <a name="additional-information"></a><a name="additional"></a>Aanvullende informatie

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>Over de route tabel van de hub

U kunt een virtuele hub-route maken en de route Toep assen op de route tabel van de virtuele hub. U kunt meerdere routes toepassen op de routetabel van de virtuele hub. Hiermee kunt u een route voor het doel-VNet instellen via een IP-adres (meestal het virtuele netwerk apparaat (NVA) in een spoke-VNet). Zie [verkeer van een virtuele hub naar een NVA routeren](virtual-wan-route-table-portal.md)voor meer informatie over nva's.

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>Over de standaard route (0.0.0.0/0)

Met een virtuele hub kunt u een geleerde standaard route door geven naar een virtueel netwerk, een site-naar-site-VPN en een ExpressRoute-verbinding als de vlag is ingeschakeld op de verbinding. Deze markering wordt weer gegeven wanneer u een virtuele netwerk verbinding, een VPN-verbinding of een ExpressRoute-verbinding bewerkt. ' EnableInternetSecurity ' is standaard altijd onwaar op hub VNet-, ExpressRoute-en VPN-verbindingen.

De standaard route is niet afkomstig van de virtuele WAN-hub. De standaard route wordt door gegeven als deze al is geleerd door de virtuele WAN-hub als gevolg van het implementeren van een firewall in de hub, of als een andere verbonden site geforceerde tunneling heeft ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN het [Overzicht van Virtual WAN](virtual-wan-about.md).