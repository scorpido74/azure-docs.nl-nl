---
title: Cognitieve vaardigheid voor documentextractie (voorbeeld)
titleSuffix: Azure Cognitive Search
description: Haalt inhoud uit een bestand in de verrijkingspijplijn. Deze vaardigheid is momenteel in openbare preview.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 0f67caad03c4ebd1cf8f3721f377d8362219016a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76837728"
---
# <a name="document-extraction-cognitive-skill"></a>Cognitieve vaardigheid voor documentextractie

> [!IMPORTANT] 
> Deze vaardigheid is momenteel in openbare preview. Preview-functionaliteit wordt geleverd zonder overeenkomst op serviceniveau en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. Er is momenteel geen portal of .NET SDK-ondersteuning.

Met **de vaardigheid Documentextractie** haalt u inhoud uit een bestand in de verrijkingspijplijn. Hierdoor u profiteren van de stap voor het extraheren van documenten die normaal gesproken plaatsvindt vóór de uitvoering van de skillset met bestanden die kunnen worden gegenereerd door andere vaardigheden.

> [!NOTE]
> Terwijl u het bereik uitbreidt door de frequentie van de verwerking te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureerbare bron voor cognitive services toevoegen.](cognitive-search-attach-cognitive-services.md) Er worden kosten gegenereerd bij het aanroepen van API's in Cognitive Services en voor het extraheren van afbeeldingen als onderdeel van de fase van het kraken van documenten bij indexering. Er zijn geen kosten voor tekstextractie uit documenten.
>
> Uitvoering van ingebouwde vaardigheden wordt in rekening gebracht tegen de bestaande [Cognitive Services pay-as-you-go prijs.](https://azure.microsoft.com/pricing/details/cognitive-services/) De prijzen voor afbeeldingsextractie worden beschreven op de [prijspagina](https://go.microsoft.com/fwlink/?linkid=2042400).
## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>Vaardigheidsparameters

Parameters zijn hoofdlettergevoelig.

| Invoer            | Toegestane waarden | Beschrijving |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Instellen `default` op voor documentextractie uit bestanden die geen zuivere tekst of json zijn. Ingesteld `text` om de prestaties van platte tekstbestanden te verbeteren. Ingesteld `json` op het extraheren van gestructureerde inhoud uit json-bestanden. Als `parsingMode` deze niet expliciet wordt gedefinieerd, `default`wordt deze ingesteld op . |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Stel `contentAndMetadata` in om alle metagegevens en tekstuele inhoud uit elk bestand te extraheren. Ingesteld `allMetadata` om alleen de [specifieke metagegevens van het inhoudstype](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) te extraheren (bijvoorbeeld metagegevens die uniek zijn voor alleen png-bestanden). Als `dataToExtract` deze niet expliciet wordt gedefinieerd, `contentAndMetadata`wordt deze ingesteld op . |
| `configuration` | Zie hieronder. | Een woordenboek met optionele parameters die de manier waarop de documentextractie wordt uitgevoerd aanpassen. Zie de onderstaande tabel voor beschrijvingen van ondersteunde configuratie-eigenschappen. |

| Configuratieparameter   | Toegestane waarden | Beschrijving |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Instellen `none` op ingesloten afbeeldingen of afbeeldingsbestanden in de gegevensset te negeren. Dit is de standaardinstelling. <br/>Voor [beeldanalyse met behulp van cognitieve vaardigheden,](cognitive-search-concept-image-scenarios.md)ingesteld op `generateNormalizedImages` de vaardigheid maken van een array van genormaliseerde beelden als onderdeel van document kraken. Deze actie `parsingMode` vereist dat `default` `dataToExtract` is ingesteld `contentAndMetadata`op en is ingesteld op . Een genormaliseerde afbeelding verwijst naar extra verwerking wat resulteert in uniforme beelduitvoer, formaat en gedraaid om consistente weergave te bevorderen wanneer u afbeeldingen opneemt in visuele zoekresultaten (bijvoorbeeld foto's van dezelfde grootte in een grafiekbesturingselement zoals te zien in de [JFK-demo).](https://github.com/Microsoft/AzureSearch_JFK_Files) Deze informatie wordt voor elke afbeelding gegenereerd wanneer u deze optie gebruikt.  <br/>Als u `generateNormalizedImagePerPage`instelt op , PDF-bestanden zullen anders worden behandeld in dat in plaats van het extraheren van ingesloten afbeeldingen, zal elke pagina worden weergegeven als een afbeelding en dienovereenkomstig genormaliseerd.  Niet-PDF-bestandstypen worden hetzelfde `generateNormalizedImages` behandeld als wanneer deze is ingesteld.
| `normalizedImageMaxWidth` | Elk geheel getal tussen 50-10000 | De maximale breedte (in pixels) voor genormaliseerde gegenereerde afbeeldingen. De standaardwaarde is 2000. | 
| `normalizedImageMaxHeight` | Elk geheel getal tussen 50-10000 | De maximale hoogte (in pixels) voor genormaliseerde gegenereerde afbeeldingen. De standaardwaarde is 2000. |

> [!NOTE]
> De standaardwaarde van 2000 pixels voor de genormaliseerde afbeeldingen maximale breedte en hoogte is gebaseerd op de maximale grootte die wordt ondersteund door de [OCR-vaardigheid](cognitive-search-skill-ocr.md) en de [vaardigheid voor beeldanalyse.](cognitive-search-skill-image-analysis.md) De [OCR-vaardigheid](cognitive-search-skill-ocr.md) ondersteunt een maximale breedte en hoogte van 4200 voor niet-Engelse talen en 10000 voor Engels.  Als u de maximale limieten verhoogt, kan de verwerking mislukken bij grotere afbeeldingen, afhankelijk van uw skillset-definitie en de taal van de documenten. 
## <a name="skill-inputs"></a>Vaardigheidsingangen

| Invoernaam     | Beschrijving |
|--------------------|-------------|
| file_data | Het bestand waaruit inhoud moet worden geëxtraheerd. |

De "file_data"-invoer moet een als volgt gedefinieerd voorwerp zijn:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Dit bestandsverwijzingsobject kan op drie manieren worden gegenereerd:

 - De `allowSkillsetToReadFileData` parameter op de definitie van uw indexer inte stellen op 'waar'.  Hiermee wordt een `/document/file_data` pad gemaakt dat een object is dat de oorspronkelijke bestandsgegevens vertegenwoordigt die zijn gedownload van uw blobgegevensbron. Deze parameter is alleen van toepassing op gegevens in Blob-opslag.

 - De `imageAction` parameter op de definitie van uw `none`indexer instellen op een andere waarde dan .  Dit creëert een reeks afbeeldingen die de vereiste conventie voor invoer aan deze `/document/normalized_images/*`vaardigheid volgt als ze individueel worden doorgegeven (d.w.z. ).

 - Het hebben van een aangepaste vaardigheid terug te keren een json object gedefinieerd precies zoals hierboven.  De `$type` parameter moet precies `file` zijn `data` ingesteld en de parameter moet de basis 64 gecodeerde bytearraygegevens van de bestandsinhoud zijn.

## <a name="skill-outputs"></a>Vaardigheidsuitvoer

| Uitvoernaam    | Beschrijving |
|--------------|-------------|
| content | De tekstuele inhoud van het document. |
| normalized_images | Wanneer `imageAction` de is ingesteld op `none`een andere waarde dan, zal de nieuwe *normalized_images* veld een array van afbeeldingen bevatten. Zie [de documentatie voor het extraheren van afbeeldingen](cognitive-search-concept-image-scenarios.md) voor meer informatie over de uitvoerindeling van elke afbeelding. |

##  <a name="sample-definition"></a>Voorbeelddefinitie

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "content"
      },
      {
        "name": "normalized_images",
        "targetName": "normalized_images"
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
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```


##  <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Informatie verwerken en extraheren uit afbeeldingen in cognitieve zoekscenario's](cognitive-search-concept-image-scenarios.md)