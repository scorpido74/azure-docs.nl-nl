---
title: bestand opnemen
description: bestand opnemen
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335107"
---
| Resource | Limiet |
| --- | --- |
| ExpressRoute-circuits per abonnement |10 |
| ExpressRoute-circuits per regio per abonnement, met Azure Resource Manager |10 |
| Maximaal aantal routes geadverteerd voor Azure private peering met ExpressRoute Standard |4000 |
| Maximaal aantal routes geadverteerd voor Azure private peering met ExpressRoute Premium add-on |10.000 |
| Maximaal aantal routes geadverteerd vanuit Azure private peering vanuit de VNet-adresruimte voor een ExpressRoute-verbinding |200 |
| Maximaal aantal routes geadverteerd aan Microsoft peering met ExpressRoute Standard |200 |
| Maximaal aantal routes geadverteerd aan Microsoft peering met ExpressRoute Premium add-on |200 |
| Maximum aantal ExpressRoute-circuits gekoppeld aan hetzelfde virtuele netwerk op dezelfde peeringlocatie |4 |
| Maximum aantal ExpressRoute-circuits gekoppeld aan hetzelfde virtuele netwerk op verschillende peeringlocaties |4 |
| Aantal toegestane virtuele netwerkkoppelingen per ExpressRoute-circuit |Bekijk het [aantal virtuele netwerken per expressroute-circuittabel.](#vnetpercircuit)  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>Aantal virtuele netwerken per ExpressRoute-circuit
| **Circuitgrootte** | **Aantal virtuele netwerkkoppelingen voor Standard** | **Aantal virtuele netwerkkoppelingen met Premium-add-on** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

**100 Gbps ExpressRoute Direct Alleen*

> [!NOTE]
> Global Reach-verbindingen tellen mee voor de limiet van virtuele netwerkverbindingen per ExpressRoute Circuit. Een 10 Gbps Premium Circuit zou bijvoorbeeld 5 Global Reach-verbindingen en 95-verbindingen met de ExpressRoute-gateways of 95 Global Reach-verbindingen en 5 verbindingen met de ExpressRoute-gateways of een andere combinatie tot de limiet van 100 verbindingen mogelijk maken voor het circuit.
