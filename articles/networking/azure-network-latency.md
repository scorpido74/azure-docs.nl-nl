---
title: Azure Network-retour latentie-statistieken | Microsoft Docs
description: Meer informatie over de statistieken voor de retour latentie tussen Azure-regio's.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 05/26/2020
ms.author: kumud
ms.openlocfilehash: 8cf5d07bb071217a5ecafca8f351c94590291603
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994001"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistieken voor de retour latentie van Azure Network

Azure bewaakt voortdurend de latentie (snelheid) van de kern gebieden van het netwerk met behulp van interne controle hulpprogramma's en metingen die door [ThousandEyes](https://thousandeyes.com)worden verzameld, een synthetische bewakings service van derden.

## <a name="how-are-the-measurements-collected"></a>Hoe worden de metingen verzameld?

De latentie metingen worden verzameld van ThousandEyes-agents die wereld wijd worden gehost in azure-Cloud regio's, waardoor voortdurend netwerk tests tussen zichzelf worden verzonden binnen een interval van 1 minuut. De statistieken voor de maandelijkse latentie worden afgeleid van de verzamelde voor beelden voor de maand.

## <a name="april-2020-round-trip-latency-figures"></a>Cijfers voor de retour latentie van april 2020

De maandelijkse gemiddelde retour tijden tussen Azure-regio's voor de afgelopen 30 dagen (eindigend op 30 april 2020) worden hieronder weer gegeven. De volgende metingen worden aangedreven door [ThousandEyes](https://thousandeyes.com).

[![Latentie statistieken tussen Azure-regio's](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).
