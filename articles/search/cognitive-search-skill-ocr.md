---
title: OCR cognitieve vaardigheid
titleSuffix: Azure Cognitive Search
description: Extraheren van tekst uit afbeeldingsbestanden met behulp van ocr (optical character recognition) in een verrijkingspijplijn in Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bdb510113a8d65ac04b54e77158f46d03cccd9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791926"
---
# <a name="ocr-cognitive-skill"></a>OCR cognitieve vaardigheid

De **ocr-vaardigheid (Optical character recognition)** herkent afgedrukte en handgeschreven tekst in afbeeldingsbestanden. Deze vaardigheid maakt gebruik van de machine learning modellen die door [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) in Cognitive Services. De **OCR-vaardigheid** wordt toegewezen aan de volgende functionaliteit:

+ De ["OCR"-API](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-optical-character-recognition-api) wordt gebruikt voor andere talen dan het Engels. 
+ Voor Het Engels wordt de nieuwe ["Lees"](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api) API gebruikt.

Met **de OCR-vaardigheid** worden tekst uit afbeeldingsbestanden geëxtraheerd. Ondersteunde bestandsindelingen zijn:

+ . Jpeg
+ . Jpg
+ . Png
+ . Bmp
+ . Gif
+ . Tiff

> [!NOTE]
> Terwijl u het bereik uitbreidt door de frequentie van de verwerking te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureerbare bron voor cognitive services toevoegen.](cognitive-search-attach-cognitive-services.md) Er worden kosten in rekening gebracht bij het aanroepen van API's in Cognitive Services en voor het extraheren van afbeeldingen als onderdeel van de fase van het kraken van documenten in Azure Cognitive Search. Er zijn geen kosten voor tekstextractie uit documenten.
>
> Uitvoering van ingebouwde vaardigheden wordt in rekening gebracht tegen de bestaande [Cognitive Services pay-as-you-go prijs.](https://azure.microsoft.com/pricing/details/cognitive-services/) De prijzen voor imageextractie worden beschreven op de [prijspagina azure cognitive search.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="skill-parameters"></a>Vaardigheidsparameters

Parameters zijn hoofdlettergevoelig.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| detecterenOriëntatie | Hiermee u de beeldoriëntatie automatisch detecteren. <br/> Geldige waarden: waar / onwaar.|
|standaardLanguageCode | <p>  Taalcode van de invoertekst. Enkele ondersteunde talen: <br/> zh-Hans (ChineesVereenvoudigd) <br/> zh-Hant (ChineesTraditioneel) <br/>cs (Tsjechisch) <br/>da (Deens) <br/>nl <br/>nl (Engels) <br/>fi (Fins)  <br/>fr (Frans) <br/>  de (Duitse) <br/>el (Grieks) <br/> hu (Hongaars) <br/> het (Italiaans) <br/>  ja (Japans) <br/> ko (Koreaans) <br/> nb (Noors) <br/>   pl (Pools) <br/> pt (Portugees) <br/>  ru (Russisch) <br/>  es (Spaans) <br/>  sv (Zweeds) <br/>  tr (Turks) <br/> ar (Arabisch) <br/> ro (Roemeens) <br/> sr-Cyrl (ServischCyrillisch) <br/> sr-Latn (ServischLatijn) <br/>  sk (Slowaaks). <br/>  unk (Onbekend) <br/><br/> Als de taalcode niet is opgegeven of niet ig is, wordt de taal ingesteld op Engels. Als de taal expliciet is ingesteld op 'unk', wordt de taal automatisch gedetecteerd. </p> |
|lineEnding | De waarde die moet worden gebruikt tussen elke gedetecteerde regel. Mogelijke waarden: 'Space','CarriageReturn','LineFeed'.  De standaardinstelling is 'Ruimte' |

Voorheen was er een parameter genaamd "textExtractionAlgorithm" voor het opgeven of de vaardigheid "gedrukte" of "handgeschreven" tekst moet extraheren.  Deze parameter is afgeschaft en niet langer nodig omdat het nieuwste Read API-algoritme in staat is om beide typen tekst tegelijk te extraheren.  Als uw vaardigheidsdefinitie deze parameter al bevat, hoeft u deze niet te verwijderen, maar deze wordt niet meer gebruikt en worden beide typen tekst in de toekomst geëxtraheerd, ongeacht waardeze is ingesteld.

## <a name="skill-inputs"></a>Vaardigheidsingangen

| Invoernaam      | Beschrijving                                          |
|---------------|------------------------------------------------------|
| installatiekopie         | Complex Type. Werkt momenteel alleen met het veld '/document/normalized_images', ```imageAction``` geproduceerd door de Azure ```none```Blob-indexer wanneer deze is ingesteld op een andere waarde dan . Zie het [voorbeeld](#sample-output) voor meer informatie.|


## <a name="skill-outputs"></a>Vaardigheidsuitvoer
| Uitvoernaam     | Beschrijving                   |
|---------------|-------------------------------|
| tekst          | Platte tekst uit de afbeelding.   |
| lay-outTekst    | Complex type dat de geëxtraheerde tekst beschrijft en de locatie waar de tekst is gevonden.|


## <a name="sample-definition"></a>Voorbeelddefinitie

```json
{
  "skills": [
    {
      "description": "Extracts text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": null,
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text",
          "targetName": "myText"
        },
        {
          "name": "layoutText",
          "targetName": "myLayoutText"
        }
      ]
    }
  ]
}
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Voorbeeldtekst en indelingTekstuitvoer

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Voorbeeld: Tekst die uit ingesloten afbeeldingen wordt geëxtraheerd, samenvoegen met de inhoud van het document.

Een veelgebruikte use case voor Tekstfusie is de mogelijkheid om de tekstuele weergave van afbeeldingen (tekst uit een OCR-vaardigheid of het bijschrift van een afbeelding) samen te voegen in het inhoudsveld van een document.

Met de volgende voorbeeldvaardigheidsset wordt een *merged_text* veld. Dit veld bevat de tekstuele inhoud van uw document en de OCRed-tekst van elk van de afbeeldingen die in dat document zijn ingesloten.

#### <a name="request-body-syntax"></a>Syntaxis aanvraagbody
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
In het bovenstaande vaardigheidssetvoorbeeld wordt ervan uitgegaan dat er een normaal-afbeeldingsveld bestaat. Als u dit veld wilt genereren, stelt u de *imageAction-configuratie* in de definitie van de indexer in om *genormaliseerde afbeeldingen* te genereren zoals hieronder wordt weergegeven:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Zie ook
+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [TextMerger vaardigheid](cognitive-search-skill-textmerger.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Indexeerfunctie maken (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
