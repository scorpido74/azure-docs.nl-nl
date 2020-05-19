---
title: bestand opnemen
ms.topic: include
ms.custom: include file
ms.date: 5/04/2020
ms.openlocfilehash: 819dfa3127eb91a2f08687a76ea0586439b650a4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590980"
---
|Niveau|Gebruikers interface-instelling|API-instelling|Informatie|
|--|--|--|--|
|App|Eind punten openbaar maken|`Public`|Iedereen kan toegang krijgen tot uw open bare app als ze een Voorspellings sleutel hebben en uw app-ID kennen. |
|Versie|Niet-deterministische training gebruiken|`UseAllTrainingData`|Training maakt gebruik van een klein percentage van negatieve steek proeven. Als u alle gegevens wilt gebruiken in plaats van de kleine negatieve steek proef, stelt u in op `true` . |
|Versie|Diakritische tekens normaliseren|`NormalizeDiacritics`|Als diakritische tekens worden genormaliseerd, worden de tekens vervangen door accenten in uitingen.|
|Versie|Interpunctie normaliseren|`NormalizePunctuation`|Het normaliseren van interpunctie betekent dat voordat uw modellen worden getraind en voordat uw eindpunt query's worden voor speld, wordt interpunctie verwijderd uit de uitingen.|
|Versie|Woord vormen normaliseren|`NormalizeWordForm`|Word-formulieren buiten het hoofd woord negeren.|

Leer [concepten](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) van normalisatie en hoe u [app](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58aeface39e2bb03dcd5909e) -en [versie](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) -api's kunt gebruiken om deze instellingen bij te werken of de Luis-portal te gebruiken **, de pagina** **Toepassings instellingen** .