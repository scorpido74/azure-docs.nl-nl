---
title: Wat is een BLEU score? - Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: BLEU is een meting van de verschillen tussen machinevertaling en door de mens gemaakte referentievertalingen van dezelfde bronzin.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 85c4ee27a828a05c64ca6cbf84bff438535328be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647361"
---
# <a name="what-is-a-bleu-score"></a>Wat is een BLEU score?

[BLEU (Bilingual Evaluation Understudy)](https://en.wikipedia.org/wiki/BLEU) is een meting van de verschillen tussen een automatische vertaling en een of meer door mensen gemaakte referentievertalingen van dezelfde bronzin.

## <a name="scoring-process"></a>Scoreproces

Het BLEU-algoritme vergelijkt opeenvolgende zinnen van de automatische vertaling met de opeenvolgende zinnen die het in de referentievertaling vindt en telt het aantal overeenkomsten op een gewogen manier. Deze overeenkomsten zijn positieonafhankelijk. Een hogere matchgraad duidt op een hogere mate van gelijkenis met de referentievertaling en een hogere score. Er wordt geen rekening gehouden met verstaanbaarheid en grammaticale correctheid.

## <a name="how-bleu-works"></a>Hoe werkt BLEU?

Bleu's kracht is dat het goed correleert met het menselijk oordeel door het gemiddelde van individuele zin oordeel fouten over een test corpus, in plaats van te proberen om de exacte menselijke oordeel voor elke zin te bedenken.

Een uitgebreidere bespreking van BLEU scores is [hier](https://youtu.be/-UqDljMymMg).

Bleu-resultaten zijn sterk afhankelijk van de breedte van uw domein, de consistentie van de testgegevens met de trainings- en tuninggegevens en hoeveel gegevens u beschikbaar hebt om te trainen. Als uw modellen zijn getraind op een smal domein en uw trainingsgegevens consistent zijn met uw testgegevens, u een hoge BLEU-score verwachten.

>[!NOTE]
>Een vergelijking tussen BLEU scores is alleen gerechtvaardigd wanneer BLEU resultaten worden vergeleken met dezelfde Test set, dezelfde taal paar, en dezelfde MT-engine. Een BLEU score van een andere testset zal zeker anders zijn.
