---
title: Trefwoordextractie met behulp van de API Voor Text Analytics REST
titleSuffix: Azure Cognitive Services
description: Sleutelzinnen extraheren met behulp van de TEXT Analytics REST API uit Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/29/2019
ms.author: raymondl
ms.openlocfilehash: ec5ff756d7e732430675676868bc754627a2a4a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72429025"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Voorbeeld: Sleutelzinnen extraheren met Text Analytics

De [Sleuteltermextractie API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) evalueert ongestructureerde tekst en retourneert voor elk JSON-document een lijst met sleuteltermen.

Deze functie is handig als u snel de belangrijkste punten moet identificeren van een verzameling documenten. Bijvoorbeeld, bij de invoertekst "het eten was heerlijk en de bediening fantastisch", retourneert de service de belangrijkste gespreksonderwerpen: 'eten' en "bediening fantastisch".

Zie voor meer informatie [Ondersteunde talen](../text-analytics-supported-languages.md).

> [!TIP]
> Text Analytics biedt ook een Docker-containerinstallatiekopie op basis van Linux voor sleuteltermextractie. U kunt de [Text Analytics-container dus dicht bij uw gegevens installeren en uitvoeren](text-analytics-how-to-install-containers.md).

## <a name="preparation"></a>Voorbereiding

Sleuteltermextractie werkt het beste wanneer u grotere segmenten tekst opgeeft. Dit is het tegenovergestelde van sentimentanalyse, wat beter presteert op kleinere blokken tekst. Overweeg dienovereenkomstig herstructurering van de invoer voor de beste resultaten uit beide bewerkingen.

U moet JSON-documenten in deze indeling hebben: ID, tekst, taal

De documentgrootte moet 5.120 of minder tekens per document zijn en u maximaal 1.000 items (ID's) per verzameling hebben. De verzameling is in de hoofdtekst van de aanvraag ingediend. Het volgende voorbeeld geeft de inhoud die u kunt indienen voor sleuteltermextractie.

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Stap 1: Structuur van de aanvraag

Zie [De API voor Text Analytics aanroepen](text-analytics-how-to-call-api.md)voor informatie over de definitie van aanvragen. De volgende punten zijn voor uw gemak opnieuw geformuleerd:

+ Maak een **POST**-aanvraag. Bekijk de API-documentatie voor dit verzoek: [Key Phrases API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6).

+ Stel het HTTP-eindpunt in voor het extraheren van de sleutelwoord met behulp van een Text Analytics-bron op Azure of een geinstantiated [Text Analytics-container.](text-analytics-how-to-install-containers.md) U moet `/text/analytics/v2.1/keyPhrases` opnemen in de URL. Bijvoorbeeld: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v2.1/keyPhrases`.

+ Stel een aanvraagkop in om de [toegangssleutel](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) voor Text Analytics-bewerkingen op te nemen.

+ Verstrek in de hoofdtekst van de aanvraag de JSON-documentenverzameling die u hebt voorbereid voor deze analyse.

> [!Tip]
> Gebruik [Postman](text-analytics-how-to-call-api.md) of open de **API-testconsole** in de [documentatie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) om de aanvraag te structureren en POST deze in de service.

## <a name="step-2-post-the-request"></a>Stap 2: De aanvraag posten

Analyse wordt uitgevoerd na ontvangst van de aanvraag. Zie de sectie [gegevenslimieten](../overview.md#data-limits) in het overzicht voor informatie over de grootte en het aantal aanvragen dat u per minuut of per seconde verzenden.

Terughalen als de service staatloos is. Er worden geen gegevens opgeslagen in uw account. Resultaten worden onmiddellijk in het antwoord geretourneerd.

## <a name="step-3-view-results"></a>Stap 3: Resultaten weergeven

Alle POST-verzoeken retourneren een ingedeeld JSON-antwoord met de id's en gedetecteerde eigenschappen. De volgorde van de geretourneerde sleutelzinnen wordt intern bepaald, door het model.

Uitvoer wordt onmiddellijk geretourneerd. U kunt de resultaten streamen naar een toepassing die JSON accepteert of u kunt de uitvoer opslaan als lokaal bestand en vervolgens importeren in een toepassing waarmee u kunt sorteren, zoeken en de gegevens kunt manipuleren.

Een voorbeeld van de uitvoer voor sleuteltermextractie:

```json
    {
        "documents": [
            {
                "keyPhrases": [
                    "year",
                    "trail",
                    "trip",
                    "views"
                ],
                "id": "1"
            },
            {
                "keyPhrases": [
                    "marked trails",
                    "Worst hike",
                    "goners"
                ],
                "id": "2"
            },
            {
                "keyPhrases": [
                    "trail",
                    "small children",
                    "family"
                ],
                "id": "3"
            },
            {
                "keyPhrases": [
                    "spectacular views",
                    "trail",
                    "area"
                ],
                "id": "4"
            },
            {
                "keyPhrases": [
                    "places",
                    "beautiful views",
                    "favorite trail"
                ],
                "id": "5"
            }
        ],
        "errors": []
    }
```

Zoals opgemerkt, de analyzer vindt en gooi niet-essentiële woorden, en het houdt enkele termen of zinnen die lijken te zijn het onderwerp of object van een zin.

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werkstroom geleerd voor het extraheren van de sleutelzin met behulp van Text Analytics in Cognitive Services. Samenvatting:

+ [Sleuteltermextractie-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) is beschikbaar in bepaalde talen.
+ JSON-documenten in de aanvraaginstantie bevatten een ID, tekst en taalcode.
+ POST-aanvraag is een `/keyphrases`-eindpunt die een persoonlijke [toegangssleutel en een eindpunt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) gebruikt die geldig zijn voor uw abonnement.
+ Responsuitvoer, die bestaat uit trefwoorden en woordgroepen voor elke document-id, kan worden gestreamd naar elke app die JSON accepteert, waaronder Microsoft Office Excel en Power BI, om er maar een paar te noemen.

## <a name="see-also"></a>Zie ook

 [Overzicht van Text Analytics](../overview.md) [Veelgestelde vragen](../text-analytics-resource-faq.md)</br>
 [Text Analytics-productpagina](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Text Analytics-API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
