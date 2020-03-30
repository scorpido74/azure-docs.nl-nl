---
title: Cognitieve vaardigheid in taaldetectie
titleSuffix: Azure Cognitive Search
description: Evalueert ongestructureerde tekst en retourneert voor elke record een taal-id met een score die de sterkte van de analyse in een AI-verrijkingspijplijn in Azure Cognitive Search aangeeft.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8439788c63ec1b9feaea148ab52aba498791dc12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045014"
---
#   <a name="language-detection-cognitive-skill"></a>Cognitieve vaardigheid in taaldetectie

De **taaldetectievaardigheid** detecteert de taal van invoertekst en rapporteert één taalcode voor elk document dat op de aanvraag wordt ingediend. De taalcode wordt gekoppeld aan een score die de sterkte van de analyse aangeeft. Deze vaardigheid maakt gebruik van de machine learning-modellen van [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in Cognitive Services.

Deze mogelijkheid is vooral handig wanneer u de taal van de tekst moet verstrekken als input voor andere vaardigheden (bijvoorbeeld de [vaardigheid sentimentanalyse](cognitive-search-skill-sentiment.md) of [tekstgesplitste vaardigheid).](cognitive-search-skill-textsplit.md)

Taaldetectie maakt gebruik van de bibliotheken voor verwerking van natuurlijke taal van Bing, die het aantal [ondersteunde talen en regio's](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) dat wordt vermeld voor Text Analytics overschrijdt. De exacte lijst van talen wordt niet gepubliceerd, maar bevat alle veelgesproken talen, plus varianten, dialecten en sommige regionale en culturele talen. Als u inhoud hebt uitgedrukt in een minder vaak gebruikte taal, u [de API voor taaldetectie proberen](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) om te zien of een code wordt geretourneerd. Het antwoord voor talen die `unknown`niet kunnen worden gedetecteerd is .

> [!NOTE]
> Terwijl u het bereik uitbreidt door de frequentie van de verwerking te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureerbare bron voor cognitive services toevoegen.](cognitive-search-attach-cognitive-services.md) Er worden kosten in rekening gebracht bij het aanroepen van API's in Cognitive Services en voor het extraheren van afbeeldingen als onderdeel van de fase van het kraken van documenten in Azure Cognitive Search. Er zijn geen kosten voor tekstextractie uit documenten.
>
> Uitvoering van ingebouwde vaardigheden wordt in rekening gebracht tegen de bestaande [Cognitive Services pay-as-you-go prijs.](https://azure.microsoft.com/pricing/details/cognitive-services/) De prijzen voor imageextractie worden beschreven op de [prijspagina azure cognitive search.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Gegevenslimieten
De maximale grootte van een record moet 50.000 tekens zijn, gemeten door [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Als u uw gegevens moet opsplitsen voordat u deze naar de taaldetectievaardigheid stuurt, u de [vaardigheid Text Split](cognitive-search-skill-textsplit.md)gebruiken.

## <a name="skill-inputs"></a>Vaardigheidsingangen

Parameters zijn hoofdlettergevoelig.

| Invoer     | Beschrijving |
|--------------------|-------------|
| tekst | De te analyseren tekst.|

## <a name="skill-outputs"></a>Vaardigheidsuitvoer

| Uitvoernaam    | Beschrijving |
|--------------------|-------------|
| languageCode | De ISO 6391 taalcode voor de geïdentificeerde taal. Bijvoorbeeld "en". |
| languageName | De naam van de taal. Bijvoorbeeld "Engels". |
| Score | Een waarde tussen 0 en 1. De waarschijnlijkheid dat taal correct wordt geïdentificeerd. De score kan lager zijn dan 1 als de zin gemengde talen heeft.  |

##  <a name="sample-definition"></a>Voorbeelddefinitie

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

##  <a name="sample-input"></a>Voorbeeldinvoer

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


## <a name="error-cases"></a>Foutgevallen
Als tekst wordt uitgedrukt in een niet-ondersteunde taal, wordt er een fout gegenereerd en wordt er geen taal-id geretourneerd.

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
