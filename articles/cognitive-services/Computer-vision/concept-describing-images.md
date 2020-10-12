---
title: Beschrijvingen van installatie kopieën-Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot de functie beschrijving van afbeelding van de Computer Vision-API.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80244712"
---
# <a name="describe-images-with-human-readable-language"></a>Afbeeldingen beschrijven met een Human-Lees bare taal

Computer Vision kunt een afbeelding analyseren en een door de mens lees bare zin genereren waarin de inhoud wordt beschreven. Het algoritme retourneert een aantal beschrijvingen op basis van verschillende visuele functies en elke beschrijving krijgt een betrouwbaarheids Score. De uiteindelijke uitvoer is een lijst met beschrijvingen die zijn besteld van hoogste naar laagste betrouw baarheid.

## <a name="image-description-example"></a>Voor beeld van installatie kopie beschrijving

In het volgende JSON-antwoord ziet u wat Computer Vision retourneert bij het beschrijven van de voorbeeld afbeelding op basis van de visuele functies.

![Een zwarte en witte afbeelding van gebouwen in Manhattan](./Images/bw_buildings.png)

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

De functie beschrijving van afbeelding maakt deel uit van de API voor het [analyseren van afbeeldingen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) . U kunt deze API aanroepen via een systeem eigen SDK of via REST-aanroepen. Neem `Description` in de query parameter **visualFeatures** op. Wanneer u vervolgens het volledige JSON-antwoord krijgt, parseert u de teken reeks voor de inhoud van de `"description"` sectie.

* [Snelstartgids: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Quick Start: een afbeelding analyseren (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verwante concepten voor het [labelen van afbeeldingen](concept-tagging-images.md) en het [categoriseren van afbeeldingen](concept-categorizing-images.md).
