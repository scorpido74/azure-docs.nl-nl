---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 75152fabfc33dda0494d871fbdf9a388f4260c0d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495718"
---
Wanneer u een virtuele netwerkgateway maakt, moet u de gewenste gateway-SKU opgeven. Selecteer de SKU die aan uw vereisten voldoet op basis van de typen werkbelasting, doorvoer, functies en SLA's. Zie [Azure-beschikbaarheidszones gateway-sku's](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md)voor virtuele netwerk gateway-sku's in azure-beschikbaarheidszones.

###  <a name="benchmark"></a>Gateway-SKU's per tunnel, verbinding en doorvoer

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

###  <a name="feature"></a>Gateway-Sku's per functieset

De nieuwe VPN-gateway-Sku's stroom lijnen de functie sets die worden aangeboden op de gateways:

| **SKU**| **Functies**|
| ---    | ---         |
|**Basic** (* *)   | **Op route gebaseerd VPN**: 10 tunnels voor S2S/Connection; geen RADIUS-authenticatie voor P2S; geen IKEv2 voor P2S<br>**Op beleid gebaseerde VPN**: (IKEv1): 1 S2S/Connection tunnel; geen P2S|
| **Alle Generation1-en Generation2-Sku's behalve basis** | **Op route gebaseerd VPN**: Maxi maal 30 tunnels (*), P2S, BGP, actief-actief, aangepast IPSec/IKE-beleid, ExpressRoute/VPN-samen werking |
|        |             |

(*) U kunt "PolicyBasedTrafficSelectors" configureren om een op route gebaseerde VPN-gateway te verbinden met meerdere on-premises op beleid gebaseerde firewall apparaten. Raadpleeg [Connect VPN gateways to multiple on-premises policy-based VPN devices using PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (VPN-gateways verbinden met meerdere on-premises,op beleid gebaseerde VPN-apparaten met behulp van PowerShell) voor meer informatie.

(\*\*) De basis-SKU wordt beschouwd als een verouderde SKU. Deze SKU kent bepaalde functiebeperkingen. Zo kunt u een gateway die gebruikmaakt van een Basic-SKU, niet overzetten naar een van de nieuwe gateway-SKU's. In plaats daarvan moet u een nieuwe SKU gebruiken, wat betekent dat u uw VPN-gateway eerst moet verwijderen en vervolgens opnieuw moet maken.

###  <a name="workloads"></a>Gateway-Sku's-productie versus dev-test-workloads

Vanwege de verschillen in service overeenkomsten en functie sets raden wij de volgende Sku's aan voor productie versus dev-test:

| **Workload**                       | **SKU's**               |
| ---                                | ---                    |
| **Productie, kritieke werkbelastingen** | Alle Generation1-en Generation2-Sku's behalve basis |
| **Ontwikkelen en testen of conceptontwerpen**   | Basic (* *)                 |
|                                    |                        |

(\*\*) De basis-SKU wordt beschouwd als een verouderde SKU en heeft functie beperkingen. Controleer of de functie die u nodig hebt, wordt ondersteund voordat u kiest voor de Basic-SKU.

Als u de oude Sku's (verouderd) gebruikt, zijn de aanbevelingen voor productie-SKU'S standaard en high performance. Zie [Gateway-sku's (verouderd)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)voor informatie over en instructies voor oude sku's.
