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
ms.date: 04/27/2020
ms.author: aahi
ms.openlocfilehash: 99a62daf6dced88efd9bda591a0ca44a8b259a75
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195635"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Procedure: sentiment detecteren met behulp van de Text Analytics-API

De functie Sentimentanalyse van Text Analytics-API evalueert tekst en retourneert sentiment-scores en-labels voor elke zin. Dit is handig voor het detecteren van positieve en negatieve sentiment in sociale media, beoordelingen van klanten, discussie forums en meer. De AI-modellen die door de API worden gebruikt, worden door de service verschaft, maar u hoeft alleen inhoud voor analyse te verzenden.

> [!TIP]
> Text Analytics biedt ook een Docker-containerinstallatiekopie op basis van Linux voor taaldetectie. U kunt de [Text Analytics-container dus dicht bij uw gegevens installeren en uitvoeren](text-analytics-how-to-install-containers.md).

Sentimentanalyse ondersteunt een breed scala aan talen, met meer in preview-versie. Zie voor meer informatie [Ondersteunde talen](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Concepten

De Text Analytics-API maakt gebruik van een machine learning classificatie algoritme voor het genereren van een sentiment-score tussen 0 en 1. Scores dicht bij 1 wijzen op een positief gevoel, terwijl scores dicht bij 0 op een negatief gevoel wijzen. Sentiment analyse wordt uitgevoerd op het hele document, in plaats van afzonderlijke entiteiten in de tekst. Dit betekent dat sentiment-scores worden geretourneerd op het niveau van een document of zin. 

Het gebruikte model is vooraf getraind met een uitgebreide verzameling tekst-en sentiment-koppelingen. Er wordt gebruikgemaakt van een combi natie van technieken voor analyse, waaronder tekst verwerking, analyse van een deel van spraak, woord plaatsing en woord koppelingen. Zie voor meer informatie over het algoritme [Introductie Text Analytics](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/). Het is momenteel niet mogelijk om uw eigen trainings gegevens op te geven. 

Er is een tendens voor de nauw keurigheid van scores om te verbeteren wanneer documenten minder zinnen bevatten in plaats van een groot tekst blok. Tijdens een fase van de evaluatie objectiviteit bepaalt het model of een document als geheel objectief is of gevoel bevat. Een document dat voornamelijk doel gericht is, wordt niet in de sentiment-detectie fase uitgevoerd, wat resulteert in een 0,50-Score, zonder verdere verwerking. Voor documenten die in de pijp lijn worden voortgezet, genereert de volgende fase een score boven of onder 0,50. De score is afhankelijk van de mate van sentiment die in het document is gedetecteerd.

## <a name="sentiment-analysis-versions-and-features"></a>Sentimentanalyse versies en onderdelen

De Text Analytics-API biedt twee versies van Sentimentanalyse v2 en v3. Sentimentanalyse v3 (open bare preview) biedt aanzienlijke verbeteringen in de nauw keurigheid en Details van de tekst categorisatie en Score van de API.

> [!NOTE]
> * De Sentimentanalyse v3-aanvraag indeling en- [gegevens limieten](../overview.md#data-limits) zijn hetzelfde als de vorige versie.
> * Sentimentanalyse v3 is beschikbaar in de volgende regio's: `Australia East`, `Central Canada`, `Central US`, `East Asia` `East US` `East US 2`,,, `North Europe`, `Southeast Asia` `South Central US` `UK South` `West Europe`,,, en `West US 2`.

| Functie                                   | Sentimentanalyse v2 | Sentimentanalyse v3 |
|-------------------------------------------|-----------------------|-----------------------|
| Methoden voor enkelvoudige en batch-aanvragen    | X                     | X                     |
| Sentiment-scores voor het hele document  | X                     | X                     |
| Sentimente scores voor afzonderlijke zinnen |                       | X                     |
| Sentiment labelen                        |                       | X                     |
| Model versie beheer                   |                       | X                     |

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

### <a name="sentiment-scoring"></a>Sentiment Score

Sentimentanalyse v3 tekst classificeert met sentiment-labels (zie hieronder). De geretourneerde scores vertegenwoordigen het vertrouwen van het model dat de tekst positief, negatief of neutraal is. Hogere waarden duiden hogere betrouw baarheid aan. 

### <a name="sentiment-labeling"></a>Sentiment labelen

Sentimentanalyse v3 retourneert sentiment-labels in een zin en document niveau`positive`( `negative`, en `neutral`) samen met betrouwbaarheids scores. Het `mixed` label sentiment kan ook op document niveau worden geretourneerd. 

De sentiment van het document wordt hieronder bepaald:

| Zin sentiment                                                                            | Label van geretourneerd document |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Ten minste één `positive` zin bevindt zich in het document. De rest van de zinnen zijn `neutral`. | `positive`              |
| Ten minste één `negative` zin bevindt zich in het document. De rest van de zinnen zijn `neutral`. | `negative`              |
| Ten minste één `negative` zin en ten minste één `positive` zin in het document.    | `mixed`                 |
| Alle zinnen in het document zijn `neutral`.                                                  | `neutral`               |

### <a name="model-versioning"></a>Model versie beheer

> [!NOTE]
> Model versie voor sentiment analyse is beschikbaar vanaf versie `v3.0-preview.1`.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>Voor beeld C#-code

U vindt een voor beeld van een C#-toepassing die deze versie van Sentimentanalyse aanroept op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/TextAnalyticsSentiment.cs).


#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

### <a name="sentiment-scoring"></a>Sentiment Score

De sentiment Analyzer classificeert de tekst als een hoofd zakelijk positief of negatief. Er wordt een score in het bereik van 0 tot 1 toegewezen. Waarden dicht bij 0,5 zijn neutraal of onbepaald. Een score van 0,5 geeft neutraliteit aan. Wanneer een teken reeks niet kan worden geanalyseerd voor sentiment of geen sentiment heeft, is de Score altijd 0,5 precies. Bijvoorbeeld, als u in een Spaanse tekenreeks doorgeeft met een Engelse taalcode is de score 0,5.

---

## <a name="sending-a-rest-api-request"></a>Een REST API aanvraag verzenden 

### <a name="preparation"></a>Voorbereiding

Sentiment-analyse levert een hogere kwaliteit op wanneer u een kleiner aantal aan tekst kunt gebruiken. Dit is het tegenovergestelde van sleuteltermextractie, wat beter presteert op grotere blokken tekst. Overweeg dienovereenkomstig herstructurering van de invoer voor de beste resultaten uit beide bewerkingen.

U moet JSON-documenten hebben met de volgende indeling: ID, tekst en taal.

De document grootte moet kleiner zijn dan 5.120 tekens per document. U kunt Maxi maal 1.000 items per verzameling hebben. De verzameling is in de hoofdtekst van de aanvraag ingediend.

## <a name="structure-the-request"></a>Structureer de aanvraag

Maak een POST-aanvraag. U kunt [postman](text-analytics-how-to-call-api.md) of de **API-test console** in de volgende referentie koppelingen gebruiken om een snelle structuur en verzen ding te sturen. 

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

[Sentimentanalyse v3-referentie](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

[Naslag informatie over Sentimentanalyse v2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

---

Stel het HTTPS-eind punt voor sentiment analyse in met behulp van een Text Analytics resource in azure of een geïnstantieerd [Text Analytics-container](text-analytics-how-to-install-containers.md). U moet de juiste URL toevoegen voor de versie die u wilt gebruiken. Bijvoorbeeld:

> [!NOTE]
> U vindt de sleutel en het eind punt voor uw Text Analytics-resource in azure Portal. Ze bevinden zich op de pagina **snel starten** van de resource, onder **resource beheer**. 

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

---

Stel een aanvraag header in om uw Text Analytics-API sleutel toe te voegen. Verstrek in de hoofdtekst van de aanvraag de JSON-documentenverzameling die u hebt voorbereid voor deze analyse.

### <a name="example-sentiment-analysis-request"></a>Voor beeld Sentimentanalyse aanvraag 

Hier volgt een voorbeeld van de inhoud die u voor gevoelsanalyse kan indienen. De aanvraag indeling is hetzelfde voor beide versies van de API.
    
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

Analyse wordt uitgevoerd na ontvangst van de aanvraag. Zie de sectie [gegevens limieten](../overview.md#data-limits) in het overzicht voor meer informatie over de grootte en het aantal aanvragen dat u per minuut en seconde kunt verzenden.

De Text Analytics-API is stateless. Er worden geen gegevens in uw account opgeslagen en de resultaten worden onmiddellijk in het antwoord geretourneerd.


### <a name="view-the-results"></a>De resultaten bekijken

De sentiment Analyzer classificeert de tekst als een hoofd zakelijk positief of negatief. Er wordt een score in het bereik van 0 tot 1 toegewezen. Waarden dicht bij 0,5 zijn neutraal of onbepaald. Een score van 0,5 geeft neutraliteit aan. Wanneer een teken reeks niet kan worden geanalyseerd voor sentiment of geen sentiment heeft, is de Score altijd 0,5 precies. Bijvoorbeeld, als u in een Spaanse tekenreeks doorgeeft met een Engelse taalcode is de score 0,5.

Uitvoer wordt onmiddellijk geretourneerd. U kunt de resultaten streamen naar een toepassing die JSON accepteert of de uitvoer opslaan in een bestand op het lokale systeem. Importeer vervolgens de uitvoer in een toepassing die u kunt gebruiken om de gegevens te sorteren, te zoeken en te bewerken. Vanwege meertalige en Emoji-ondersteuning kan het antwoord tekst verschuivingen bevatten. Zie [offsets verwerken](../concepts/text-offsets.md) voor meer informatie.

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>Sentimentanalyse v3-voorbeeld antwoord

Antwoorden van Sentimentanalyse v3 bevatten sentiment labels en scores voor elke geanalyseerde zin en document. `documentScores`wordt niet geretourneerd als het sentiment label van het `mixed`document is.

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

#### <a name="version-21"></a>[Versie 2,1](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>Voorbeeld reactie van Sentimentanalyse v2

Antwoorden van Sentimentanalyse v2 bevatten sentiment scores voor elk verzonden document.

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

In dit artikel hebt u concepten en werk stromen geleerd voor sentiment analyse met behulp van de Text Analytics-API. Samenvatting:

+ Sentimentanalyse is beschikbaar voor geselecteerde talen in twee versies.
+ JSON-documenten in de hoofd tekst van de aanvraag bevatten een ID, tekst en taal code.
+ De POST-aanvraag is naar `/sentiment` een eind punt met behulp van een aangepaste [toegangs sleutel en een eind punt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dat geldig is voor uw abonnement.
+ De uitvoer van antwoorden, die bestaat uit een sentiment-score voor elke document-ID, kan worden gestreamd naar elke app die JSON accepteert. Bijvoorbeeld Excel en Power BI.

## <a name="see-also"></a>Zie ook

* [Overzicht van Text Analytics](../overview.md)
* [De Text Analytics-client bibliotheek gebruiken](../quickstarts/text-analytics-sdk.md)
* [Wat is er nieuw](../whats-new.md)
