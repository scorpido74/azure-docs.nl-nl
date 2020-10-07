---
title: SKU's en prijzen voor Cognitive Services
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: cb066ecc1dc11985c0ccb0fa687a15d6b038b9fa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89321559"
---
Bekijk de onderstaande lijst met SKU's en prijsinformatie. 

#### <a name="multi-service"></a>Multi-service

| Service                    | Soort                      |
|----------------------------|---------------------------|
| Meerdere services. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/) voor meer informatie.            | `CognitiveServices`     |


#### <a name="vision"></a>Vision

| Service                    | Soort                      |
|----------------------------|---------------------------|
| Computer Vision            | `ComputerVision`          |
| Custom Vision - Voorspelling | `CustomVision.Prediction` |
| Custom Vision - Training   | `CustomVision.Training`   |
| Face                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

#### <a name="search"></a>Search

| Service            | Soort                  |
|--------------------|-----------------------|
| Bing Automatische suggesties   | `Bing.Autosuggest.v7` |
| Bing Aangepaste zoekopdrachten | `Bing.CustomSearch`   |
| Bing Entiteiten zoeken | `Bing.EntitySearch`   |
| Bing Zoeken        | `Bing.Search.v7`      |
| Bing Spellingcontrole   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Speech

| Service            | Soort                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| Spraakherkenning | `SpeakerRecognition` |

#### <a name="language"></a>Taal

| Service            | Soort                |
|--------------------|---------------------|
| Informatie over formulier | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Tekstanalyse     | `TextAnalytics`     |
| Tekstomzetting   | `TextTranslation`   |

#### <a name="decision"></a>Besluit

| Service           | Soort               |
|-------------------|--------------------|
| Anomaly Detector  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Prijscategorieën en facturering

Prijscategorieën (en het bedrag dat in rekening wordt gebracht) zijn gebaseerd op het aantal transacties dat u verzendt met behulp van uw verificatiegegevens. Voor elke prijsklasse wordt het volgende gespecificeerd:
* het maximumaantal toegestane transacties per seconde (TPS).
* servicefuncties die zijn ingeschakeld binnen de prijscategorie.
* kosten voor een vooraf gedefinieerd aantal transacties. Als u boven dit aantal uitkomt, worden er extra kosten in rekening gebracht zoals gespecificeerd in de [prijsinformatie](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) voor uw service.

> [!NOTE]
> Veel van de Cognitive Services hebben een gratis laag die u kunt gebruiken om de service te proberen. Als u de gratis laag wilt gebruiken, gebruikt u `F0` als de SKU voor uw resource.