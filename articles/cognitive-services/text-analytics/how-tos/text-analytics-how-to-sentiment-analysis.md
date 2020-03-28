---
title: Sentimentanalyse uitvoeren met Text Analytics REST API
titleSuffix: Azure Cognitive Services
description: In dit artikel ziet u hoe u sentiment in tekst detecteren met de Azure Cognitive Services Text Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 03/09/2020
ms.author: aahi
ms.openlocfilehash: b3c112876bfd2578e6ebaa95c6902aa9b8f832d9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79203454"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Hoe: Sentiment detecteren met behulp van de Text Analytics API

De functie Sentiment Analysis van de Text Analytics-api evalueert tekst en retourneert sentimentscores en labels voor elke zin. Dit is handig voor het detecteren van positief en negatief sentiment in sociale media, recensies van klanten, discussiefora en meer. De AI-modellen die door de API worden gebruikt, worden geleverd door de service, u hoeft alleen maar inhoud te verzenden voor analyse.

> [!TIP]
> Text Analytics biedt ook een Docker-containerinstallatiekopie op basis van Linux voor taaldetectie. U kunt de [Text Analytics-container dus dicht bij uw gegevens installeren en uitvoeren](text-analytics-how-to-install-containers.md).

Sentiment Analysis ondersteunt een breed scala aan talen, met meer in preview. Zie voor meer informatie [Ondersteunde talen](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Concepten

De Text Analytics API maakt gebruik van een machine learning classificatiealgoritme om een sentimentscore tussen 0 en 1 te genereren. Scores dicht bij 1 wijzen op een positief gevoel, terwijl scores dicht bij 0 op een negatief gevoel wijzen. Sentimentanalyse wordt uitgevoerd op het hele document, in plaats van afzonderlijke entiteiten in de tekst. Dit betekent dat sentimentscores worden geretourneerd op document- of zinsniveau. 

Het gebruikte model is vooraf opgeleid met een uitgebreid corpus van tekst- en sentimentsassociaties. Het maakt gebruik van een combinatie van technieken voor analyse, met inbegrip van tekstverwerking, deel-van-spraak analyse, tekstplaatsing, en tekst associaties. Zie voor meer informatie over het algoritme [Introductie Text Analytics](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/). Op dit moment is het niet mogelijk om uw eigen trainingsgegevens te verstrekken. 

Er is een tendens voor het noteren nauwkeurigheid om te verbeteren wanneer documenten minder zinnen eerder dan een groot blok van tekst bevatten. Tijdens een fase van de evaluatie objectiviteit bepaalt het model of een document als geheel objectief is of gevoel bevat. Een document dat meestal objectief is, gaat niet door naar de sentimentdetectiefase, wat resulteert in een score van 0,50, zonder verdere verwerking. Voor documenten die in de pijplijn worden voortgezet, genereert de volgende fase een score boven of onder 0,50. De score is afhankelijk van de mate van sentiment die in het document wordt gedetecteerd.

## <a name="sentiment-analysis-versions-and-features"></a>Sentiment Analysis-versies en -functies

De Text Analytics API biedt twee versies van Sentiment Analysis - v2 en v3. Sentiment Analysis v3 (Public preview) biedt aanzienlijke verbeteringen in de nauwkeurigheid en details van de tekstcategorisering en -score van de API.

> [!NOTE]
> * De v3-aanvraagindeling voor sentimentanalyse en [gegevenslimieten](../overview.md#data-limits) zijn hetzelfde als de vorige versie.
> * Sentiment Analysis v3 is beschikbaar `Australia East`in `Central Canada` `Central US`de `East Asia` `East US`volgende `East US 2` `North Europe`regio's: , , , , , , `Southeast Asia` `South Central US`, , , `UK South`, `West Europe`en `West US 2`.

| Functie                                   | Sentiment Analyse v2 | Sentiment Analyse v3 |
|-------------------------------------------|-----------------------|-----------------------|
| Methoden voor enkele aanvragen en batchaanvragen    | X                     | X                     |
| Sentimentscores voor het hele document  | X                     | X                     |
| Sentimentscores voor individuele zinnen |                       | X                     |
| Sentiment labeling                        |                       | X                     |
| Modelversiebeheer                   |                       | X                     |

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

### <a name="sentiment-scoring"></a>Sentiment scoren

Sentiment Analysis v3 classificert tekst met sentimentlabels (hieronder beschreven). De geretourneerde scores geven het vertrouwen van het model weer dat de tekst positief, negatief of neutraal is. Hogere waarden betekenen een hoger vertrouwen. 

### <a name="sentiment-labeling"></a>Sentiment labeling

Sentiment Analysis v3 kan scores en labels op zins- en documentniveau retourneren. De scores en `positive` `negative`labels `neutral`zijn , en . Op documentniveau kan `mixed` het sentimentlabel ook zonder score worden geretourneerd. Het sentiment van het document wordt hieronder bepaald:

| Zinssentiment                                                                            | Geretourneerd documentlabel |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Ten minste `positive` één zin staat in het document. De rest van `neutral`de zinnen zijn. | `positive`              |
| Ten minste `negative` één zin staat in het document. De rest van `neutral`de zinnen zijn. | `negative`              |
| Ten minste `negative` één zin `positive` en ten minste één zin staan in het document.    | `mixed`                 |
| Alle zinnen in `neutral`het document zijn .                                                  | `neutral`               |

### <a name="model-versioning"></a>Modelversiebeheer

> [!NOTE]
> Modelversiewerk voor sentimentanalyse is `v3.0-preview.1`beschikbaar vanaf versie.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>Voorbeeld C#-code

U een voorbeeld C#-toepassing vinden die deze versie van Sentiment Analysis aanroept op [GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/TextAnalyticsSentiment.cs)


#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

### <a name="sentiment-scoring"></a>Sentiment scoren

De sentimentanalyzer classificert tekst als overwegend positief of negatief. Het wijst een score toe in het bereik van 0 tot 1. Waarden dicht bij 0,5 zijn neutraal of onbepaald. Een score van 0,5 geeft neutraliteit aan. Wanneer een tekenreeks niet kan worden geanalyseerd op sentiment of geen sentiment heeft, is de score altijd 0,5 precies. Bijvoorbeeld, als u in een Spaanse tekenreeks doorgeeft met een Engelse taalcode is de score 0,5.

---

## <a name="sending-a-rest-api-request"></a>Een REST API-aanvraag verzenden 

### <a name="preparation"></a>Voorbereiding

Sentimentanalyse levert een resultaat van hogere kwaliteit op wanneer u het kleinere hoeveelheden tekst geeft om aan te werken. Dit is het tegenovergestelde van sleuteltermextractie, wat beter presteert op grotere blokken tekst. Overweeg dienovereenkomstig herstructurering van de invoer voor de beste resultaten uit beide bewerkingen.

U moet JSON-documenten in deze indeling hebben: ID, tekst en taal.

De documentgrootte moet minder dan 5.120 tekens per document bevatten. U maximaal 1.000 items (ID's) per collectie hebben. De verzameling is in de hoofdtekst van de aanvraag ingediend.

## <a name="structure-the-request"></a>Structureer de aanvraag

Maak een POST-aanvraag. U [Postman](text-analytics-how-to-call-api.md) of de **API-testconsole** in de volgende referentiekoppelingen gebruiken om er snel een te structureren en te verzenden. 

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

[Sentiment analyse v3 referentie](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

[Sentiment analyse v2 referentie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

---

Stel het HTTPS-eindpunt in voor sentimentanalyse met behulp van een Text Analytics-bron op Azure of een geinstantieerde [Text Analytics-container](text-analytics-how-to-install-containers.md). U moet de juiste URL opgeven voor de versie die u wilt gebruiken. Bijvoorbeeld:

> [!NOTE]
> U uw sleutel en eindpunt voor uw Text Analytics-bron vinden op de azure-portal. Ze bevinden zich op de **startpagina snel van** de resource, onder **resourcebeheer.** 

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

---

Stel een aanvraagkop in om uw Text Analytics API-sleutel op te nemen. Verstrek in de hoofdtekst van de aanvraag de JSON-documentenverzameling die u hebt voorbereid voor deze analyse.

### <a name="example-sentiment-analysis-request"></a>Voorbeeld sentimentanalyseaanvraag 

Hier volgt een voorbeeld van de inhoud die u voor gevoelsanalyse kan indienen. De aanvraagindeling is hetzelfde voor beide versies van de API.
    
```json
{
    "documents": [
    {
        "language": "en",
        "id": "1",
        "text": "Hello world. This is some input text that I love."
    },
    {
        "language": "en",
        "id": "2",
        "text": "It's incredibly sunny outside! I'm so happy."
    }
    ],
}
```

### <a name="post-the-request"></a>Plaats de aanvraag

Analyse wordt uitgevoerd na ontvangst van de aanvraag. Zie de sectie [gegevenslimieten](../overview.md#data-limits) in het overzicht voor informatie over de grootte en het aantal aanvragen dat u per minuut en seconde verzenden.

De Text Analytics API is stateloos. Er worden geen gegevens opgeslagen in uw account en de resultaten worden onmiddellijk geretourneerd in het antwoord.


### <a name="view-the-results"></a>De resultaten bekijken

De sentimentanalyzer classificert tekst als overwegend positief of negatief. Het wijst een score toe in het bereik van 0 tot 1. Waarden dicht bij 0,5 zijn neutraal of onbepaald. Een score van 0,5 geeft neutraliteit aan. Wanneer een tekenreeks niet kan worden geanalyseerd op sentiment of geen sentiment heeft, is de score altijd 0,5 precies. Bijvoorbeeld, als u in een Spaanse tekenreeks doorgeeft met een Engelse taalcode is de score 0,5.

Uitvoer wordt onmiddellijk geretourneerd. U de resultaten streamen naar een toepassing die JSON accepteert of de uitvoer opslaan in een bestand op het lokale systeem. Importeer vervolgens de uitvoer in een toepassing die u gebruiken om de gegevens te sorteren, zoeken en manipuleren. Vanwege ondersteuning voor meertaligen en emoji's kan het antwoord tekstverschuivingen bevatten. Zie [hoe u compensaties verwerkt](../concepts/text-offsets.md) voor meer informatie.

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>Sentiment Analyse v3 voorbeeld reactie

Reacties van Sentiment Analysis v3 bevatten sentimentlabels en scores voor elke geanalyseerde zin en document. `documentScores`wordt niet geretourneerd als het `mixed`documentsentimentlabel is .

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>Sentiment Analyse v2 voorbeeld reactie

Reacties van Sentiment Analysis v2 bevatten sentimentscores voor elk verzonden document.

```json
{
  "documents": [{
    "id": "1",
    "score": 0.98690706491470337
  }, {
    "id": "2",
    "score": 0.95202046632766724
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werkstroom geleerd voor sentimentanalyse met behulp van de Text Analytics API. Samenvatting:

+ Sentiment Analyse is beschikbaar voor geselecteerde talen in twee versies.
+ JSON-documenten in de aanvraaginstantie bevatten een ID, tekst en taalcode.
+ Het POST-verzoek `/sentiment` is een eindpunt met behulp van een gepersonaliseerde [toegangssleutel en een eindpunt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dat geldig is voor uw abonnement.
+ Responsuitvoer, die bestaat uit een gevoelsscore voor elke document-id, kan worden gestreamd naar elke app die JSON accepteert. Bijvoorbeeld Excel en Power BI.

## <a name="see-also"></a>Zie ook

* [Overzicht van Text Analytics](../overview.md)
* [De tekstanalyse-clientbibliotheek gebruiken](../quickstarts/text-analytics-sdk.md)
* [Wat is er nieuw](../whats-new.md)
