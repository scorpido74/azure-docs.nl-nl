---
title: Wat is een BLEU-Score? -Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: BLEU is een meting van de verschillen tussen machine vertalingen en door de mens gemaakte referentie vertalingen van dezelfde bron zin.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 85c4ee27a828a05c64ca6cbf84bff438535328be
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647361"
---
# <a name="what-is-a-bleu-score"></a>Wat is een BLEU-Score?

[Bleu (tweetalige evaluatie-onderstudie)](https://en.wikipedia.org/wiki/BLEU) is een meting van de verschillen tussen een automatische vertaling en een of meer door de mens gemaakte referentie vertalingen van dezelfde bron zin.

## <a name="scoring-process"></a>Score proces

Het BLEU-algoritme vergelijkt opeenvolgende zinnen van de automatische vertaling met de opeenvolgende woord groepen die worden gevonden in de referentie conversie en telt het aantal overeenkomsten op een gewogen manier. Deze overeenkomst is onafhankelijk van positie. Een hogere nauw keurigheid duidt op een hogere mate van gelijkenis met de referentie vertaling en een hogere score. Er wordt geen rekening gehouden met intelligibility en grammatica correctie.

## <a name="how-bleu-works"></a>Hoe BLEU werkt?

De sterkte van BLEU is dat deze goed wordt afgestemd op de beslissing van de mens door het berekenen van de afzonderlijke beslissings fouten van de zinnen ten opzichte van een test verzameling, in plaats van dat er wordt geprobeerd om voor elke zin de exacte menselijke beslissing te nemen.

[Hier volgt](https://youtu.be/-UqDljMymMg)een uitgebreidere bespreking van Bleu-scores.

BLEU resultaten zijn sterk afhankelijk van de breedte van uw domein, de consistentie van de test gegevens met de training en het afstemmen van gegevens en de hoeveelheid gegevens die u beschikbaar hebt om te trainen. Als uw modellen zijn getraind op een beperkt domein en uw trainings gegevens consistent zijn met uw test gegevens, kunt u een hoge BLEU-Score verwachten.

>[!NOTE]
>Een vergelijking tussen BLEU-scores is alleen verantwoord wanneer BLEU-resultaten worden vergeleken met dezelfde testset, hetzelfde taal paar en dezelfde MT-engine. Een BLEU-Score van een andere testset is gebonden aan een andere set.
