---
title: Afbeeldingsbeschrijvingen - Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot de functie voor beeldbeschrijving van de Computer Vision API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7bf95a2b49608ef1f031a3b443db92b42cdae624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244712"
---
# <a name="describe-images-with-human-readable-language"></a>Afbeeldingen beschrijven met door de mens leesbare taal

Computer Vision kan een afbeelding analyseren en een door de mens leesbare zin genereren die de inhoud ervan beschrijft. Het algoritme retourneert eigenlijk verschillende beschrijvingen op basis van verschillende visuele functies, en elke beschrijving krijgt een betrouwbaarheidsscore. De uiteindelijke uitvoer is een lijst met beschrijvingen die zijn geordend van het hoogste tot laagste vertrouwen.

## <a name="image-description-example"></a>Voorbeeld van afbeeldingsbeschrijving

De volgende JSON-respons illustreert wat Computer Vision retourneert bij het beschrijven van de voorbeeldafbeelding op basis van de visuele kenmerken.

![Een zwart-wit beeld van gebouwen in Manhattan](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>De API gebruiken

De functie voor afbeeldingsbeschrijving maakt deel uit van de [API Voor afbeelding analyseren.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) U deze API aanroepen via een native SDK of via REST-oproepen. Opnemen `Description` in de parameter **visualFeatures** query. Wanneer u vervolgens de volledige JSON-respons krijgt, ontleden `"description"` u gewoon de tekenreeks voor de inhoud van de sectie.

* [Snelstart: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snelstart: een afbeelding analyseren (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Volgende stappen

Leer de bijbehorende concepten van [het taggen](concept-tagging-images.md) van afbeeldingen en [het categoriseren van afbeeldingen.](concept-categorizing-images.md)
