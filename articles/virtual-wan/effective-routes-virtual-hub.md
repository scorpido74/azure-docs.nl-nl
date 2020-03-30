---
title: 'Effectieve routes van een virtuele hub weergeven: Azure Virtual WAN | Microsoft Documenten'
description: Vie effectieve routes voor een virtuele hub in Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515848"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Bekijk effectieve routes van een virtuele hub

U alle routes van uw Virtuele WAN-hub bekijken in de Azure-portal. Als u de routes wilt bekijken, navigeert u naar de virtuele hub en selecteert u **Routerings-> Effectieve routes weergeven**.

## <a name="understanding-routes"></a><a name="understand"></a>Routes begrijpen

In het volgende voorbeeld u beter begrijpen hoe virtuele WAN-routering wordt weergegeven.

In dit voorbeeld hebben we een virtueel WAN met drie hubs. De eerste hub bevindt zich in de regio Oost-VS, de tweede hub bevindt zich in de regio West-Europa en de derde hub bevindt zich in de regio West-VS. In een virtueel WAN zijn alle hubs met elkaar verbonden. In dit voorbeeld gaan we ervan uit dat de oost-Amerikaanse en West-Europese hubs verbindingen hebben van on-premises branches (spaken) en Azure virtuele netwerken (spaken).

Een Azure VNet sprak (10.4.0.0/16) met een Network Virtual Appliance (10.4.0.6) wordt verder naar een VNet gekeken (10.5.0.0/16). Zie [aanvullende informatie](#abouthubroute) later in dit artikel voor meer informatie over de hubroutetabel.

In dit voorbeeld gaan we er ook van uit dat de West-Europa Tak 1 is aangesloten op de Oost-Amerikaanse hub, evenals met de West-Europese hub. Een ExpressRoute-circuit in Oost-VS verbindt Branch 2 met de Oost-Amerikaanse hub.

![schema](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>Bekijk effectieve routes

Wanneer u 'Effectieve routes weergeven' selecteert in de portal, produceert deze de uitvoer die wordt weergegeven in de [hubroutetabel](#routetable) voor de Hub Oost-VS.

Om dit in perspectief te plaatsen, impliceert de eerste regel dat de Oost-Amerikaanse hub de route van 10.20.1.0/24 (Branch 1) heeft geleerd vanwege de VPN *Next hop type* verbinding ('Next hop' VPN Gateway Instance0 IP 10.1.0.6, Instance1 IP 10.1.0.7). *Route Origin* verwijst naar de resource-id. *AS Path* geeft het AS-pad voor Branch 1 aan.

### <a name="hub-route-table"></a><a name="routetable"></a>Hubroutetabel

Gebruik de schuifbalk onder aan de tabel om het 'AS-pad' weer te geven.

| **Voorvoegsel** |  **Volgend hoptype** | **Volgende hop** |  **Route oorsprong** |**AS-pad** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw| 20.000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d85555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw|23000|
|10.4.0.0/16|Virtuele netwerkverbinding| On-link |  |  |
|10.5.0.0/16| IP-adres| 10.4.0.6|/abonnementen/`<sub>`/resourceGroepen/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|0.0.0.0/0| IP-adres| `<Azure Firewall IP>` |/abonnementen/`<sub>`/resourceGroepen/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|10.22.1.0/16| Externe hub|10.8.0.6, 10.8.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_| 4848-22000 |
|10.9.0.0/16| Externe hub|  On-link |/subscriptions/`<sub>`/resourceGroepen/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_1| |

>[!NOTE]
> Als de Oost-Amerikaanse en de West-Europese hubs niet met elkaar zouden communiceren in de voorbeeldtopologie, zou de geleerde route (10.9.0.0/16) niet bestaan. Hubs adverteren alleen netwerken die rechtstreeks met hen zijn verbonden.
>

## <a name="additional-information"></a><a name="additional"></a>Aanvullende informatie

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>Informatie over de hubroutetabel

U een virtuele hubroute maken en de route toepassen op de virtuele hubroutetabel. U kunt meerdere routes toepassen op de routetabel van de virtuele hub. Hiermee u een route instellen voor bestemming VNet via een IP-adres (meestal het Network Virtual Appliance (NVA) in een spaakVNet). Zie Verkeer van een [virtuele hub naar een NVA voor](virtual-wan-route-table-portal.md)meer informatie over NVA's.

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>Informatie over standaardroute (0.0.0.0/0)

Een virtuele hub heeft de mogelijkheid om een geleerde standaardroute te propageren naar een virtueel netwerk, een site-to-site VPN en een ExpressRoute-verbinding als de vlag 'Ingeschakeld' is op de verbinding. Deze vlag is zichtbaar wanneer u een virtuele netwerkverbinding, een VPN-verbinding of een ExpressRoute-verbinding bewerkt. 'EnableInternetSecurity' is standaard altijd false op Hub VNet-, ExpressRoute- en VPN-verbindingen.

De standaardroute is niet afkomstig van de virtuele WAN-hub. De standaardroute wordt gepropageerd als deze al wordt geleerd door de virtuele WAN-hub als gevolg van het implementeren van een firewall in de hub of als een andere verbonden site tunneling heeft geforceerd ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN het [Overzicht van Virtual WAN](virtual-wan-about.md).