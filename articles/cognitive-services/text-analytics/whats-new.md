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
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: f2ce8f300ae7586fc16fd9170ec290b19875d01c
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371121"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Wat is er nieuw in de Text Analytics-API?

De Text Analytics-API wordt doorlopend bijgewerkt. In dit artikel vindt u informatie over nieuwe releases en functies, zodat u op de hoogte blijft van recente ontwikkelingen.

## <a name="july-2020"></a>Juli 2020

### <a name="text-analytics-for-health-container---public-gated-preview"></a>Text Analytics voor de status container-Public gated preview

De Text Analytics voor de status container bevindt zich nu in Public gated preview, waarmee u informatie kunt ophalen uit ongestructureerde tekst in het Engels in klinische documenten, zoals: patiënten-formulieren, notities van dokters, onderzoek documenten en kwijtings overzichten. Er wordt momenteel geen kosten in rekening gebracht voor Text Analytics voor het gebruik van de status container. 

De container biedt de volgende functies:

* Herkenning van benoemde entiteiten
* Relatie-extractie
* Entiteit koppelen
* Negatie


> [!div class="nextstepaction"]
> [Meer informatie over Text Analytics voor de status container](how-tos/text-analytics-for-health.md)

## <a name="may-2020"></a>Mei 2020

### <a name="text-analytics-api-v3-general-availability"></a>Text Analytics-API v3 algemene Beschik baarheid

De tekst analyse-API v3 is nu algemeen beschikbaar met de volgende updates:

* Model versie`2020-04-01`
* Nieuwe [gegevens limieten](concepts/data-limits.md) voor elke functie
* Bijgewerkte [taal ondersteuning](language-support.md) voor [sentimentanalyse (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md)
* Afzonderlijk eind punt voor entiteits koppeling 
* Nieuwe "adres" entiteits categorie in [named entity Recognition (ner) v3](how-tos/text-analytics-how-to-entity-linking.md).
* Nieuwe subcategorieën in NER V3:
   * Locatie-geografische
   * Locatie-structureel
   * Organisatie-aandelen beurs
   * Organisatie-medisch
   * Organisatie-sporten
   * Gebeurtenis-cultureel
   * Gebeurtenis-natuurlijk
   * Evenement-sporten

De volgende eigenschappen in het JSON-antwoord zijn toegevoegd:
   * `SentenceText`in Sentimentanalyse
   * `Warnings`voor elk document 

De namen van de volgende eigenschappen in het JSON-antwoord zijn gewijzigd, indien van toepassing:

* De naam van `score` is gewijzigd in `confidenceScore`
    * `confidenceScore`heeft twee decimale punten nauw keurigheid. 
* De naam van `type` is gewijzigd in `category`
* De naam van `subtype` is gewijzigd in `subcategory`

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [Meer informatie over Text Analytics-API v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Text Analytics-API v 3.1 open bare preview
   * Nieuwe Sentimentanalyse functie: [opinie analyse](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Nieuw [persoonlijk ( `PII` ) domein filter](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) voor beveiligde status informatie ( `PHI` ).

> [!div class="nextstepaction"]
> [Meer informatie over Text Analytics-API v 3.1 Preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Februari 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>SDK-ondersteuning voor de open bare preview van Text Analytics-API v3

Als onderdeel van de [Unified Azure SDK-versie](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)is de Text Analytics-API v3 SDK nu beschikbaar als open bare Preview voor de volgende programmeer talen:
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)
   
   > [!div class="nextstepaction"]
> [Meer informatie over Text Analytics-API v3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Named entity Recognition v3 open bare preview

Er zijn nu extra entiteits typen beschikbaar in de open bare preview-service van de NER (named entity Recognition) omdat we de detectie van algemene en persoonlijke informatie entiteiten in tekst kunnen uitbreiden. Deze update introduceert [model versie](concepts/model-versioning.md) `2020-02-01` , waaronder:

* Erkenning van de volgende algemene entiteits typen (alleen Engels):
    * PersonType
    * Product
    * Gebeurtenis
    * Geopolitieke entiteit (GPE) als subtype onder locatie
    * Vaardigheid

* Herkenning van de volgende entiteits typen van persoonlijke gegevens (alleen Engels):
    * Person
    * Organisatie
    * Leeftijd als subtype onder hoeveelheid
    * Datum als een subtype onder DateTime
    * E-mail 
    * Telefoon nummer (alleen VS)
    * URL
    * IP-adres

> [!div class="nextstepaction"]
> [Meer informatie over named entity Recognition v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Oktober 2019

#### <a name="named-entity-recognition-ner"></a>Herkenning van benoemde entiteiten (NER)

* Een [Nieuw eind punt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) voor het herkennen van entiteits typen van persoonlijke gegevens (alleen Engels)

* Afzonderlijke eind punten voor [entiteits herkenning](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) en [entiteits koppeling](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Model versie](concepts/model-versioning.md) `2019-10-01` , waaronder:
    * Uitgebreide detectie en categorisatie van entiteiten gevonden in tekst. 
    * Herkenning van de volgende nieuwe entiteits typen:
        * Telefoonnummer
        * Het IP-adres

Koppeling van entiteit ondersteunt Engels en Spaans. NER taal ondersteuning varieert per entiteits type.

#### <a name="sentiment-analysis-v3-public-preview"></a>Open bare preview van Sentimentanalyse v3

* Een [Nieuw eind punt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) voor het analyseren van sentiment.
* [Model versie](concepts/model-versioning.md) `2019-10-01` , waaronder:

    * Aanzienlijke verbeteringen in de nauw keurigheid en Details van de tekst categorisatie en Score van de API.
    * Automatische labeling voor verschillende gevoel in tekst.
    * Sentiment analyse en uitvoer op het niveau van een document en zin. 

Het biedt ondersteuning voor Engels ( `en` ), Japans ( `ja` ), vereenvoudigd Chinees ( `zh-Hans` ), traditioneel Chinees ( `zh-Hant` ), Frans ( `fr` ), Italiaans ( `it` ), Spaans (), `es` Nederlands (), Portugees () en `nl` `pt` Duits ( `de` ), en is beschikbaar in de volgende regio's: `Australia East` ,, `Central Canada` , `Central US` `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2` ,,,,,,, en. 

> [!div class="nextstepaction"]
> [Meer informatie over Sentimentanalyse v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Volgende stappen

* [Wat is Text Analytics-API?](overview.md)  
* [Voorbeeldgebruikerscenario's](text-analytics-user-scenarios.md)
* [Sentiment analyse](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Taaldetectie](how-tos/text-analytics-how-to-language-detection.md)
* [Herkenning van entiteiten](how-tos/text-analytics-how-to-entity-linking.md)
* [Sleuteltermextractie](how-tos/text-analytics-how-to-keyword-extraction.md)
