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
ms.openlocfilehash: 45d34297bf37a6e46bc57e95ff49def49051e32e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "67622093"
---
| Resource | Standaard/maximum limiet |
| --- | --- |
| ExpressRoute-circuits per abonnement |10 |
| ExpressRoute-circuits per regio per abonnement, met Azure Resource Manager |10 |
| Het maximum aantal routes dat is geadverteerd voor persoonlijke Azure-peering met ExpressRoute Standard |4,000 |
| Het maximum aantal routes dat is geadverteerd voor persoonlijke Azure-peering met de ExpressRoute Premium-invoeg toepassing |10.000 |
| Het maximum aantal routes dat is geadverteerd vanuit persoonlijke Azure-peering vanuit de VNet-adres ruimte voor een ExpressRoute-verbinding |200 |
| Maximum aantal routes dat is geadverteerd naar micro soft-peering met ExpressRoute Standard |200 |
| Maximum aantal routes dat is geadverteerd naar micro soft-peering met ExpressRoute Premium-invoeg toepassing |200 |
| Het maximum aantal ExpressRoute-circuits dat is gekoppeld aan hetzelfde virtuele netwerk op dezelfde peering-locatie |4 |
| Het maximum aantal ExpressRoute-circuits dat is gekoppeld aan hetzelfde virtuele netwerk op verschillende peering locaties |4 |
| Aantal toegestane virtuele netwerk koppelingen per ExpressRoute-circuit |Bekijk de tabel [aantal virtuele netwerken per ExpressRoute-circuit](#vnetpercircuit) .  |

#### <a name="vnetpercircuit"></a>Aantal virtuele netwerken per ExpressRoute-circuit
| **Grootte van circuit** | **Aantal koppelingen naar virtuele netwerken voor Standard** | **Aantal koppelingen naar het virtuele netwerk met Premium-invoeg toepassing** |
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

**100 Gbps alleen direct ExpressRoute*
