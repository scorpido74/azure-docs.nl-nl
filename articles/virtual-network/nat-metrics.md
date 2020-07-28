---
title: Metrische gegevens en waarschuwingen voor Azure Virtual Network NAT
titleSuffix: Azure Virtual Network
description: Meer informatie over metrische gegevens en waarschuwingen van Azure Monitor die beschikbaar zijn voor Virtual Network NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 75e20a4fb91b73bc353d347f0b34f9be8f7a8a58
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84709943"
---
# <a name="azure-virtual-network-nat-metrics"></a>Metrische gegevens van Azure Virtual Network NAT

Azure Virtual Network NAT-gatewayresources bieden multidimensionale metrische gegevens. U kunt deze metrische gegevens gebruiken om de werking te observeren en voor [probleemoplossing](troubleshoot-nat.md).  Er kunnen waarschuwingen worden geconfigureerd voor kritieke problemen, zoals SNAT-uitputting.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtual Network NAT voor uitgaand verkeer naar internet">
</p>

*Afbeelding: Virtual Network NAT voor uitgaand verkeer naar internet*

## <a name="metrics"></a>Metrische gegevens

NAT-gatewayresources bieden de volgende multidimensionale metrische gegevens in Azure Monitor:

| Gegevens | Beschrijving | Aanbevolen aggregatie | Dimensies |
|---|---|---|---|
| Bytes | Verwerkte bytes inkomend en uitgaand | Sum | Richting (in; uit), Protocol (6 TCP; 17 UDP) |
| Pakketten | Verwerkte pakketten inkomend en uitgaand | Sum | Richting (in; uit), Protocol (6 TCP; 17 UDP) |
| Verwijderde pakketten | Pakketten die zijn verwijderd door de NAT-gateway | Sum | / |
| Aantal SNAT-verbindingen | Statusovergangen per interval | Sum | Verbindingsstatus, Protocol (6 TCP; 17 UDP) |
| Totaal aantal SNAT-verbindingen | Huidige actieve SNAT-verbindingen (~ SNAT-poorten in gebruik) | Sum | Protocol (6 TCP; 17 UDP) |


## <a name="alerts"></a>Waarschuwingen

Waarschuwingen voor metrische gegevens kunnen worden geconfigureerd in Azure Monitor voor elk van de voorgaande [meetwaarden](#metrics).

## <a name="limitations"></a>Beperkingen

Resource Health wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Virtual Network NAT](nat-overview.md)
* Meer informatie over [NAT-gatewayresource](nat-gateway-resource.md)
* Meer informatie over [Azure Monitor](../azure-monitor/overview.md)
* Meer informatie over [het oplossen van problemen met NAT-gatewayresources](troubleshoot-nat.md).
* [Vertel ons in UserVoice wat we verder kunnen ontwikkelen voor Virtual Network NAT](https://aka.ms/natuservoice).


