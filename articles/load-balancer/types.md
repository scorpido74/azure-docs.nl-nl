---
title: Azure Load Balancer Sku's
description: Overzicht van Azure Load Balancer Sku's
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2020
ms.author: allensu
ms.openlocfilehash: c7ca630b4a6a1bedeab21feacc22cd27a1a3ee7e
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734940"
---
# <a name="azure-load-balancer-skus"></a>Azure Load Balancer Sku's

Azure Load Balancer heeft twee soorten of Sku's.

## <a name="sku-comparison"></a><a name="skus"></a>SKU-vergelijking

De Load Balancer ondersteunt zowel de basis-als standaard-Sku's. Deze Sku's wijken af van de schaal, functies en prijzen van het scenario. Elk scenario dat mogelijk is met basis load balancer, kan worden gemaakt met standaard load balancer.

Als u de verschillen met elkaar wilt vergelijken en wilt weten wat ze precies inhouden, kunt u de volgende tabel raadplegen. Zie [overzicht van Azure Standard Load Balancer](load-balancer-standard-overview.md)voor meer informatie.

>[!NOTE]
> Micro soft adviseert standaard load balancer.
Zelfstandige virtuele machines, beschikbaarheidssets en virtuele-machineschaalsets kunnen worden verbonden met slechts één SKU, niet met beide. De Load Balancer en de open bare IP-adres-SKU moeten overeenkomen wanneer u deze gebruikt met open bare IP-adressen. De Load Balancer en de open bare IP-Sku's zijn niet onveranderbaar.

| | Standard Load Balancer | Basis Load Balancer |
| --- | --- | --- |
| [Grootte van back-end-pool](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Ondersteunt maximaal 1000 instanties. | Ondersteunt Maxi maal 300 exemplaren. |
| Eindpunten voor de back-end-pool | Virtuele machines of virtuele-machine schaal sets in één virtueel netwerk. | Virtuele machines in een beschikbaarheidsset of virtuele-machineschaalset. |
| [Statuscontroles](./load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Gedrag statustest inactief](./load-balancer-custom-probe-overview.md#probedown) | TCP-verbindingen blijven actief op een instantie die omlaag __en__ op alle tests wordt gecontroleerd. | TCP-verbindingen blijven actief op een instantie die wordt getest. Alle TCP-verbindingen worden beëindigd wanneer alle tests uitvallen. |
| Beschikbaarheidszones | Zone-redundante en zonegebonden-front-end voor binnenkomend en uitgaand verkeer. | Niet beschikbaar |
| Diagnostiek | [Multidimensionale metrische gegevens Azure Monitor](./load-balancer-standard-diagnostics.md) | [Azure Monitor-logboeken](./load-balancer-monitor-log.md) |
| HA-poorten | [Beschikbaar voor interne Load Balancer](./load-balancer-ha-ports-overview.md) | Niet beschikbaar |
| Standaardbeveiliging | Gesloten voor binnenkomende stromen, tenzij toegestaan door een netwerk beveiligings groep. Houd er rekening mee dat intern verkeer van het VNet naar de interne load balancer is toegestaan. | Standaard geopend. De netwerk beveiligings groep is optioneel. |
| Regels voor uitgaand verkeer | [Declaratieve uitgaande NAT-configuratie](./load-balancer-outbound-rules-overview.md) | Niet beschikbaar |
| TCP opnieuw instellen bij inactiviteit | [Beschikbaar op elke regel](./load-balancer-tcp-reset.md) | Niet beschikbaar |
| [Meerdere front-ends](./load-balancer-multivip-overview.md) | Inkomend en [uitgaand](./load-balancer-outbound-connections.md) | Alleen inkomend |
| Beheerbewerkingen | De meeste bewerkingen < 30 seconden | 60 tot 90 seconden typisch |
| SLA | [99,99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Niet beschikbaar | 

Zie [limieten voor load balancers](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)voor meer informatie. Zie [Overzicht](load-balancer-standard-overview.md), [Prijzen](https://aka.ms/lbpricing) en [SLA](https://aka.ms/lbsla) voor meer details over Standard Load Balancer.

## <a name="next-steps"></a>Volgende stappen

- Zie [een open bare Standard Load Balancer maken](quickstart-load-balancer-standard-public-portal.md) om aan de slag te gaan met het gebruik van een Load Balancer.
- Meer informatie over het gebruik van [Standard Load Balancer en Beschikbaarheidszones](load-balancer-standard-availability-zones.md).
- Meer informatie over [status controles](load-balancer-custom-probe-overview.md).
- Meer informatie over het gebruik [van Load Balancer voor uitgaande verbindingen](load-balancer-outbound-connections.md).
- Meer informatie over Standard Load Balancer met de taakverdelings [regels van ha-poorten](load-balancer-ha-ports-overview.md).
- Meer informatie over [netwerk beveiligings groepen](../virtual-network/security-overview.md).
