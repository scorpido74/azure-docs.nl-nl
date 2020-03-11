---
title: Azure Network-retour latentie-statistieken | Microsoft Docs
description: Meer informatie over de statistieken voor de retour latentie tussen Azure-regio's.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: 3469daa8b81b20d5d0052a23ce3236fc4fde75de
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082932"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistieken voor de retour latentie van Azure Network

Azure bewaakt voortdurend de latentie (snelheid) van de kern gebieden van het netwerk met behulp van interne controle hulpprogramma's en metingen die door [ThousandEyes](https://thousandeyes.com)worden verzameld, een synthetische bewakings service van derden.

## <a name="how-are-the-measurements-collected"></a>Hoe worden de metingen verzameld?

De latentie metingen worden verzameld van ThousandEyes-agents die wereld wijd worden gehost in azure-Cloud regio's, waardoor voortdurend netwerk tests tussen zichzelf worden verzonden binnen een interval van 1 minuut. De statistieken voor de maandelijkse latentie worden afgeleid van de verzamelde voor beelden voor de maand.

## <a name="february-2020-round-trip-latency-figures"></a>Cijfers voor de retour latentie van februari 2020

De maandelijkse gemiddelde retour tijden tussen Azure-regio's gedurende de afgelopen 29 dagen (eindigend op 29 februari 2020) worden hieronder weer gegeven. De volgende metingen worden aangedreven door [ThousandEyes](https://thousandeyes.com).

[![latentie statistieken voor de Inter-regio van Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).
