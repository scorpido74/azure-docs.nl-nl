---
title: Statistieken en waarschuwingen voor Azure Virtual Network NAT
titleSuffix: Azure Virtual Network
description: Lees de statistieken en waarschuwingen van Azure Monitor die beschikbaar zijn voor NAT van het virtuele netwerk.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 1c3a73cba6a2ece0c9c7459b3515f053a648d683
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408616"
---
# <a name="azure-virtual-network-nat-metrics"></a>Nat-statistieken voor Azure Virtual Network

Azure Virtual Network NAT-gatewaybronnen bieden multidimensionale statistieken. U deze statistieken gebruiken om de bewerking te observeren en om [problemen op te lossen.](troubleshoot-nat.md)  Waarschuwingen kunnen worden geconfigureerd voor kritieke problemen zoals SNAT-uitputting.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtual Network NAT voor uitgaande naar internet">
</p>

*Figuur: Virtual Network NAT voor uitgaande naar internet*

## <a name="metrics"></a>Metrische gegevens

NAT-gatewaybronnen bieden de volgende multidimensionale statistieken in Azure Monitor:

| Gegevens | Beschrijving | Aanbevolen aggregatie | Dimensies |
|---|---|---|---|
| Bytes | Inkomende en uitgaande bytes verwerkt | Sum | Richting (In; Uit), Protocol (6 TCP; 17 UDP) |
| Pakketten | Pakketten verwerkt inkomende en uitgaande | Sum | Richting (In; Uit), Protocol (6 TCP; 17 UDP) |
| Gedropte pakketten | Pakketten die door de NAT-gateway zijn gedropt | Sum | / |
| Aantal SNAT-verbindingen | Statusovergangen per interval | Sum | Verbindingsstatus, Protocol (6 TCP; 17 UDP) |
| Totaal aantal SNAT-verbindingen | Huidige actieve SNAT-verbindingen (~ SNAT-poorten in gebruik) | Sum | Protocol (6 TCP; 17 UDP) |


## <a name="alerts"></a>Waarschuwingen

Waarschuwingen voor metrische gegevens kunnen worden geconfigureerd in Azure Monitor voor elk van de voorgaande [statistieken](#metrics).

## <a name="limitations"></a>Beperkingen

Resourcestatus wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [NAT van virtueel netwerk](nat-overview.md)
* Meer informatie over [NAT-gatewaybron](nat-gateway-resource.md)
* Meer informatie over [Azure Monitor](../azure-monitor/overview.md)
* Meer informatie over [het oplossen van PROBLEMEN met NAT-gatewaybronnen](troubleshoot-nat.md).
* [Vertel ons wat we nu moeten bouwen voor Virtual Network NAT in UserVoice.](https://aka.ms/natuservoice)


