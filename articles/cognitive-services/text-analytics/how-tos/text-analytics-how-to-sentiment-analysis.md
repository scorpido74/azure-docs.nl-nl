---
title: Sentiment-analyse uitvoeren met Text Analytics REST API
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u sentiment in tekst kunt detecteren met de Azure Cognitive Services Text Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 05/18/2020
ms.author: aahi
ms.openlocfilehash: acd8fae81baa7ad65b8d9c321c55a6311cbf4c72
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/28/2020
ms.locfileid: "84141242"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Procedure: sentiment detecteren met behulp van de Text Analytics-API

De functie Sentimentanalyse van Text Analytics-API evalueert tekst en retourneert sentiment-scores en-labels voor elke zin. Dit is handig voor het detecteren van positieve en negatieve sentiment in sociale media, beoordelingen van klanten, discussie forums en meer. De AI-modellen die door de API worden gebruikt, worden door de service verschaft, maar u hoeft alleen inhoud voor analyse te verzenden.

Na verzen ding van een sentiment-analyse aanvraag retourneert de API sentiment-labels (zoals ' negatieve ', ' neutral ' en ' positieve ') en betrouwbaarheids scores op de zin en op document niveau.

Sentimentanalyse ondersteunt een breed scala aan talen, met meer in preview-versie. Zie voor meer informatie [Ondersteunde talen](../text-analytics-supported-languages.md).

## <a name="sentiment-analysis-versions-and-features"></a>Sentimentanalyse versies en onderdelen

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Functie                                   | Sentimentanalyse v3 | Sentimentanalyse v 3.1 (preview-versie) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Methoden voor enkelvoudige en batch-aanvragen    | X                     | X                                 |
| Sentiment scores en labels             | X                     | X                                 |
| [Docker-container](text-analytics-how-to-install-containers.md) op basis van Linux | X  |  |
| Opinie analyse                            |                       | X                                 |

### <a name="sentiment-scoring-and-labeling"></a>Sentiment scoreing en labeling

Sentimentanalyse in V3 past sentiment-labels toe op tekst, die wordt geretourneerd op een zin en document niveau, met een betrouwbaarheids score voor elke. 

De labels zijn `positive` , `negative` en `neutral` . Op document niveau `mixed` kan het label sentiment ook worden geretourneerd. De sentiment van het document wordt hieronder bepaald:

| Zin sentiment                                                                            | Label van geretourneerd document |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Ten minste één `positive` zin bevindt zich in het document. De rest van de zinnen zijn `neutral` . | `positive`              |
| Ten minste één `negative` zin bevindt zich in het document. De rest van de zinnen zijn `neutral` . | `negative`              |
| Ten minste één `negative` zin en ten minste één `positive` zin in het document.    | `mixed`                 |
| Alle zinnen in het document zijn `neutral` .                                                  | `neutral`               |

Betrouwbaarheids scores variëren van 1 tot 0. Scores dichter bij 1 geven een hogere betrouw baarheid in de classificatie van het label aan, terwijl lagere scores de lagere betrouw baarheid aangeven. De betrouwbaarheids scores in elk document of elke zin worden toegevoegd aan 1.

### <a name="opinion-mining"></a>Opinie analyse

Opinie-analyse is een functie van Sentimentanalyse, vanaf versie 3,1-Preview. 1. Deze functie is ook bekend als op aspect gebaseerde Sentimentanalyse in natuurlijke taal verwerking (NLP) en biedt meer gedetailleerde informatie over de adviezen met betrekking tot aspecten (zoals de kenmerken van producten of Services) in de tekst.

Als een klant bijvoorbeeld feedback over een hotel laat staan, zoals ' de kamer is geweldig, maar het personeel niet op de hoogte was ', worden er in de opinie analyse aspecten van de tekst en hun bijbehorende meningen en gevoels gevonden:

| Aspect | Mening    | Sentiment |
|--------|------------|-----------|
| ruimte   | geweldige      | positief  |
| mede  | goedgekeurde | negatief  |

Als u wilt weten hoe u uw resultaten kunt bekijken, moet u de `opinionMining=true` vlag in een aanvraag voor sentiment analyse toevoegen. De resultaten van de opinie analyse worden opgenomen in het sentiment-analyse antwoord.

## <a name="sending-a-rest-api-request"></a>Een REST API aanvraag verzenden 

### <a name="preparation"></a>Voorbereiding

Sentiment-analyse levert een hogere kwaliteit op wanneer u een kleiner aantal aan tekst kunt gebruiken. Dit is het tegenovergestelde van sleuteltermextractie, wat beter presteert op grotere blokken tekst. Overweeg dienovereenkomstig herstructurering van de invoer voor de beste resultaten uit beide bewerkingen.

U moet JSON-documenten hebben met de volgende indeling: ID, tekst en taal.

De document grootte moet kleiner zijn dan 5.120 tekens per document. U kunt Maxi maal 1.000 items per verzameling hebben. De verzameling is in de hoofdtekst van de aanvraag ingediend.

## <a name="structure-the-request"></a>Structureer de aanvraag

Maak een POST-aanvraag. U kunt [postman](text-analytics-how-to-call-api.md) of de **API-test console** in de volgende referentie koppelingen gebruiken om een snelle structuur en verzen ding te sturen. 

#### <a name="version-30"></a>[Versie 3,0](#tab/version-3)

[Sentimentanalyse v3-referentie](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

#### <a name="version-31-preview1"></a>[Versie 3,1-Preview. 1](#tab/version-3-1)

[Naslag informatie over Sentimentanalyse v 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Sentiment)

---

### <a name="request-endpoints"></a>Eind punten van aanvraag

Stel het HTTPS-eind punt voor sentiment analyse in met behulp van een Text Analytics resource in azure of een geïnstantieerd [Text Analytics-container](text-analytics-how-to-install-containers.md). U moet de juiste URL toevoegen voor de versie die u wilt gebruiken. Bijvoorbeeld:

> [!NOTE]
> U vindt de sleutel en het eind punt voor uw Text Analytics-resource in azure Portal. Ze bevinden zich op de pagina **snel starten** van de resource, onder **resource beheer**. 

#### <a name="version-30"></a>[Versie 3,0](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

#### <a name="version-31-preview1"></a>[Versie 3,1-Preview. 1](#tab/version-3-1)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment`

U moet de para meter toevoegen om advies te krijgen over de resultaten van de analyse `opinionMining=true` . Bijvoorbeeld:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment?opinionMining=true`

Deze para meter is standaard ingesteld op `false` . 

---

Stel een aanvraag header in om uw Text Analytics-API sleutel toe te voegen. Verstrek in de hoofdtekst van de aanvraag de JSON-documentenverzameling die u hebt voorbereid voor deze analyse.

### <a name="example-sentiment-analysis-request"></a>Voor beeld Sentimentanalyse aanvraag 

Hier volgt een voorbeeld van de inhoud die u voor gevoelsanalyse kan indienen. De aanvraag indeling is hetzelfde voor beide versies.
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>Plaats de aanvraag

Analyse wordt uitgevoerd na ontvangst van de aanvraag. Zie de sectie [gegevens limieten](../overview.md#data-limits) in het overzicht voor meer informatie over de grootte en het aantal aanvragen dat u per minuut en seconde kunt verzenden.

De Text Analytics-API is stateless. Er worden geen gegevens in uw account opgeslagen en de resultaten worden onmiddellijk in het antwoord geretourneerd.


### <a name="view-the-results"></a>De resultaten bekijken

Sentiment analyse retourneert een sentiment-label en betrouwbaarheids score voor het hele document, en elke zin daarin. Scores dichter bij 1 geven een hogere betrouw baarheid in de classificatie van het label aan, terwijl lagere scores de lagere betrouw baarheid aangeven. Een document kan meerdere zinnen bevatten en de betrouwbaarheids scores binnen elk document of elke zin worden toegevoegd aan 1.

Uitvoer wordt onmiddellijk geretourneerd. U kunt de resultaten streamen naar een toepassing die JSON accepteert of de uitvoer opslaan in een bestand op het lokale systeem. Importeer vervolgens de uitvoer in een toepassing die u kunt gebruiken om de gegevens te sorteren, te zoeken en te bewerken. Vanwege meertalige en Emoji-ondersteuning kan het antwoord tekst verschuivingen bevatten. Zie [offsets verwerken](../concepts/text-offsets.md) voor meer informatie.

#### <a name="version-30"></a>[Versie 3,0](#tab/version-3)

### <a name="sentiment-analysis-v30-example-response"></a>Voorbeeld reactie van Sentimentanalyse v 3.0

Antwoorden van Sentimentanalyse v3 bevatten sentiment labels en scores voor elke geanalyseerde zin en document.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

#### <a name="version-31-preview1"></a>[Versie 3,1-Preview. 1](#tab/version-3-1)

### <a name="sentiment-analysis-v31-example-response"></a>Voorbeeld reactie van Sentimentanalyse v 3.1

Sentimentanalyse v 3.1 bevat naast het antwoord object op het tabblad **versie 3,0** een advies analyse. In het onderstaande antwoord heeft de zin dat *het restaurant een fantastisch levens middel heeft en onze wacht tijd* twee aspecten: *levens middelen* en *wacht*woorden. De eigenschap van elk aspect `relations` bevat een `ref` waarde met de URI-verwijzing naar de `documents` bijbehorende `sentences` objecten, en `opinions` .

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly.",
                    "aspects": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 25,
                            "length": 4,
                            "text": "food",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/0"
                                }
                            ]
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 38,
                            "length": 6,
                            "text": "waiter",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/1"
                                }
                            ]
                        }
                    ],
                    "opinions": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 19,
                            "length": 5,
                            "text": "great",
                            "isNegated": false
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 49,
                            "length": 8,
                            "text": "friendly",
                            "isNegated": false
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

---

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werk stromen geleerd voor sentiment analyse met behulp van de Text Analytics-API. Samenvatting:

+ Sentimentanalyse is beschikbaar voor geselecteerde talen.
+ JSON-documenten in de hoofd tekst van de aanvraag bevatten een ID, tekst en taal code.
+ De POST-aanvraag is naar een `/sentiment` eind punt met behulp van een aangepaste [toegangs sleutel en een eind punt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dat geldig is voor uw abonnement.
+ De uitvoer van antwoorden, die bestaat uit een sentiment-score voor elke document-ID, kan worden gestreamd naar elke app die JSON accepteert. Bijvoorbeeld Excel en Power BI.

## <a name="see-also"></a>Zie ook

* [Overzicht van Text Analytics](../overview.md)
* [De Text Analytics-client bibliotheek gebruiken](../quickstarts/text-analytics-sdk.md)
* [Wat is er nieuw](../whats-new.md)
