---
title: Wat is er nieuw in de Text Analytics-API
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
ms.openlocfilehash: e3fbff7a5919d4d43656f3112cb24f2017191348
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086799"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Wat is er nieuw in de Text Analytics-API?

De Text Analytics-API wordt doorlopend bijgewerkt. In dit artikel vindt u informatie over nieuwe releases en functies, zodat u op de hoogte blijft van recente ontwikkelingen.

## <a name="named-entity-recognition-v3-public-preview---february-2020"></a>Named entity Recognition v3 Public Preview-februari 2020

Er zijn nu extra entiteits typen beschikbaar in de open bare preview-service van de NER (named entity Recognition) omdat we de detectie van algemene en persoonlijke informatie entiteiten in tekst kunnen uitbreiden. Deze update introduceert de [model versie](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`, met inbegrip van:

* Erkenning van de volgende algemene entiteits typen (alleen Engels):
    * PersonType
    * Product
    * Gebeurtenis
    * Geopolitieke entiteit (GPE) als subtype onder locatie
    * Eigen

* Herkenning van de volgende entiteits typen van persoonlijke gegevens (alleen Engels):
    * Person
    * Organisatie
    * Leeftijd als subtype onder hoeveelheid
    * Datum als een subtype onder DateTime
    * Email 
    * Telefoon nummer (alleen VS)
    * URL
    * IP-adres

> [!div class="nextstepaction"]
> [Meer informatie over named entity Recognition v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Oktober 2019

#### <a name="named-entity-recognition-ner"></a>Herkenning van benoemde entiteiten (NER)

* Een [Nieuw eind punt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) voor het herkennen van entiteits typen van persoonlijke gegevens (alleen Engels)

* Afzonderlijke eind punten voor [entiteits herkenning](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) en [entiteits koppeling](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Model versie](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`, met inbegrip van:
    * Uitgebreide detectie en categorisatie van entiteiten gevonden in tekst. 
    * Herkenning van de volgende nieuwe entiteits typen:
        * Telefoonnummer
        * IP-adres

Koppeling van entiteit ondersteunt Engels en Spaans. NER taal ondersteuning varieert per entiteits type.

#### <a name="sentiment-analysis-v3-public-preview"></a>Open bare preview van Sentimentanalyse v3

* Een [Nieuw eind punt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) voor het analyseren van sentiment.
* [Model versie](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`, met inbegrip van:

    * Aanzienlijke verbeteringen in de nauw keurigheid en Details van de tekst categorisatie en Score van de API.
    * Automatische labeling voor verschillende gevoel in tekst.
    * Sentiment analyse en uitvoer op het niveau van een document en zin. 

Het biedt ondersteuning voor Engels (`en`), Japans (`ja`) vereenvoudigd Chinees (`zh-Hans`), traditioneel Chinees (`zh-Hant`), Frans (`fr`), Italiaans (`it`), Spaans (`es`), Nederlands (`nl`), Portugees (`pt`) en Duits (`de`), en is beschikbaar in de volgende regio's: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`en `West US 2`. 

> [!div class="nextstepaction"]
> [Meer informatie over Sentimentanalyse v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Volgende stappen

* [Wat is de Text Analytics-API?](overview.md)  
* [Voor beelden van gebruikers scenario's](text-analytics-user-scenarios.md)
* [Sentiment analyse](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Taal detectie](how-tos/text-analytics-how-to-language-detection.md)
* [Entiteit herkenning](how-tos/text-analytics-how-to-entity-linking.md)
* [Extractie van sleutel woorden](how-tos/text-analytics-how-to-keyword-extraction.md)
