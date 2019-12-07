---
title: Azure Network-latentie van round-trip-statistieken | Microsoft Docs
description: Meer informatie over de latentie statistieken voor de retour tussen Azure-regio's.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 12/04/2019
ms.author: mnayak
ms.openlocfilehash: 3947df81b67d5aefc1b628b6ddaf8275152a4cd3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893079"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistieken voor round-trip-latentie van Azure Network

Azure bewaakt voortdurend de latentie (snelheid) van de kern gebieden van het netwerk met behulp van interne controle hulpprogramma's en metingen die door [ThousandEyes](https://thousandeyes.com)worden verzameld, een synthetische bewakings service van derden.

## <a name="how-are-the-measurements-collected"></a>Hoe worden de metingen verzameld?

De latentie metingen worden verzameld van ThousandEyes-agents die wereld wijd worden gehost in azure-Cloud regio's, waardoor voortdurend netwerk tests tussen zichzelf worden verzonden binnen een interval van 1 minuut. De statistieken voor de maandelijkse latentie worden afgeleid van de verzamelde voor beelden voor de maand.

## <a name="november-2019-latency-figures"></a>Cijfers van november 2019-latentie

**Update van november:** Er zijn drie regio's toegevoegd.

* Noorwegen - oost
* Noorwegen - west
* Australië

De maandelijkse gemiddelde retour tijden tussen Azure-regio's voor de afgelopen 30 dagen (eindigend op 30 november 2019) worden hieronder weer gegeven. De volgende metingen worden aangedreven door [ThousandEyes](https://thousandeyes.com).

![Latentie statistieken tussen Azure-regio's](media/azure-network-latency/latency-nov-2019.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).
