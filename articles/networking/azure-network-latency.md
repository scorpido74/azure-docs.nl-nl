---
title: Azure Network-latentie van round-trip-statistieken | Microsoft Docs
description: Meer informatie over de latentie statistieken voor de retour tussen Azure-regio's.
services: networking
documentationcenter: na
author: nayak-mahesh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/04/2019
ms.author: mnayak
ms.openlocfilehash: 500676983233f943fdc9638d75758645dee65564
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587587"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistieken voor round-trip-latentie van Azure Network

Azure bewaakt voortdurend de latentie (snelheid) van de kern gebieden van het netwerk met behulp van interne controle hulpprogramma's en metingen die door [ThousandEyes](https://thousandeyes.com)worden verzameld, een synthetische bewakings service van derden.

## <a name="how-are-the-measurements-collected"></a>Hoe worden de metingen verzameld?

De latentie metingen worden verzameld van ThousandEyes-agents die worden gehost in azure-Cloud regio's, waardoor voortdurend netwerk tests tussen zichzelf worden verzonden, in intervallen van 1 minuut. De statistieken voor de maandelijkse latentie worden afgeleid van de verzamelde voor beelden voor de maand.

## <a name="october-2019-latency-figures"></a>Cijfers van oktober 2019-latentie

Gedurende 31 dagen eindigend op 31 oktober 2019, worden de maandelijkse minimum-en maximum latentie tijden binnen geaggregeerde regio's:

- **5 MS** tot **72 MS** voor retouren binnen **Noord-Amerika** regio's.
- **3 MS** tot **28 MS** voor retouren binnen **Europa** regio's.
- **4 MS** tot **134 MS** voor retouren binnen **Azië** regio's.

De volgende latentie metingen tussen regio's worden aangedreven door [ThousandEyes](https://thousandeyes.com). De maat eenheid in de onderstaande tabel is in milliseconden (MS).

![Latentie statistieken tussen Azure-regio's](media/azure-network-latency/azure-inter-region-latency.png)


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).