---
title: Concepten voor gezichtsherkenning
titleSuffix: Azure Cognitive Services
description: In dit artikel worden de concepten van de bewerkingen Verifiëren, Vergelijkbare gegevens, Groeperen en Gezichtsherkenning en de onderliggende gegevensstructuren worden uitgelegd.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 164e5a8c107f445b376d26f9be7db92a7983b0d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743074"
---
# <a name="face-recognition-concepts"></a>Concepten voor gezichtsherkenning

In dit artikel worden de concepten van de bewerkingen Verifiëren, Vergelijkbare gegevens, Groeperen en Gezichtsherkenning en de onderliggende gegevensstructuren worden uitgelegd. In grote lijnen beschrijft herkenning het werk van het vergelijken van twee verschillende gezichten om te bepalen of ze vergelijkbaar zijn of behoren tot dezelfde persoon.

## <a name="recognition-related-data-structures"></a>Herkenningsgerelateerde gegevensstructuren

De herkenningsbewerkingen gebruiken voornamelijk de volgende gegevensstructuren. Deze objecten worden opgeslagen in de cloud en kunnen worden verwezen door hun ID-tekenreeksen. ID-strings zijn altijd uniek binnen een abonnement. Naamvelden kunnen worden gedupliceerd.

|Name|Beschrijving|
|:--|:--|
|GedetecteerdGezicht| Deze enkele gezichtsweergave wordt opgehaald door de [gezichtsherkenningsbewerking.](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) De ID verloopt 24 uur nadat deze is gemaakt.|
|PersistedFace PersistedFace| Wanneer Gedetecteerde Face-objecten aan een groep worden toegevoegd, zoals FaceList of Persoon, worden ze PersistedFace-objecten. Ze kunnen op elk gewenst moment worden [opgehaald](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) en verlopen niet.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) of [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Deze gegevensstructuur is een diverse lijst met PersistedFace-objecten. Een FaceList heeft een unieke ID, een naamtekenreeks en optioneel een tekenreeks voor gebruikersgegevens.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Deze gegevensstructuur is een lijst met PersistedFace-objecten die van dezelfde persoon zijn. Het heeft een unieke ID, een naamtekenreeks en optioneel een tekenreeks voor gebruikersgegevens.|
|[Persoonsgroep](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) of [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Deze gegevensstructuur is een diverse lijst met persoonsobjecten. Het heeft een unieke ID, een naamtekenreeks en optioneel een tekenreeks voor gebruikersgegevens. Een persoonsgroep moet worden [opgeleid](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) voordat deze kan worden gebruikt in herkenningsbewerkingen.|

## <a name="recognition-operations"></a>Erkenningsoperaties

In dit gedeelte wordt beschreven hoe de vier herkenningsbewerkingen de eerder beschreven gegevensstructuren gebruiken. Zie [Overzicht](../Overview.md)voor een brede beschrijving van elke herkenningsbewerking.

### <a name="verify"></a>Verifiëren

Met de bewerking [Verifiëren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) wordt een face-id van DetectedFace of PersistedFace en een ander face-id of een persoonsobject genomen en wordt bepaald of deze van dezelfde persoon zijn. Als u een persoonsobject doorgeeft, u optioneel in een persoonsgroep worden doorgegeven waartoe die persoon behoort om de prestaties te verbeteren.

### <a name="find-similar"></a>Vergelijkbare zoeken

De bewerking [Vergelijkbare zoeken](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) neemt een face-id van DetectedFace of PersistedFace en een FaceList of een reeks andere gezichts-id's. Met een FaceList retourneert het een kleinere FaceList met gezichten die vergelijkbaar zijn met het opgegeven gezicht. Met een array van gezichts-id's, het op dezelfde manier retourneert een kleinere array.

### <a name="group"></a>Groep

De [groepsbewerking](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) neemt een array van diverse gezichts-id's van DetectedFace of PersistedFace en retourneert dezelfde id's gegroepeerd in verschillende kleinere arrays. Elke array met 'groepen' bevat gezichts-id's die op elkaar lijken. Een enkele "messyGroup" array bevat face ID's waarvoor geen overeenkomsten werden gevonden.

### <a name="identify"></a>Identificeren

De bewerking [Identificeren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) neemt een of meer gezichts-id's van GedetecteerdGezicht of PersistedFace en een persoonsgroep en retourneert een lijst met persoonsobjecten waartoe elk gezicht mogelijk behoort. Geretourneerde persoonsobjecten worden verpakt als kandidaat-objecten, die een voorspellingsbetrouwbaarheid hebben.

## <a name="input-data"></a>Invoergegevens

Gebruik de volgende tips om ervoor te zorgen dat uw invoerafbeeldingen de meest nauwkeurige herkenningsresultaten geven:

* De ondersteunde invoerafbeeldingsindelingen zijn JPEG, PNG, GIF (het eerste frame), BMP.
* De grootte van het afbeeldingsbestand mag niet groter zijn dan 4 MB.
* Wanneer u Persoonsobjecten maakt, gebruikt u foto's met verschillende soorten hoeken en verlichting.
* Sommige gezichten worden mogelijk niet herkend vanwege technische uitdagingen, zoals:
  * Beelden met extreme verlichting, bijvoorbeeld, ernstige achtergrondverlichting.
  * Obstakels die een of beide ogen blokkeren.
  * Verschillen in haartype of gezichtshaar.
  * Veranderingen in het uiterlijk van het gezicht als gevolg van leeftijd.
  * Extreme gezichtsuitdrukkingen.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met concepten voor gezichtsherkenning, leert u hoe u een script schrijft dat gezichten identificeert met een getrainde persoonsgroep.

* [Gezichten in afbeeldingen identificeren](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)