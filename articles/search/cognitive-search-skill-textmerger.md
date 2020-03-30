---
title: Cognitieve vaardigheid voor tekst samenvoegen
titleSuffix: Azure Cognitive Search
description: Tekst uit een verzameling velden samenvoegen tot één geconsolideerd veld. Gebruik deze cognitieve vaardigheid in een AI-verrijkingspijplijn in Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 98ea416305f080850d85498f74693eb2d45b0944
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162341"
---
#   <a name="text-merge-cognitive-skill"></a>Cognitieve vaardigheid voor tekst samenvoegen

Met de vaardigheid **Tekst samenvoegen** wordt tekst uit een verzameling velden samengevoegd tot één veld. 

> [!NOTE]
> Deze vaardigheid is niet gebonden aan een API voor Cognitive Services en u wordt niet in rekening gebracht voor het gebruik ervan. U moet echter nog steeds [een resource voor Cognitive Services koppelen](cognitive-search-attach-cognitive-services.md)om de optie **Gratis** resource te overschrijven die u beperkt tot een klein aantal dagelijkse verrijkingen per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Vaardigheidsparameters

Parameters zijn hoofdlettergevoelig.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| invoegenPreTag  | Tekenreeks die moet worden opgenomen voor elke invoeging. De standaardwaarde is `" "`. Als u de ruimte wilt `""`weglaten, stelt u de waarde in op .  |
| Posttag invoegen | Tekenreeks die moet worden opgenomen na elke invoeging. De standaardwaarde is `" "`. Als u de ruimte wilt `""`weglaten, stelt u de waarde in op .  |


##  <a name="sample-input"></a>Voorbeeldinvoer
Een JSON-document met bruikbare invoer voor deze vaardigheid zou kunnen zijn:

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28]
      }
    }
  ]
}
```

##  <a name="sample-output"></a>Voorbeelduitvoer
In dit voorbeeld wordt de uitvoer van de vorige invoer `" "`weergegeven, ervan uitgaande dat de *invoegtoepassingPreTag* is ingesteld op , en *posttag invoegen* is ingesteld op `""`. 

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "mergedText": "The quick brown fox jumps over the lazy dog"
      }
    }
  ]
}
```

## <a name="extended-sample-skillset-definition"></a>Uitgebreide definitie van voorbeeldvaardigheden

Een veelvoorkomend scenario voor het gebruik van tekstsamenvoegen is het samenvoegen van de tekstuele weergave van afbeeldingen (tekst uit een OCR-vaardigheid of het bijschrift van een afbeelding) in het inhoudsveld van een document. 

In de volgende voorbeeldvaardigheidsset wordt de OCR-vaardigheid gebruikt om tekst uit afbeeldingen te extraheren die in het document zijn ingesloten. Vervolgens wordt een *merged_text* veld gemaakt dat zowel originele als OCRed-tekst van elke afbeelding bevat. U meer informatie over de OCR vaardigheid [hier](https://docs.microsoft.com/azure/search/cognitive-search-skill-ocr).

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
In het bovenstaande voorbeeld wordt ervan uitgegaan dat er een normaal beeldveld bestaat. Als u het veld normaliseerde afbeeldingen wilt genereren, stelt u de *imageAction-configuratie* in de definitie van de indexer in om *genormaliseerde afbeeldingen* te genereren, zoals hieronder wordt weergegeven:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":{
    "configuration":{
        "dataToExtract":"contentAndMetadata",
        "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Indexeerfunctie maken (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
