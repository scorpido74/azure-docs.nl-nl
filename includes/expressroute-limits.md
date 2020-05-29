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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335107"
---
| Resource | Limiet |
| --- | --- |
| ExpressRoute-circuits per abonnement |10 |
| ExpressRoute-circuits per regio per abonnement, met Azure Resource Manager |10 |
| Het maximale aantal routes dat mag worden geadverteerd aan Azure-privépeering met ExpressRoute Standard |4000 |
| Het maximale aantal routes dat mag worden geadverteerd aan Azure-privépeering met de ExpressRoute Premium-invoegtoepassing |10.000 |
| Het maximale aantal routes dat mag worden geadverteerd vanaf Azure-privépeering vanuit de VNet-adresruimte voor een ExpressRoute-verbinding |200 |
| Het maximale aantal routes dat mag worden geadverteerd aan Microsoft-peering met ExpressRoute Standard |200 |
| Het maximale aantal routes dat mag worden geadverteerd aan Microsoft-peering met de ExpressRoute Premium-invoegtoepassing |200 |
| Het maximale aantal ExpressRoute-circuits dat mag worden gekoppeld aan hetzelfde virtuele netwerk op dezelfde peeringlocatie |4 |
| Het maximale aantal ExpressRoute-circuits dat mag worden gekoppeld aan hetzelfde virtuele netwerk op verschillende peeringlocaties |4 |
| Aantal toegestane virtuele netwerkkoppelingen per ExpressRoute-circuit |Zie de tabel [Aantal virtuele netwerken per ExpressRoute-circuit](#vnetpercircuit).  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a> Aantal virtuele netwerken per ExpressRoute-circuit
| **Circuitgrootte** | **Aantal virtuele netwerkkoppelingen voor Standard** | **Aantal virtuele netwerkkoppelingen met Premium-invoegtoepassing** |
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

**100 Gbps alleen ExpressRoute Direct*

> [!NOTE]
> Aantal Global Reach-verbindingen vergeleken met de limiet van virtuele netwerkverbindingen per ExpressRoute-circuit. Zo zijn bij een Premium-circuit van 10 Gbps 5 Global Reach-verbindingen en 95 ExpressRoute-gatewayverbindingen of 95 Global Reach-verbindingen en 5 ExpressRoute-gatewayverbindingen toegestaan. Zolang de combinaties gezamenlijk uit maximaal 100 circuitverbindingen bestaan, zijn ze toegestaan.
