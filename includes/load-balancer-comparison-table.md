---
title: bestand opnemen
description: bestand opnemen
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/21/2020
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 33e6fecafa2b9748a27794800365974ebc975d0e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78202528"
---
| | Standard Load Balancer | Basis Load Balancer |
| --- | --- | --- |
| [Grootte van back-end-pool](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Ondersteunt maximaal 1000 instanties. | Ondersteunt Maxi maal 300 exemplaren. |
| Eindpunten voor de back-end-pool | Virtuele machines of virtuele-machine schaal sets in één virtueel netwerk. | Virtuele machines in een beschikbaarheidsset of virtuele-machineschaalset. |
| [Statuscontroles](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Gedrag statustest inactief](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP-verbindingen blijven actief op een instantie die omlaag __en__ op alle tests wordt gecontroleerd. | TCP-verbindingen blijven actief op een instantie die wordt getest. Alle TCP-verbindingen worden beëindigd wanneer alle tests uitvallen. |
| Beschikbaarheidszones | Zone-redundante en zonegebonden-front-end voor binnenkomend en uitgaand verkeer. | Niet beschikbaar |
| Diagnostiek | [Multidimensionale metrische gegevens Azure Monitor](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Azure Monitor-logboeken](../articles/load-balancer/load-balancer-monitor-log.md) |
| HA-poorten | [Beschikbaar voor interne Load Balancer](../articles/load-balancer/load-balancer-ha-ports-overview.md) | Niet beschikbaar |
| Standaardbeveiliging | Gesloten voor binnenkomende stromen, tenzij toegestaan door een netwerk beveiligings groep. Houd er rekening mee dat intern verkeer van het VNet naar de interne load balancer is toegestaan. | Standaard geopend. De netwerk beveiligings groep is optioneel. |
| Regels voor uitgaand verkeer | [Declaratieve uitgaande NAT-configuratie](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Niet beschikbaar |
| TCP opnieuw instellen bij inactiviteit | [Beschikbaar op elke regel](../articles/load-balancer/load-balancer-tcp-reset.md) | Niet beschikbaar |
| [Meerdere front-ends](../articles/load-balancer/load-balancer-multivip-overview.md) | Inkomend en [uitgaand](../articles/load-balancer/load-balancer-outbound-connections.md) | Alleen inkomend |
| Beheerbewerkingen | De meeste bewerkingen < 30 seconden | 60 tot 90 seconden typisch |
| SLA | [99,99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Niet beschikbaar | 
