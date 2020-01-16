---
title: Herkennings vaardigheid van taal detectie
titleSuffix: Azure Cognitive Search
description: Evalueert ongestructureerde tekst en retourneert voor elke record een taal-id met een score die de sterkte van de analyse in een AI-verrijkings pijplijn in azure Cognitive Search aangeeft.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8439788c63ec1b9feaea148ab52aba498791dc12
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045014"
---
#   <a name="language-detection-cognitive-skill"></a>Herkennings vaardigheid van taal detectie

De **taaldetectie** vaardigheid detecteert de taal van invoer tekst en rapporteert één taal code voor elk document dat voor de aanvraag wordt verzonden. De taal code is gekoppeld aan een score die de sterkte van de analyse aangeeft. Deze vaardigheid maakt gebruik van de machine learning modellen van [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in cognitive Services.

Deze mogelijkheid is vooral nuttig wanneer u de taal van de tekst wilt opgeven als invoer voor andere vaardig heden (bijvoorbeeld de kwalificatie [sentimentanalyse vaardigheid](cognitive-search-skill-sentiment.md) of [tekst splitsen](cognitive-search-skill-textsplit.md)).

Met taal detectie kunt u gebruikmaken van de natuurlijke taal verwerkings bibliotheken van Bing, die het aantal [ondersteunde talen en regio's](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) overschrijdt dat voor Text Analytics wordt vermeld. De exacte lijst met talen wordt niet gepubliceerd, maar bevat alle talen met veel spraak, plus varianten, dialecten en enkele regionale en culturele talen. Als er inhoud in een minder vaak gebruikte taal wordt weer gegeven, kunt u [de taaldetectie-API proberen](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) om te zien of er een code wordt geretourneerd. Het antwoord op talen dat niet kan worden gedetecteerd, is `unknown`.

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureer bare Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in azure Cognitive Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met prijzen voor Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Gegevenslimieten
De maximale grootte van een record moet 50.000 tekens zijn, zoals gemeten door [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Als u uw gegevens wilt opsplitsen voordat u deze naar de taal detectie vaardigheid verzendt, kunt u de [Kwalificatie tekst splitsen](cognitive-search-skill-textsplit.md)gebruiken.

## <a name="skill-inputs"></a>Vaardigheids invoer

Parameters zijn hoofdlettergevoelig.

| Invoer     | Beschrijving |
|--------------------|-------------|
| tekst | De tekst die moet worden geanalyseerd.|

## <a name="skill-outputs"></a>Vaardigheids uitvoer

| Uitvoer naam    | Beschrijving |
|--------------------|-------------|
| languageCode | De ISO 6391-taal code voor de geïdentificeerde taal. Bijvoorbeeld ' en '. |
| languageName | De naam van de taal. Bijvoorbeeld ' Engels '. |
| score | Een waarde tussen 0 en 1. De kans dat de taal correct wordt geïdentificeerd. De score kan lager zijn dan 1 als de zin gemengde talen heeft.  |

##  <a name="sample-definition"></a>Voorbeeld definitie

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>Voorbeeld invoer

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      }
    ]
```


##  <a name="sample-output"></a>Voorbeelduitvoer

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>Fout cases
Als de tekst wordt weer gegeven in een niet-ondersteunde taal, wordt er een fout gegenereerd en wordt er geen taal-id geretourneerd.

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardig heden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
