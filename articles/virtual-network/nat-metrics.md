---
title: Metrische gegevens en waarschuwingen voor Azure Virtual Network NAT
titleSuffix: Azure Virtual Network
description: Meer informatie over Azure Monitor metrische gegevens en waarschuwingen die beschikbaar zijn voor Virtual Network NAT.
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
ms.openlocfilehash: 4f1760c32117b34d4d453964473cba3c7d07f725
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79202175"
---
# <a name="azure-virtual-network-nat-metrics"></a>Metrische gegevens van Azure Virtual Network NAT

Azure Virtual Network NAT gateway-resources bieden multidimensionale metrische gegevens. U kunt deze metrische gegevens gebruiken om de werking van de bewerking en voor het [oplossen van problemen](nat-metrics.md)te bekijken.  Waarschuwingen kunnen worden geconfigureerd voor kritieke problemen, zoals SNAT-uitputting.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="NAT Virtual Network voor uitgaand verkeer naar Internet">
</p>

*Afbeelding: Virtual Network NAT voor uitgaand verkeer naar Internet*

## <a name="metrics"></a>Metrische gegevens

De resources van de NAT-gateway bieden de volgende meerdimensionale metrische gegevens in Azure Monitor:

| Gegevens | Beschrijving | Aanbevolen aggregatie | Dimensies |
|---|---|---|---|
| Bytes | Verwerkte bytes inkomend en uitgaand | Sum | Richting (in; Out), protocol (6 TCP; 17-UDP) |
| Pakket | Pakketten die binnenkomend en uitgaand worden verwerkt | Sum | Richting (in; Out), protocol (6 TCP; 17-UDP) |
| Verwijderde pakketten | Pakketten die worden verwijderd door de NAT-gateway | Sum | / |
| Aantal SNAT-verbindingen | Status overgangen per interval | Sum | Verbindings status, protocol (6 TCP; 17-UDP) |
| Totaal aantal SNAT-verbindingen | Huidige actieve SNAT-verbindingen (~ SNAT-poorten in gebruik) | Sum | Protocol (6 TCP; 17-UDP) |


## <a name="alerts"></a>Waarschuwingen

Waarschuwingen voor metrische gegevens kunnen worden geconfigureerd in Azure Monitor voor elk van de voor gaande [metrische gegevens](#metrics).

## <a name="limitations"></a>Beperkingen

Resource Health wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Virtual Network NAT](nat-overview.md)
* Meer informatie over de [NAT gateway-resource](nat-gateway-resource.md)
* Meer informatie over [Azure monitor](../azure-monitor/overview.md)
* Meer informatie over het [oplossen van problemen met NAT-gateway bronnen](troubleshoot-nat.md).
* [Vertel ons wat u nu kunt bouwen voor Virtual Network nat in UserVoice](https://aka.ms/natuservoice).


