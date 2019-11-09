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
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: a930a22472dd4329c555539704747af9a2359c33
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837154"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>Voor beeld: sentiment detecteren met Text Analytics

De [Azure sentimentanalyse-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) evalueert tekst invoer en retourneert een sentiment-score voor elk document. Scores variëren van 0 (negatief) tot 1 (positief).

Deze mogelijkheid is handig voor het detecteren van een positief en negatief gevoel in sociale media, klantbeoordelingen en discussieforums. De inhoud wordt door u verschaft. Modellen en trainings gegevens worden door de service verschaft.

Op dit moment ondersteunt de Sentimentanalyse-API Engels, Duits, Spaans en Frans. Andere talen bevinden zich in preview-fase. Zie voor meer informatie [Ondersteunde talen](../text-analytics-supported-languages.md).

> [!TIP]
> De Azure Text Analytics-API biedt ook een docker-container installatie kopie op basis van Linux voor sentiment analyse, zodat u de Text Analytics container dicht bij uw gegevens kunt [installeren en uitvoeren](text-analytics-how-to-install-containers.md) .

## <a name="concepts"></a>Concepten

Text Analytics maakt gebruik van een machine learning-classificatiealgoritme voor het genereren van een gevoelsscore tussen 0 en 1. Scores dicht bij 1 wijzen op een positief gevoel, terwijl scores dicht bij 0 op een negatief gevoel wijzen. Het model is vooraf getraind met een uitgebreide hoofdtekst met gevoelskoppelingen. Het is momenteel niet mogelijk om uw eigen trainings gegevens op te geven. Het model gebruikt een combi natie van technieken tijdens het analyseren van tekst. Technieken zijn tekst verwerking, analyse van een deel van spraak, woord plaatsing en woord koppelingen. Zie voor meer informatie over het algoritme [Introductie Text Analytics](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

Sentimentanalyse wordt uitgevoerd op het hele document, in plaats van een gevoel voor een bepaalde entiteit in de tekst te extraheren. In de praktijk is er sprake van een tendens om nauw keurigheid van scores te verbeteren wanneer documenten een of twee zinnen bevatten in plaats van een groot tekst blok. Tijdens een fase van de evaluatie objectiviteit bepaalt het model of een document als geheel objectief is of gevoel bevat. Een document dat voornamelijk doel gericht is, wordt niet in de sentiment-detectie fase uitgevoerd, wat resulteert in een 0,50-Score, zonder verdere verwerking. Voor documenten die in de pijp lijn worden voortgezet, genereert de volgende fase een score boven of onder 0,50. De score is afhankelijk van de mate van sentiment die in het document is gedetecteerd.

## <a name="sentiment-analysis-v3-public-preview"></a>Open bare preview van Sentimentanalyse v3

De [volgende versie van sentimentanalyse](https://cognitiveusw2ppe.portal.azure-api.net/docs/services/TextAnalytics-v3-0-Preview-1/operations/56f30ceeeda5650db055a3c9) is nu beschikbaar voor open bare preview. Het biedt aanzienlijke verbeteringen in de nauw keurigheid en Details van de tekst categorisatie en Score van de API.

> [!NOTE]
> * De Sentimentanalyse v3-aanvraag indeling en- [gegevens limieten](../overview.md#data-limits) zijn hetzelfde als de vorige versie.
> * Op dit moment Sentimentanalyse V3:
>    * Ondersteunt momenteel de Engelse (`en`), Japans (`ja`), vereenvoudigd Chinees (`zh-Hans`), traditioneel Chinees (`zh-Hant`), Frans (`fr`), Italiaans (`it`), Spaans (`es`), Nederlands (`nl`), Portugees (`pt`) en Duits (`de`) talen.
>    * Is beschikbaar in de volgende regio's: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`en `West US 2`.

|Functie |Beschrijving  |
|---------|---------|
|Verbeterde nauw keurigheid     | Vergeleken met eerdere versies aanzienlijke verbetering voor het detecteren van positieve, neutrale, negatieve en gecombineerde sentimenten in tekstdocumenten.           |
|Sentiment-Score documenteren en op zinnen niveau     | Detecteer het sentiment van zowel een document als van de afzonderlijke zinnen in het document. Als het document meerdere zinnen bevat, wordt aan elke zin een sentimentscore toegewezen.         |
|Sentiment labelen en scores     | De API retourneert nu sentiment-categorieën voor tekst, naast een sentiment Score. De categorieën zijn `positive`, `negative`, `neutral`en `mixed`.       |
| Verbeterde uitvoer | Sentiment-analyse retourneert nu informatie voor zowel een volledig tekst document als de afzonderlijke zinnen. |
| para meter model-versie | Een optionele para meter voor het kiezen van de versie van het Text Analytics model wordt gebruikt voor uw gegevens. |

### <a name="sentiment-labeling"></a>Sentiment labelen

Sentimentanalyse v3 kan scores en labels in een zin en document niveau retour neren. De scores en labels zijn `positive`, `negative`en `neutral`. Op document niveau kan het label van de `mixed` sentiment (niet de Score) ook worden geretourneerd. De sentiment van het document wordt bepaald door de scores van de zinnen samen te voegen.

| Zin sentiment                                                        | Label van geretourneerd document |
|---------------------------------------------------------------------------|----------------|
| Ten minste één positieve zin en de rest van de zinnen zijn neutraal. | `positive`     |
| Ten minste één negatieve zin en de rest van de zinnen zijn neutraal.  | `negative`     |
| Ten minste één negatieve zin en ten minste één positieve zin.         | `mixed`        |
| Alle zinnen zijn neutraal.                                                 | `neutral`      |

### <a name="model-versioning"></a>Model versie beheer

> [!NOTE]
> Model versie voor sentiment analyse is beschikbaar vanaf versie `v3.0-preview.1`.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="sentiment-analysis-v3-example-request"></a>Voorbeeld aanvraag van Sentimentanalyse v3

De volgende JSON is een voor beeld van een aanvraag voor de nieuwe versie van Sentimentanalyse. De opmaak van de aanvraag is hetzelfde als de vorige versie:

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

### <a name="sentiment-analysis-v3-example-response"></a>Sentimentanalyse v3-voorbeeld antwoord

Hoewel de indeling van de aanvraag hetzelfde is als de vorige versie, is de antwoord indeling gewijzigd. De volgende JSON is een voor beeld van een reactie van de nieuwe versie van de API:

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

### <a name="example-c-code"></a>Voorbeeld C# code

U kunt een voorbeeld C# toepassing vinden die deze versie van sentimentanalyse aanroept op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs).

## <a name="preparation"></a>Voorbereiding

Sentiment-analyse levert een hogere kwaliteit op wanneer u het kleinere segment aan tekst kunt toewijzen. Dit is het tegenovergestelde van sleuteltermextractie, wat beter presteert op grotere blokken tekst. Overweeg dienovereenkomstig herstructurering van de invoer voor de beste resultaten uit beide bewerkingen.

U moet JSON-documenten hebben met de volgende indeling: ID, tekst en taal.

De document grootte moet kleiner zijn dan 5.120 tekens per document. U kunt Maxi maal 1.000 items per verzameling hebben. De verzameling is in de hoofdtekst van de aanvraag ingediend. Het volgende voor beeld is een voor beeld van inhoud die u kunt verzenden voor sentiment-analyse:

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

Zie [de Text Analytics-API aanroepen](text-analytics-how-to-call-api.md)voor meer informatie over de definitie van de aanvraag. De volgende punten zijn voor uw gemak opnieuw geformuleerd:

+ Maak een POST-aanvraag. Als u de API-documentatie voor deze aanvraag wilt bekijken, raadpleegt u de [sentimentanalyse-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9).

+ Stel het HTTP-eind punt voor sentiment analyse in met behulp van een Text Analytics resource in azure of een geïnstantieerd [Text Analytics-container](text-analytics-how-to-install-containers.md). U moet `/text/analytics/v2.1/sentiment` in de URL toevoegen. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`.

+ Stel een aanvraag header in voor het toevoegen van de [toegangs sleutel](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) voor Text Analytics bewerkingen.

+ Verstrek in de hoofdtekst van de aanvraag de JSON-documentenverzameling die u hebt voorbereid voor deze analyse.

> [!Tip]
> Gebruik [postman](text-analytics-how-to-call-api.md) of open de **console** voor het testen van de API in de [documentatie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) om de aanvraag te structureren en te plaatsen in de service.

## <a name="step-2-post-the-request"></a>Stap 2: De aanvraag posten

Analyse wordt uitgevoerd na ontvangst van de aanvraag. Zie de sectie [gegevens limieten](../overview.md#data-limits) in het overzicht voor meer informatie over de grootte en het aantal aanvragen dat u per minuut en seconde kunt verzenden.

Terughalen als de service staatloos is. Er worden geen gegevens opgeslagen in uw account. Resultaten worden onmiddellijk in het antwoord geretourneerd.


## <a name="step-3-view-the-results"></a>Stap 3: de resultaten weer geven

De sentiment Analyzer classificeert de tekst als een hoofd zakelijk positief of negatief. Er wordt een score in het bereik van 0 tot 1 toegewezen. Waarden dicht bij 0,5 zijn neutraal of onbepaald. Een score van 0,5 geeft neutraliteit aan. Wanneer een teken reeks niet kan worden geanalyseerd voor sentiment of geen sentiment heeft, is de Score altijd 0,5 precies. Bijvoorbeeld, als u in een Spaanse tekenreeks doorgeeft met een Engelse taalcode is de score 0,5.

Uitvoer wordt onmiddellijk geretourneerd. U kunt de resultaten streamen naar een toepassing die JSON accepteert of de uitvoer opslaan in een bestand op het lokale systeem. Importeer vervolgens de uitvoer in een toepassing die u kunt gebruiken om de gegevens te sorteren, te zoeken en te bewerken.

In het volgende voor beeld ziet u het antwoord op de document verzameling in dit artikel:

```json
    {
        "documents": [
            {
                "score": 0.9999237060546875,
                "id": "1"
            },
            {
                "score": 0.0000540316104888916,
                "id": "2"
            },
            {
                "score": 0.99990355968475342,
                "id": "3"
            },
            {
                "score": 0.980544924736023,
                "id": "4"
            },
            {
                "score": 0.99996328353881836,
                "id": "5"
            }
        ],
        "errors": []
    }
```

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werk stromen geleerd voor sentiment analyse met behulp van Text Analytics in azure Cognitive Services. Samenvatting:

+ De [sentimentanalyse-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) is beschikbaar voor geselecteerde talen.
+ JSON-documenten in de hoofd tekst van de aanvraag bevatten een ID, tekst en taal code.
+ De POST-aanvraag is een `/sentiment`-eind punt met behulp van een aangepaste [toegangs sleutel en een eind punt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dat geldig is voor uw abonnement.
+ De uitvoer van antwoorden, die bestaat uit een sentiment-score voor elke document-ID, kan worden gestreamd naar elke app die JSON accepteert. Voor beeld-apps zijn Excel en Power BI, om een paar te noemen.

## <a name="see-also"></a>Zie ook

 [Overzicht van Text Analytics](../overview.md) [Veelgestelde vragen](../text-analytics-resource-faq.md)</br>
 [Text Analytics-productpagina](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Sleuteltermen extraheren](text-analytics-how-to-keyword-extraction.md)
