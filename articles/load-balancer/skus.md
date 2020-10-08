---
title: Azure Load Balancer-SKU's
description: Overzicht van Azure Load Balancer-SKU's
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2020
ms.author: allensu
ms.openlocfilehash: 8eb8be3307cf5e1df987f636be5c01cecaf4ae45
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91631440"
---
# <a name="azure-load-balancer-skus"></a>Azure Load Balancer-SKU's

Azure Load Balancer heeft twee SKU's.

## <a name="sku-comparison"></a><a name="skus"></a>-SKU vergelijking

Load balancer ondersteund zowel Standard als Basic SKU's. Deze SKU's verschillen van schaal, functies en prijs. Elk scenario dat mogelijk is met de Basic-versie van Load Balancer kan worden gemaakt met de Standard-versie ervan.

Als u de verschillen met elkaar wilt vergelijken en wilt weten wat ze precies inhouden, kunt u de volgende tabel raadplegen. Zie [Overzicht van Azure Standard Load Balancer](load-balancer-standard-overview.md) voor meer informatie.

>[!NOTE]
> Microsoft raadt de Standard Load Balancer aan.
Zelfstandige virtuele machines, beschikbaarheidssets en virtuele-machineschaalsets kunnen worden verbonden met slechts één SKU, niet met beide. De SKU van de Load Balancer en het openbare IP-adres overeenkomen wanneer u ze met openbare IP-adressen. SKU's van Load Balancers en openbare IP-adressen zijn niet veranderlijk.

| | Standard Load Balancer | Basic Load Balancer |
| --- | --- | --- |
| **[Grootte van back-end-pool](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)** | Ondersteunt maximaal 1000 instanties. | Ondersteunt maximaal 300 instanties. |
| **Eindpunten voor de back-end-pool** | Virtuele machines of virtuele-machineschaalsets in een enkel virtueel netwerk. | Virtuele machines in een beschikbaarheidsset of virtuele-machineschaalset. |
| **[Statuscontroles](./load-balancer-custom-probe-overview.md#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Gedrag statustest inactief](./load-balancer-custom-probe-overview.md#probedown)** | TCP-verbindingen blijven actief als een instantietest inactief is __en__ als alle tests inactief zijn. | TCP-verbindingen blijven actief als een instantietest inactief is. Alle TCP-verbindingen worden beëindigd als tests inactief zijn. |
| **Beschikbaarheidszones** | Zone-redundante en zonale front-ends voor binnenkomend en uitgaand verkeer. | Niet beschikbaar |
| **Diagnostics** | [Multidimensionale metrische gegevens van Azure Monitor](./load-balancer-standard-diagnostics.md) | [Azure Monitor-logboeken](./load-balancer-monitor-log.md) |
| **HA-poorten** | [Beschikbaar voor interne Load Balancer](./load-balancer-ha-ports-overview.md) | Niet beschikbaar |
| **Standaard beveiligd** | Gesloten voor binnenkomende stromen, tenzij dat toegestaan wordt door een netwerkbeveiligingsgroep. Intern verkeer van het virtuele netwerk naar de interne load balancer is toegestaan. | Standaard open. Netwerkbeveiligingsgroep optioneel. |
| **Regels voor uitgaand verkeer** | [Declaratieve uitgaande NAT-configuratie](./load-balancer-outbound-rules-overview.md) | Niet beschikbaar |
| **TCP opnieuw instellen bij inactiviteit** | [Beschikbaar op alle regels](./load-balancer-tcp-reset.md) | Niet beschikbaar |
| **[Meerdere front-ends](./load-balancer-multivip-overview.md)** | Inkomend en [uitgaand](./load-balancer-outbound-connections.md) | Alleen inkomend |
| **Beheerbewerkingen** | De meeste bewerkingen < 30 seconden | Meestal 60-90 seconden |
| **SLA** | [99,99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Niet beschikbaar | 

Zie [limieten voor Load Balancer](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) voor meer informatie. Zie [Overzicht](load-balancer-standard-overview.md), [Prijzen](https://aka.ms/lbpricing) en [SLA](https://aka.ms/lbsla) voor meer details over Standard Load Balancer.

## <a name="limitations"></a>Beperkingen

- SKU's zijn niet veranderlijk. U kunt de SKU van een bestaande resource niet veranderen.
- Een zelfstandige resource voor een virtuele machine, resource voor een beschikbaarheidsset of resource voor een virtuele-machineschaalset kan verwijzen naar één SKU, nooit naar beide.
- [Verplaatsen](../azure-resource-manager/management/move-resource-group-and-subscription.md):
  - Verplaatsen van resourcegroepen (binnen hetzelfde abonnement) **wordt ondersteund** voor Standard Load Balancer en Openbare IP-adressen van Standard. 
  - [Verplaatsen van abonnementsgroepen](../azure-resource-manager/management/move-support-resources.md) wordt **niet** ondersteund voor resources voor Standard Load Balancer en Openbare IP-adressen van Standard.

## <a name="next-steps"></a>Volgende stappen

- Zie [Een openbare Standard Load Balancer maken](quickstart-load-balancer-standard-public-portal.md) om aan de slag te gaan met een Load Balancer.
- Meer informatie over [Load Balancer van het type Standard en beschikbaarheidszones](load-balancer-standard-availability-zones.md).
- Meer informatie over [Statuscontroles](load-balancer-custom-probe-overview.md).
- Meer informatie over het gebruik van [Load Balancer voor uitgaande verbindingen](load-balancer-outbound-connections.md).
- Meer informatie over [Standard Load Balancer met taakverdelingsregels voor poorten met hoge beschikbaarheid](load-balancer-ha-ports-overview.md).
- Meer informatie over [Netwerkbeveiligingsgroepen](../virtual-network/security-overview.md).
