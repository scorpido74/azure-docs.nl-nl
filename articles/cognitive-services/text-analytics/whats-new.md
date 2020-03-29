---
title: Nieuwe nieuws in de Text Analytics API
titleSuffix: Text Analytics - Azure Cognitive Services
description: In dit artikel vindt u informatie over nieuwe releases en functies voor de Azure Cognitive Services Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77188809"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Wat is er nieuw in de Text Analytics-API?

De Text Analytics API wordt voortdurend bijgewerkt. Om op de hoogte te blijven van recente ontwikkelingen, biedt dit artikel u informatie over nieuwe releases en functies.

## <a name="february-2020"></a>Februari 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>SDK-ondersteuning voor Text Analytics API v3 Public Preview

Als onderdeel van de [uniforme Azure SDK-release](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)is de Text Analytics API v3 SDK nu beschikbaar als openbare preview voor de volgende programmeertalen:
   * [C #](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [Meer informatie over Text Analytics API v3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Met de naam entiteitsherkenning v3 openbare preview

Aanvullende entiteitstypen zijn nu beschikbaar in de NER-v3-openbare preview-service (Named Entity Recognition) terwijl we de detectie van algemene en persoonlijke informatieentiteiten in tekst uitbreiden. Deze update introduceert [modelversie,](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`die omvat:

* Erkenning van de volgende algemene entiteitstypen (alleen In het Engels):
    * PersonType
    * Product
    * Gebeurtenis
    * Geopolitieke entiteit (GPE) als subtype onder Locatie
    * Behendigheid

* Erkenning van de volgende typen persoonlijke gegevens (alleen In het Engels):
    * Person
    * Organisatie
    * Leeftijd als subtype onder Hoeveelheid
    * Datum als subtype onder DateTime
    * Email 
    * Telefoonnummer (alleen in de VS)
    * URL
    * IP-adres

> [!div class="nextstepaction"]
> [Meer informatie over Named Entity Recognition v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Oktober 2019

#### <a name="named-entity-recognition-ner"></a>Benoemde entiteitsherkenning (NER)

* Een [nieuw eindpunt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) voor het herkennen van entiteitstypen voor persoonlijke gegevens (alleen In het Engels)

* Afzonderlijke eindpunten voor [entiteitsherkenning](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) en [entiteitskoppeling](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Modelversie,](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`waaronder:
    * Uitgebreide detectie en categorisering van entiteiten in tekst. 
    * Erkenning van de volgende nieuwe entiteitstypen:
        * Telefoonnummer
        * IP-adres

Entiteitskoppeling ondersteunt Engels en Spaans. Ner-taalondersteuning verschilt per entiteitstype.

#### <a name="sentiment-analysis-v3-public-preview"></a>Sentiment Analyse v3 openbare preview

* Een [nieuw eindpunt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) voor het analyseren van sentiment.
* [Modelversie,](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`waaronder:

    * Aanzienlijke verbeteringen in de nauwkeurigheid en details van de tekstcategorisering en -score van de API.
    * Automatische etikettering voor verschillende sentimenten in tekst.
    * Sentimentanalyse en output op document- en zinsniveau. 

Het ondersteunt`en`Engels (`ja`), Japans`zh-Hans`( ),`zh-Hant`Chinese Vereenvoudigde`fr`(`it`), Chinese`es`Traditionele`nl`( ),`pt`Franse (`de`), Italiaanse ( ), `Australia East`Spaanse `Central Canada` `Central US`( `East Asia` `East US`), Nederlandse ( ), Portugese ( ) en Duitse ( ), en is beschikbaar in de volgende regio's: , , , , `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, , `UK South`, `West Europe`, en `West US 2`. 

> [!div class="nextstepaction"]
> [Meer informatie over Sentiment Analysis v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Volgende stappen

* [Wat is Text Analytics-API?](overview.md)  
* [Voorbeeldgebruikerscenario's](text-analytics-user-scenarios.md)
* [Sentimentanalyse](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Taaldetectie](how-tos/text-analytics-how-to-language-detection.md)
* [Herkenning van entiteiten](how-tos/text-analytics-how-to-entity-linking.md)
* [Trefwoordextractie](how-tos/text-analytics-how-to-keyword-extraction.md)
