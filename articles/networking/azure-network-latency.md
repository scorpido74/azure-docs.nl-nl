---
title: Azure Network-latentie van round-trip-statistieken | Microsoft Docs
description: Meer informatie over de latentie statistieken voor de retour tussen Azure-regio's.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 01/08/2020
ms.author: mnayak
ms.openlocfilehash: 91b528cc6900a3ec91ff7189f58f941226b8acd5
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779719"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistieken voor round-trip-latentie van Azure Network

Azure bewaakt voortdurend de latentie (snelheid) van de kern gebieden van het netwerk met behulp van interne controle hulpprogramma's en metingen die door [ThousandEyes](https://thousandeyes.com)worden verzameld, een synthetische bewakings service van derden.

## <a name="how-are-the-measurements-collected"></a>Hoe worden de metingen verzameld?

De latentie metingen worden verzameld van ThousandEyes-agents die wereld wijd worden gehost in azure-Cloud regio's, waardoor voortdurend netwerk tests tussen zichzelf worden verzonden binnen een interval van 1 minuut. De statistieken voor de maandelijkse latentie worden afgeleid van de verzamelde voor beelden voor de maand.

## <a name="december-2019-latency-figures"></a>Cijfers van december 2019-latentie

De maandelijkse gemiddelde retour tijden tussen Azure-regio's voor de afgelopen 30 dagen (eindigend op 31 december 2019) worden hieronder weer gegeven. De volgende metingen worden aangedreven door [ThousandEyes](https://thousandeyes.com).

[![latentie statistieken voor de Inter-regio van Azure](media/azure-network-latency/december.jpg)](media/azure-network-latency/december.jpg#lightbox)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).
