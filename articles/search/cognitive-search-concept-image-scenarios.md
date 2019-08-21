---
title: Tekst uit afbeeldingen verwerken en extra heren in cognitieve zoek opdracht-Azure Search
description: Verwerken en extra heren tekst en andere informatie uit afbeeldingen in cognitieve Zoek pijplijnen in Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.subservice: cognitive-search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: 216be226f3ce6d1a288462d1a1b79f2bb0d3efe5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639914"
---
#  <a name="how-to-process-and-extract-information-from-images-in-cognitive-search-scenarios"></a>Informatie over het verwerken en extra heren van afbeeldingen in cognitieve Zoek scenario's

Cognitieve Zoek opdrachten hebben verschillende mogelijkheden voor het werken met afbeeldingen en afbeeldings bestanden. Tijdens het kraken van documenten kunt u de para meter *imageAction* gebruiken om tekst te extra heren uit Foto's of afbeeldingen met alfanumerieke tekst, zoals het woord ' Stop ' in een stop teken. Andere scenario's zijn onder andere het genereren van een tekst weergave van een afbeelding, zoals ' Dandelion ' voor een foto van een Dandelion of de kleur geel. U kunt ook meta gegevens over de afbeelding extra heren, zoals de grootte.

In dit artikel vindt u meer informatie over de verwerking van afbeeldingen en richt lijnen voor het werken met afbeeldingen in een cognitieve Zoek pijplijn.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Genormaliseerde installatie kopieën ophalen

Als onderdeel van het kraken van documenten zijn er een nieuwe set indexer configuratie parameters voor het verwerken van afbeeldings bestanden of installatie kopieën die in bestanden zijn Inge sloten. Deze para meters worden gebruikt om installatie kopieën te normaliseren voor verdere downstream-verwerking. Door afbeeldingen te normaliseren, worden ze gelijkmatiger. Grote afbeeldingen worden aangepast aan de maximale hoogte en breedte om ze te kunnen gebruiken. Voor afbeeldingen die meta gegevens bevatten, wordt afbeeldings rotatie aangepast voor verticaal laden. Meta gegevens aanpassingen worden vastgelegd in een complex type dat voor elke afbeelding is gemaakt. 

U kunt het normaliseren van afbeeldingen niet uitschakelen. Vaardig heden die over installatie kopieën lopen, verwachten genormaliseerde installatie kopieën. Voor het inschakelen van installatie kopie normalisatie voor een Indexeer functie moet een vakkennisset aan die Indexeer functie worden gekoppeld.

| Configuratie parameter | Description |
|--------------------|-------------|
| imageAction   | Stel deze waarde in op geen als er geen actie moet worden ondernomen wanneer Inge sloten afbeeldingen of afbeeldings bestanden worden aangetroffen. <br/>Ingesteld op ' generateNormalizedImages ' om een matrix van genormaliseerde installatie kopieën te genereren als onderdeel van het kraken van documenten.<br/>Ingesteld op ' generateNormalizedImagePerPage ' om een matrix van genormaliseerde installatie kopieën te genereren, waarbij voor Pdf's in uw gegevens bron elke pagina wordt weer gegeven met één uitvoer afbeelding.  De functionaliteit is hetzelfde als ' generateNormalizedImages ' voor niet-PDF-bestands typen.<br/>Voor elke optie die niet ' geen ' is, worden de afbeeldingen weer gegeven in het veld *normalized_images* . <br/>De standaard waarde is geen. Deze configuratie is alleen relevant voor BLOB-gegevens bronnen, wanneer ' dataToExtract ' is ingesteld op ' contentAndMetadata '. <br/>Er worden Maxi maal 1000 installatie kopieën geëxtraheerd uit een bepaald document. Als er meer dan 1000 installatie kopieën in een document zijn, wordt de eerste 1000 geëxtraheerd en wordt er een waarschuwing gegenereerd. |
|  normalizedImageMaxWidth | De maximum breedte (in pixels) voor genormaliseerde afbeeldingen die worden gegenereerd. De standaard waarde is 2000.|
|  normalizedImageMaxHeight | De maximum hoogte (in pixels) voor genormaliseerde afbeeldingen die worden gegenereerd. De standaard waarde is 2000.|

> [!NOTE]
> Als u de eigenschap *imageAction* op een andere waarde dan ' geen ' instelt, kunt u de eigenschap *parsingMode* niet instellen op een andere waarde dan default.  U kunt slechts een van deze twee eigenschappen instellen op een niet-standaard waarde in de configuratie van de Indexeer functie.

Stel de para meter parsingMode `json` in op (om elke BLOB als één document te indexeren `jsonArray` ) of (als uw blobs JSON-matrices bevatten en u wilt dat elk element van een matrix als een afzonderlijk document wordt behandeld).

De standaard waarde van 2000 pixels voor de genormaliseerde afbeeldingen maximale breedte en hoogte is gebaseerd op de maximale grootte die wordt ondersteund door de [OCR-vaardigheid](cognitive-search-skill-ocr.md) en de vaardigheid van de [afbeeldings analyse](cognitive-search-skill-image-analysis.md). Als u de maximum limiet verhoogt, kan de verwerking mislukken voor de grotere installatie kopieën.


U geeft de imageAction op in de definitie van de [Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/create-indexer) als volgt:

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

Wanneer de *imageAction* is ingesteld op een andere waarde dan ' geen ', bevat het nieuwe *normalized_images* -veld een matrix met installatie kopieën. Elke afbeelding is een complex type met de volgende leden:

| Onderdeel van installatie kopie       | Description                             |
|--------------------|-----------------------------------------|
| data               | BASE64-gecodeerde teken reeks van de genormaliseerde afbeelding in JPEG-indeling.   |
| Breedte              | Breedte van de genormaliseerde afbeelding in pixels. |
| hoogte             | De hoogte van de genormaliseerde afbeelding in pixels. |
| originalWidth      | De oorspronkelijke breedte van de afbeelding vóór normalisatie. |
| originalHeight      | De oorspronkelijke hoogte van de afbeelding vóór normalisatie. |
| rotationFromOriginal |  Rotatie linksom in graden die is opgetreden tijdens het maken van de genormaliseerde afbeelding. Een waarde tussen 0 en 360 graden. Met deze stap worden de meta gegevens van de installatie kopie die wordt gegenereerd door een camera of scanner, gelezen. Doorgaans een veelvoud van 90 graden. |
| contentOffset | De teken verschuiving binnen het inhouds veld waaruit de afbeelding is geëxtraheerd. Dit veld is alleen van toepassing op bestanden met Inge sloten installatie kopieën. |
| pageNumber | Als de afbeelding is geëxtraheerd of gerenderd vanuit een PDF, bevat dit veld het pagina nummer in het PDF-bestand dat is geëxtraheerd of gerenderd, vanaf 1.  Als de afbeelding niet afkomstig is uit een PDF, is dit veld 0.  |

 Voorbeeld waarde van *normalized_images*:
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

## <a name="image-related-skills"></a>Image-gerelateerde vaardig heden

Er zijn twee ingebouwde cognitieve vaardig heden die afbeeldingen als invoer maken: [OCR](cognitive-search-skill-ocr.md) en [afbeeldings analyse](cognitive-search-skill-image-analysis.md). 

Momenteel werken deze vaardig heden alleen met installatie kopieën die zijn gegenereerd op basis van de stap voor het kraken van het document. Als zodanig is de enige invoer die wordt `"/document/normalized_images"`ondersteund.

### <a name="image-analysis-skill"></a>Vaardigheid van afbeeldings analyse

De [Kwalificatie analyse van installatie kopieën](cognitive-search-skill-image-analysis.md) extraheert een uitgebreide set visuele functies op basis van de inhoud van de installatie kopie. U kunt bijvoorbeeld een bijschrift genereren op basis van een afbeelding, tags genereren of beroemdheden en bezienswaardigheden identificeren.

### <a name="ocr-skill"></a>OCR-vaardigheid

De [OCR-vaardigheid](cognitive-search-skill-ocr.md) extraheert tekst uit afbeeldings bestanden zoals jpgs, PNGs en bitmaps. Het kan tekst ophalen en indelings informatie. De lay-outinformatie voorziet in omsluitende vakken voor elk van de geïdentificeerde teken reeksen.

## <a name="embedded-image-scenario"></a>Scenario voor Inge sloten afbeelding

Een veelvoorkomend scenario bestaat uit het maken van een enkele teken reeks met alle bestands inhoud, tekst-en afbeeldings oorsprong, door de volgende stappen uit te voeren:  

1. [Normalized_images extra heren](#get-normalized-images)
1. De OCR-vaardigheid uitvoeren `"/document/normalized_images"` met als invoer
1. De tekst weergave van deze afbeeldingen samen voegen met de onbewerkte tekst uit het bestand. U kunt de [tekst](cognitive-search-skill-textmerger.md) samenvoegings vaardigheid gebruiken om beide tekst segmenten samen te voegen tot één grote teken reeks.

In het volgende voor beeld wordt een *merged_text* -veld gemaakt met daarin de tekstuele inhoud van uw document. Het bevat ook de OCRed-tekst van elk van de Inge sloten afbeeldingen. 

#### <a name="request-body-syntax"></a>Syntaxis van aanvraag tekst
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

Nu u een merged_text-veld hebt, kunt u dit als Zoek bare veld in uw indexerings definitie toewijzen. Alle inhoud van uw bestanden, met inbegrip van de tekst van de afbeeldingen, kan doorzoekbaar zijn.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Begrenzings vakken van geëxtraheerde tekst visualiseren

Een ander algemeen scenario is het visualiseren van informatie over de indeling van zoek resultaten. U kunt bijvoorbeeld aangeven waar een stukje tekst is gevonden in een afbeelding als onderdeel van de zoek resultaten.

Omdat de OCR-stap op de genormaliseerde installatie kopieën wordt uitgevoerd, bevinden de lay-outcoördinaten zich in de genormaliseerde afbeeldings ruimte. Bij het weer geven van de genormaliseerde afbeelding is de aanwezigheid van coördinaten doorgaans geen probleem, maar in sommige gevallen wilt u mogelijk de oorspronkelijke afbeelding weer geven. In dit geval moet u elke coördinaat punt in de lay-out converteren naar het oorspronkelijke afbeeldings coördinaten systeem. 

Als helper moet u het volgende algoritme gebruiken als u genormaliseerde coördinaten wilt transformeren naar de oorspronkelijke coördinaten ruimte:

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
+ [Indexeer functie maken (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [Afbeeldings vaardigheid analyseren](cognitive-search-skill-image-analysis.md)
+ [OCR-vaardigheid](cognitive-search-skill-ocr.md)
+ [Tekst samenvoegings vaardigheid](cognitive-search-skill-textmerger.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Verrijkte velden toewijzen](cognitive-search-output-field-mapping.md)
