---
title: Key Phrase Extractie cognitieve vaardigheid
titleSuffix: Azure Cognitive Search
description: Evalueert ongestructureerde tekst en retourneert voor elke record een lijst met belangrijke zinnen in een AI-verrijkingspijplijn in Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ccdd25d82af2b4893260af18dac818816d9e4579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791979"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Key Phrase Extractie cognitieve vaardigheid

De **vaardigheid Sleutelzinextractie** evalueert ongestructureerde tekst en geeft voor elke record een lijst met sleutelzinnen als resultaat. Deze vaardigheid maakt gebruik van de machine learning-modellen van [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in Cognitive Services.

Deze mogelijkheid is handig als u snel de belangrijkste gesprekspunten in de record moet identificeren. Bijvoorbeeld, gezien input tekst "Het eten was heerlijk en er waren geweldig personeel", de service keert terug "eten" en "geweldig personeel".

> [!NOTE]
> Terwijl u het bereik uitbreidt door de frequentie van de verwerking te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureerbare bron voor cognitive services toevoegen.](cognitive-search-attach-cognitive-services.md) Er worden kosten in rekening gebracht bij het aanroepen van API's in Cognitive Services en voor het extraheren van afbeeldingen als onderdeel van de fase van het kraken van documenten in Azure Cognitive Search. Er zijn geen kosten voor tekstextractie uit documenten.
>
> Uitvoering van ingebouwde vaardigheden wordt in rekening gebracht tegen de bestaande [Cognitive Services pay-as-you-go prijs.](https://azure.microsoft.com/pricing/details/cognitive-services/) De prijzen voor imageextractie worden beschreven op de [prijspagina azure cognitive search.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Gegevenslimieten
De maximale grootte van een record moet 50.000 tekens zijn, gemeten door [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Als u uw gegevens moet opsplitsen voordat u deze naar de trefwoordextractor van de sleutelzin stuurt, u overwegen de [vaardigheid Text Split te](cognitive-search-skill-textsplit.md)gebruiken.

## <a name="skill-parameters"></a>Vaardigheidsparameters

Parameters zijn hoofdlettergevoelig.

| Invoer                | Beschrijving |
|---------------------|-------------|
| standaardLanguageCode | (Optioneel) De taalcode die moet worden toegepast op documenten die geen expliciet taal opgeven.  Als de standaardtaalcode niet is opgegeven, wordt Engels (nl) gebruikt als standaardtaalcode. <br/> Zie [Volledige lijst met ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Optioneel) Het maximum aantal sleutelzinnen dat moet worden geproduceerd. |

## <a name="skill-inputs"></a>Vaardigheidsingangen

| Invoer     | Beschrijving |
|--------------------|-------------|
| tekst | De te analyseren tekst.|
| languageCode  |  Een tekenreeks die de taal van de records aangeeft. Als deze parameter niet is opgegeven, wordt de standaardtaalcode gebruikt om de records te analyseren. <br/>Zie [Volledige lijst met ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Voorbeelddefinitie

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
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
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Fouten en waarschuwingen
Als u een niet-ondersteunde taalcode opgeeft, wordt er een fout gegenereerd en worden sleutelzinnen niet geëxtraheerd.
Als uw tekst leeg is, wordt er een waarschuwing weergegeven.
Als uw tekst groter is dan 50.000 tekens, worden alleen de eerste 50.000 tekens geanalyseerd en wordt er een waarschuwing uitgegeven.

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
