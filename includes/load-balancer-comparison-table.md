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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78202528"
---
| | Standaard load balancer | Basisbalansbalans |
| --- | --- | --- |
| [Grootte van back-end-pool](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Ondersteunt maximaal 1000 instanties. | Ondersteunt maximaal 300 exemplaren. |
| Eindpunten voor de back-end-pool | Alle virtuele machines of virtuele machineschaalsets in één virtueel netwerk. | Virtuele machines in een beschikbaarheidsset of virtuele-machineschaalset. |
| [Statuscontroles](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Gedrag statustest inactief](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP verbindingen blijven in leven op een instantie sonde naar beneden __en__ op alle sondes naar beneden. | TCP-verbindingen blijven in leven op een instantie sonde naar beneden. Alle TCP-verbindingen worden beëindigd wanneer alle sondes zijn uitgeschakeld. |
| Beschikbaarheidszones | Zone-redundante en zonale frontends voor inkomend en uitgaand verkeer. | Niet beschikbaar |
| Diagnostiek | [Multidimensionale metrische gegevens van Azure Monitor](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Azure Monitor-logboeken](../articles/load-balancer/load-balancer-monitor-log.md) |
| HA-poorten | [Beschikbaar voor interne load balancer](../articles/load-balancer/load-balancer-ha-ports-overview.md) | Niet beschikbaar |
| Standaardbeveiliging | Gesloten voor binnenkomende stromen, tenzij toegestaan door een netwerkbeveiligingsgroep. Houd er rekening mee dat intern verkeer van het VNet naar de interne load balancer is toegestaan. | Standaard openen. Netwerkbeveiligingsgroep optioneel. |
| Regels voor uitgaand verkeer | [Declaratieve uitgaande NAT-configuratie](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Niet beschikbaar |
| TCP opnieuw instellen bij inactiviteit | [Beschikbaar op elke regel](../articles/load-balancer/load-balancer-tcp-reset.md) | Niet beschikbaar |
| [Meerdere fronteinden](../articles/load-balancer/load-balancer-multivip-overview.md) | Inkomend en [uitgaand](../articles/load-balancer/load-balancer-outbound-connections.md) | Alleen inkomend |
| Beheerbewerkingen | De meeste bewerkingen < 30 seconden | 60-90+ seconden typisch |
| SLA | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Niet beschikbaar | 
