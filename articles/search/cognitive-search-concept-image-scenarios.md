---
title: Extraheren van tekst uit afbeeldingen
titleSuffix: Azure Cognitive Search
description: Tekst en andere informatie uit afbeeldingen in Azure Cognitive Search-pijplijnen verwerken en extraheren.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 98054060210f55803d6e2811e1f494fd3ff00e48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76838255"
---
# <a name="how-to-process-and-extract-information-from-images-in-ai-enrichment-scenarios"></a>Informatie verwerken en extraheren uit afbeeldingen in AI-verrijkingsscenario's

Azure Cognitive Search heeft verschillende mogelijkheden voor het werken met afbeeldingen en afbeeldingsbestanden. Tijdens het kraken van documenten u de parameter *imageAction* gebruiken om tekst uit foto's of afbeeldingen te extraheren die alfanumerieke tekst bevatten, zoals het woord STOP in een stopteken. Andere scenario's zijn het genereren van een tekstweergave van een afbeelding, zoals 'paardebloem' voor een foto van een paardebloem of de kleur 'geel'. U ook metagegevens over de afbeelding extraheren, zoals de grootte ervan.

Dit artikel behandelt beeldverwerking in meer detail en biedt richtlijnen voor het werken met afbeeldingen in een AI-verrijkingspijplijn.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Genormaliseerde afbeeldingen krijgen

Als onderdeel van het kraken van documenten, zijn er een nieuwe set indexerconfiguratieparameters voor het verwerken van afbeeldingsbestanden of afbeeldingen die zijn ingesloten in bestanden. Deze parameters worden gebruikt om afbeeldingen te normaliseren voor verdere downstream verwerking. Het normaliseren van afbeeldingen maakt ze uniformer. Grote afbeeldingen worden aangepast aan een maximale hoogte en breedte om ze bruikbaar te maken. Voor afbeeldingen die metagegevens op oriëntatie aanbieden, wordt de beeldrotatie aangepast voor verticaal laden. Aanpassingen met ametagegevens worden vastgelegd in een complex type dat voor elke afbeelding is gemaakt. 

U de normalisatie van het beeld niet uitschakelen. Vaardigheden die herhalen over beelden verwachten genormaliseerde beelden. Als u afbeeldingsnormalisatie inschakelt op een indexer, moet een skillset aan die indexer worden gekoppeld.

| Configuratieparameter | Beschrijving |
|--------------------|-------------|
| imageAction   | Stel in op 'niets' als er geen actie moet worden ondernomen wanneer ingesloten afbeeldingen of afbeeldingsbestanden worden aangetroffen. <br/>Stel in op "genererenGenormaliseerde afbeeldingen" om een reeks genormaliseerde afbeeldingen te genereren als onderdeel van het kraken van documenten.<br/>Stel in op "genererenNormalizedImagePerPage" om een array van genormaliseerde afbeeldingen te genereren, waarbij voor PDF's in uw gegevensbron elke pagina wordt weergegeven in één uitvoerafbeelding.  De functionaliteit is hetzelfde als "generateNormalizedImages" voor niet-PDF-bestandstypen.<br/>Voor elke optie die niet "geen" is, worden de afbeeldingen in het *normalized_images* veld weergegeven. <br/>De standaardinstelling is 'geen'. Deze configuratie is alleen relevant voor blob-gegevensbronnen, wanneer 'dataToExtract' is ingesteld op 'contentAndMetadata'. <br/>Uit een bepaald document worden maximaal 1000 afbeeldingen gehaald. Als er meer dan 1000 afbeeldingen in een document staan, worden de eerste 1000 geëxtraheerd en wordt er een waarschuwing gegenereerd. |
|  genormaliseerdImageMaxWidth | De maximale breedte (in pixels) voor genormaliseerde gegenereerde afbeeldingen. De standaardwaarde is 2000. De maximaal toegestane waarde is 10000. | 
|  genormaliseerdImageMaxHeight | De maximale hoogte (in pixels) voor genormaliseerde gegenereerde afbeeldingen. De standaardwaarde is 2000. De maximaal toegestane waarde is 10000.|

> [!NOTE]
> Als u de eigenschap *imageAction* instelt op iets anders dan 'geen', u de eigenschap *parsingMode* niet instellen op iets anders dan 'standaard'.  U mag slechts één van deze twee eigenschappen instellen op een niet-standaardwaarde in uw indexerconfiguratie.

Stel de parameter **parsingMode** in op `json` (om elke `jsonArray` blob te indexeren als één document) of (als uw blobs JSON-arrays bevatten en elk element van een array als een afzonderlijk document moet worden behandeld).

De standaardwaarde van 2000 pixels voor de genormaliseerde afbeeldingen maximale breedte en hoogte is gebaseerd op de maximale grootte die wordt ondersteund door de [OCR-vaardigheid](cognitive-search-skill-ocr.md) en de [vaardigheid voor beeldanalyse.](cognitive-search-skill-image-analysis.md) De [OCR-vaardigheid](cognitive-search-skill-ocr.md) ondersteunt een maximale breedte en hoogte van 4200 voor niet-Engelse talen en 10000 voor Engels.  Als u de maximale limieten verhoogt, kan de verwerking mislukken bij grotere afbeeldingen, afhankelijk van uw skillset-definitie en de taal van de documenten. 

U geeft de imageAction in de [definitie van indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) als volgt op:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

Wanneer de *imageAction* is ingesteld op een andere waarde dan "geen", bevat het nieuwe *normalized_images* veld een array met afbeeldingen. Elke afbeelding is een complex type met de volgende leden:

| Afbeeldingslid       | Beschrijving                             |
|--------------------|-----------------------------------------|
| data               | BASE64 gecodeerde tekenreeks van de genormaliseerde afbeelding in JPEG-formaat.   |
| breedte              | Breedte van de genormaliseerde afbeelding in pixels. |
| hoogte             | Hoogte van de genormaliseerde afbeelding in pixels. |
| origineelBreedte      | De oorspronkelijke breedte van de afbeelding vóór normalisatie. |
| origineelHoogte      | De oorspronkelijke hoogte van de afbeelding vóór normalisatie. |
| rotatieFromOriginal |  Rotatie tegen de klok in in graden die is opgetreden om de genormaliseerde afbeelding te maken. Een waarde tussen 0 graden en 360 graden. In deze stap worden de metagegevens van de afbeelding gelezen die wordt gegenereerd door een camera of scanner. Meestal een veelvoud van 90 graden. |
| inhoudOffset | Het teken wordt gecompenseerd in het inhoudsveld waar de afbeelding uit is gehaald. Dit veld is alleen van toepassing op bestanden met ingesloten afbeeldingen. |
| Pagenumber | Als de afbeelding is geëxtraheerd of uit een PDF is gerenderd, bevat dit veld het paginanummer in de PDF waaruit het is geëxtraheerd of weergegeven, vanaf 1.  Als de afbeelding niet van een PDF afkomstig is, is dit veld 0.  |

 Steekproefwaarde van *normalized_images*:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500,
    "pageNumber": 2
  }
]
```

## <a name="image-related-skills"></a>Beeldgerelateerde vaardigheden

Er zijn twee ingebouwde cognitieve vaardigheden die foto's als input nemen: [OCR](cognitive-search-skill-ocr.md) en [Image Analysis](cognitive-search-skill-image-analysis.md). 

Momenteel werken deze vaardigheden alleen met afbeeldingen die zijn gegenereerd door de stap voor het kraken van documenten. Als zodanig is `"/document/normalized_images"`de enige ondersteunde input .

### <a name="image-analysis-skill"></a>Vaardigheid in beeldanalyse

De [vaardigheid Image Analysis](cognitive-search-skill-image-analysis.md) haalt een uitgebreide set visuele functies uit op basis van de afbeeldingsinhoud. U bijvoorbeeld een bijschrift genereren uit een afbeelding, tags genereren of beroemdheden en oriëntatiepunten identificeren.

### <a name="ocr-skill"></a>OCR-vaardigheid

Met [de OCR-vaardigheid](cognitive-search-skill-ocr.md) worden tekst uit afbeeldingsbestanden zoals JPGs, PG's en bitmaps geëxtraheerd. Het kan tekst en lay-outinformatie extraheren. De lay-outinformatie biedt selectiekaders voor elk van de geïdentificeerde tekenreeksen.

## <a name="embedded-image-scenario"></a>Ingesloten afbeeldingsscenario

Een veelvoorkomend scenario omvat het maken van één tekenreeks met alle bestandsinhoud, zowel tekst als tekst van oorsprong van afbeeldingen, door de volgende stappen uit te voeren:  

1. [Extract normalized_images](#get-normalized-images)
1. De OCR-vaardigheid `"/document/normalized_images"` uitvoeren met als invoer
1. Voeg de tekstweergave van die afbeeldingen samen met de ruwe tekst die uit het bestand wordt gehaald. U de vaardigheid [Tekst samenvoegen](cognitive-search-skill-textmerger.md) gebruiken om beide tekstsegmenten samen te voegen tot één grote tekenreeks.

Met de volgende voorbeeldvaardigheidsset wordt een *merged_text* veld gemaakt met de tekstuele inhoud van het document. Het bevat ook de OCRed-tekst van elk van de ingesloten afbeeldingen. 

#### <a name="request-body-syntax"></a>Syntaxis van de hoofdtekst van het verzoek
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

Nu u een merged_text veld hebt, u het toewijzen als een doorzoekbaar veld in uw indexerdefinitie. Alle inhoud van uw bestanden, inclusief de tekst van de afbeeldingen, is doorzoekbaar.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Selectiekaders met geëxtraheerde tekst visualiseren

Een ander veelvoorkomend scenario is het visualiseren van de lay-outgegevens van zoekresultaten. U bijvoorbeeld markeren waar een stuk tekst in een afbeelding is gevonden als onderdeel van uw zoekresultaten.

Aangezien de OCR-stap wordt uitgevoerd op de genormaliseerde afbeeldingen, bevinden de indelingscoördinaten zich in de genormaliseerde afbeeldingsruimte. Bij het weergeven van de genormaliseerde afbeelding is de aanwezigheid van coördinaten over het algemeen geen probleem, maar in sommige situaties wilt u misschien de oorspronkelijke afbeelding weergeven. Converteer in dit geval elk van de coördinatenpunten in de lay-out naar het oorspronkelijke afbeeldingscoördinaat. 

Als helper u als helper genormaliseerde coördinaten omzetten in de oorspronkelijke coördinatenruimte:

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```

## <a name="see-also"></a>Zie ook
+ [Indexeren maken (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [Vaardigheid in beeldanalyse](cognitive-search-skill-image-analysis.md)
+ [OCR-vaardigheid](cognitive-search-skill-ocr.md)
+ [Groep tekst samenvoegen](cognitive-search-skill-textmerger.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Verrijkte velden in kaart brengen](cognitive-search-output-field-mapping.md)
