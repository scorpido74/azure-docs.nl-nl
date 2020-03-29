---
title: Objectdetectie - Computer Vision
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot de functie objectdetectie van de Computer Vision API - gebruik en limieten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3957e15a09bd7e7ecd814d169451af3241108b64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131660"
---
# <a name="detect-common-objects-in-images"></a>Veelvoorkomende objecten in afbeeldingen detecteren

Objectdetectie is vergelijkbaar met [tags,](concept-tagging-images.md)maar de API retourneert de selectiecoördinaten (in pixels) voor elk gevonden object. Als een afbeelding bijvoorbeeld een hond, kat en persoon bevat, zal de detectiebewerking deze objecten vermelden, samen met hun coördinaten in de afbeelding. U deze functionaliteit gebruiken om de relaties tussen de objecten in een afbeelding te verwerken. U ook bepalen of er meerdere exemplaren van dezelfde tag in een afbeelding zijn.

De DETECT API past tags toe op basis van de objecten of levende dingen die in de afbeelding zijn geïdentificeerd. Er is momenteel geen formele relatie tussen de tagging taxonomie en de object detectie taxonomie. Op conceptueel niveau vindt de DETECT API alleen objecten en levende wezens, terwijl de Tag API ook contextuele termen kan bevatten zoals 'binnen', die niet kunnen worden gelokaliseerd met omsluitende vakken.

## <a name="object-detection-example"></a>Voorbeeld van objectdetectie

De volgende JSON-reactie illustreert wat Computer Vision retourneert bij het detecteren van objecten in de voorbeeldafbeelding.

![Een vrouw die een apparaat van de Oppervlakte van Microsoft in een keuken gebruikt](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>Beperkingen

Het is belangrijk om de beperkingen van objectdetectie op te merken, zodat u de effecten van valse negatieven (gemiste objecten) en beperkte details vermijden of beperken.

* Objecten worden over het algemeen niet gedetecteerd als ze klein zijn (minder dan 5% van de afbeelding).
* Objecten worden over het algemeen niet gedetecteerd als ze dicht bij elkaar zijn gerangschikt (bijvoorbeeld een stapel platen).
* Objecten worden niet onderscheiden door merk- of productnamen (verschillende soorten frisdranken op een winkelplank bijvoorbeeld). U echter merkinformatie uit een afbeelding halen met behulp van de [functie Merkdetectie.](concept-brand-detection.md)

## <a name="use-the-api"></a>De API gebruiken

De functie objectdetectie maakt deel uit van de [API Voor afbeelding analyseren.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) U deze API aanroepen via een native SDK of via REST-oproepen. Opnemen `Objects` in de parameter **visualFeatures** query. Wanneer u vervolgens de volledige JSON-respons krijgt, ontleden `"objects"` u gewoon de tekenreeks voor de inhoud van de sectie.

* [Snelstart: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snelstart: een afbeelding analyseren (REST API)](./quickstarts/csharp-analyze.md)