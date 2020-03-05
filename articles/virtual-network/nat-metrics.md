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
ms.date: 03/03/2020
ms.author: allensu
ms.openlocfilehash: 6bffa114b325e8b98f439e7ff2ce21480407a643
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274912"
---
# <a name="azure-virtual-network-nat-metrics"></a>Metrische gegevens van Azure Virtual Network NAT

Azure Virtual Network NAT gateway-resources bieden multidimensionale metrische gegevens. U kunt deze metrische gegevens gebruiken om de werking van de bewerking en voor het [oplossen van problemen](nat-metrics.md)te bekijken.  Waarschuwingen kunnen worden geconfigureerd voor kritieke problemen, zoals SNAT-uitputting.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="NAT Virtual Network voor uitgaand verkeer naar Internet">
</p>

*Afbeelding: Virtual Network NAT voor uitgaand verkeer naar Internet*

>[!NOTE] 
>Virtual Network NAT is op dit moment beschikbaar als open bare preview. Het is momenteel alleen beschikbaar in een beperkt aantal [regio's](nat-overview.md#region-availability). Deze preview is beschikbaar zonder service level agreement en wordt niet aanbevolen voor productie werkbelastingen. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

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

- Meer informatie over [Virtual Network NAT](nat-overview.md)
- Meer informatie over de [NAT gateway-resource](nat-gateway-resource.md)
- Meer informatie over [Azure monitor](../azure-monitor/overview.md)
