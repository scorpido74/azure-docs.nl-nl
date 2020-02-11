---
title: Azure Network-retour latentie-statistieken | Microsoft Docs
description: Meer informatie over de statistieken voor de retour latentie tussen Azure-regio's.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 02/10/2020
ms.author: kumud
ms.openlocfilehash: dc2cb6545093e8ab70d18014a5fd9d0e7d36f8b7
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122389"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistieken voor de retour latentie van Azure Network

Azure bewaakt voortdurend de latentie (snelheid) van de kern gebieden van het netwerk met behulp van interne controle hulpprogramma's en metingen die door [ThousandEyes](https://thousandeyes.com)worden verzameld, een synthetische bewakings service van derden.

## <a name="how-are-the-measurements-collected"></a>Hoe worden de metingen verzameld?

De latentie metingen worden verzameld van ThousandEyes-agents die wereld wijd worden gehost in azure-Cloud regio's, waardoor voortdurend netwerk tests tussen zichzelf worden verzonden binnen een interval van 1 minuut. De statistieken voor de maandelijkse latentie worden afgeleid van de verzamelde voor beelden voor de maand.

## <a name="january-2020-round-trip-latency-figures"></a>Cijfers van januari 2020-retour latentie

De maandelijkse gemiddelde retour tijden tussen Azure-regio's voor de afgelopen 31 dagen (eindigend op 31 januari 2020) worden hieronder weer gegeven. De volgende metingen worden aangedreven door [ThousandEyes](https://thousandeyes.com).

[![latentie statistieken voor de Inter-regio van Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).
