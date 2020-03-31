---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74085232"
---
Wanneer u een virtuele netwerkgateway maakt, moet u de gewenste gateway-SKU opgeven. Selecteer de SKU die aan uw vereisten voldoet op basis van de typen werkbelasting, doorvoer, functies en SLA's. Zie [Azure Availability Zones-gateway](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md)SKU's voor SKU's voor virtuele netwerkgateways in Azure Availability Zones.

###  <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>Gateway-SKU's per tunnel, verbinding en doorvoer

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> Vpngw SKU's (Vpngw1, Vpngw1AZ, Vpngw2, VpnGw2AZ, Vpngw3, VpnGw3AZ, VpnGw4, VpnGw4AZ, VpnGw5 en VpnGw5AZ) worden alleen ondersteund voor het Resource Manager-implementatiemodel. Klassieke virtuele netwerken moeten de oude (legacy) SKU's blijven gebruiken.
>  * Zie [Werken met VPN-gateway SKU's (legacy SKU's)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)voor informatie over het werken met de verouderde gateway SKU's (Basic, Standard en HighPerformance).
>  * Zie [Virtual Network Gateways for ExpressRoute voor](../articles/expressroute/expressroute-about-virtual-network-gateways.md)ExpressRoute voor ExpressRoute.
>

###  <a name="gateway-skus-by-feature-set"></a><a name="feature"></a>Gateway SKU's op functieset

De nieuwe VPN-gateway SKU's stroomlijnen de functiesets die op de gateways worden aangeboden:

| **Sku**| **Functies**|
| ---    | ---         |
|**Basic** (**)   | **Routegebaseerde VPN**: 10 tunnels voor S2S/verbindingen; geen RADIUS-verificatie voor P2S; geen IKEv2 voor P2S<br>**Beleidsgebaseerde VPN**: (IKEv1): 1 S2S/verbindingstunnel; geen P2S|
| **Alle Generation1 en Generation2 SKU's behalve Basic** | **Route-based VPN**: maximaal 30 tunnels (*), P2S, BGP, actief, aangepast IPsec/IKE-beleid, ExpressRoute/VPN coëxistentie |
|        |             |

(*) U 'PolicyBasedTrafficSelectors' configureren om een op route gebaseerde VPN-gateway te verbinden met meerdere on-premises beleidsgebaseerde firewallapparaten. Raadpleeg [Connect VPN gateways to multiple on-premises policy-based VPN devices using PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (VPN-gateways verbinden met meerdere on-premises,op beleid gebaseerde VPN-apparaten met behulp van PowerShell) voor meer informatie.

(\*\*) De Basic SKU wordt beschouwd als een legacy SKU. Deze SKU kent bepaalde functiebeperkingen. Zo kunt u een gateway die gebruikmaakt van een Basic-SKU, niet overzetten naar een van de nieuwe gateway-SKU's. In plaats daarvan moet u een nieuwe SKU gebruiken, wat betekent dat u uw VPN-gateway eerst moet verwijderen en vervolgens opnieuw moet maken.

###  <a name="gateway-skus---production-vs-dev-test-workloads"></a><a name="workloads"></a>Gateway SKU's - Productie vs. Dev-Test Workloads

Vanwege de verschillen in SLA's en functiesets, raden we de volgende SKU's aan voor productie vs. ontwikkelen en testen:

| **Workload**                       | **SKU's**               |
| ---                                | ---                    |
| **Productie, kritieke werkbelastingen** | Alle Generation1 en Generation2 SKU's behalve Basic |
| **Ontwikkelen en testen of conceptontwerpen**   | Basic (**)                 |
|                                    |                        |

(\*\*) De Basic SKU wordt beschouwd als een legacy SKU en heeft functiebeperkingen. Controleer of de functie die u nodig hebt, wordt ondersteund voordat u kiest voor de Basic-SKU.

Als u de oude SKU's (legacy) gebruikt, zijn de aanbevelingen voor de productie-SKU Standaard en HighPerformance. Zie [Gateway SKU's (legacy)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)voor informatie en instructies voor oude SKU's.
