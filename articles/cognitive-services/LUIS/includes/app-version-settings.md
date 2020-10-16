---
title: bestand opnemen
ms.topic: include
ms.custom: include file
ms.date: 5/17/2020
ms.openlocfilehash: 79a45825be66b44d869ef741917436de87a1f5d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83653202"
---
Meer informatie over [concepten](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) van normalisering en hoe u [versie](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)-API's kunt gebruiken om deze instellingen bij te werken of hoe u de sectie **Beheren** op de pagina **Instellingen** op de LUIS-portal kunt gebruiken.


|Gebruikersinterface-instelling|API-instelling|Informatie|
|--|--|--|
|Niet-deterministische training gebruiken|`UseAllTrainingData`|Voor de training wordt een klein percentage negatieve steekproeven gebruikt. Als u alle gegevens wilt gebruiken in plaats van de kleine negatieve steekproef, moet u de instelling `true` gebruiken. |
|Diakritische tekens normaliseren|`NormalizeDiacritics`|Als diakritische tekens worden genormaliseerd, worden de uitingen in de vorm van tekens met diakritische tekens vervangen door normale tekens. Deze instelling is alleen beschikbaar voor [talen](../luis-reference-application-settings.md#diacritics-normalization) die ondersteuning bieden voor diakritische tekens.|
|Interpunctie normaliseren|`NormalizePunctuation`|Het normaliseren van interpunctie betekent dat, voordat uw modellen worden getraind en voordat uw eindpuntquery's worden voorspeld, interpunctie uit uitingen worden verwijderd.|
|Woordvormen normaliseren|`NormalizeWordForm`|Andere woordvormen dan de stam negeren.|
