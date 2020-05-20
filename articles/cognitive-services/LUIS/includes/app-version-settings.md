---
title: bestand opnemen
ms.topic: include
ms.custom: include file
ms.date: 5/17/2020
ms.openlocfilehash: 79a45825be66b44d869ef741917436de87a1f5d0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653202"
---
Leer [concepten](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) van normalisatie en hoe u [versie](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) -api's kunt gebruiken om deze instellingen bij te werken of door de sectie **instellingen** **van de Luis** -portal te gebruiken.


|Gebruikers interface-instelling|API-instelling|Informatie|
|--|--|--|
|Niet-deterministische training gebruiken|`UseAllTrainingData`|Training maakt gebruik van een klein percentage van negatieve steek proeven. Als u alle gegevens wilt gebruiken in plaats van de kleine negatieve steek proef, stelt u in op `true` . |
|Diakritische tekens normaliseren|`NormalizeDiacritics`|Als diakritische tekens worden genormaliseerd, worden de tekens vervangen door accenten in uitingen. Deze instelling is alleen beschikbaar voor [talen](../luis-reference-application-settings.md#diacritics-normalization) die diakritische tekens ondersteunen.|
|Interpunctie normaliseren|`NormalizePunctuation`|Het normaliseren van interpunctie betekent dat voordat uw modellen worden getraind en voordat uw eindpunt query's worden voor speld, wordt interpunctie verwijderd uit de uitingen.|
|Woord vormen normaliseren|`NormalizeWordForm`|Word-formulieren buiten het hoofd woord negeren.|
