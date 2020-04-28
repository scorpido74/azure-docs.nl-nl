---
title: Sleuteltermextractie cognitieve vaardigheid
titleSuffix: Azure Cognitive Search
description: Hiermee wordt ongestructureerde tekst geëvalueerd en voor elke record een lijst met sleutel zinnen in een AI-pijp lijn in azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ccdd25d82af2b4893260af18dac818816d9e4579
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "72791979"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Sleuteltermextractie cognitieve vaardigheid

Met de **Sleuteltermextractie** vaardigheid wordt ongestructureerde tekst geëvalueerd, en voor elke record wordt een lijst met sleutel zinnen geretourneerd. Deze vaardigheid maakt gebruik van de machine learning modellen van [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in cognitive Services.

Deze mogelijkheid is handig als u snel de belangrijkste pratende punten in de record moet identificeren. Als er bijvoorbeeld een invoer tekst is, is de levens middelen Delicious en zijn er fantastische mede werkers. de service retourneert "voedsel" en "fantastische mede werkers".

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureer bare Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in azure Cognitive Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met prijzen voor Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Micro soft. skills. Text. KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Gegevenslimieten
De maximale grootte van een record moet 50.000 tekens zijn, zoals gemeten [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)door. Als u uw gegevens moet opsplitsen voordat u deze naar de sleutel woord groep verstuurt, kunt u overwegen de [Kwalificatie tekst splitsen](cognitive-search-skill-textsplit.md)te gebruiken.

## <a name="skill-parameters"></a>Vaardigheids parameters

Parameters zijn hoofdlettergevoelig.

| Invoer                | Beschrijving |
|---------------------|-------------|
| defaultLanguageCode | Beschrijving De taal code die moet worden toegepast op documenten die geen taal expliciet opgeven.  Als de standaard taal code niet wordt opgegeven, wordt Engels (en) gebruikt als de standaard taal code. <br/> Bekijk de [volledige lijst met ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | Beschrijving Het maximum aantal sleutel zinnen dat moet worden geproduceerd. |

## <a name="skill-inputs"></a>Vaardigheids invoer

| Invoer     | Beschrijving |
|--------------------|-------------|
| tekst | De tekst die moet worden geanalyseerd.|
| languageCode  |  Een teken reeks die de taal van de records aangeeft. Als deze para meter niet wordt opgegeven, wordt de standaardtaal code gebruikt voor het analyseren van de records. <br/>[Volledige lijst met ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) weer geven|

##  <a name="sample-definition"></a>Voorbeeld definitie

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

##  <a name="sample-input"></a>Voorbeeld invoer

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
Als u een niet-ondersteunde taal code opgeeft, wordt er een fout gegenereerd en worden er geen sleutel zinnen geëxtraheerd.
Als uw tekst leeg is, wordt er een waarschuwing gegenereerd.
Als uw tekst groter is dan 50.000 tekens, worden alleen de eerste 50.000 tekens geanalyseerd en wordt er een waarschuwing gegeven.

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
