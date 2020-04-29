---
title: Gezichts detectie-Computer Vision
titleSuffix: Azure Cognitive Services
description: Leer concepten die betrekking hebben op de functie gezichts detectie van de Computer Vision-API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d10e4c98d3e2f00e7139063c8263e65ffd015d7c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80244697"
---
# <a name="face-detection-with-computer-vision"></a>Gezichts detectie met Computer Vision

Computer Vision kunt menselijke gezichten detecteren binnen een afbeelding en de leeftijd, het geslacht en de rechthoek voor elk gedetecteerd gezicht genereren. 

> [!NOTE]
> Deze functie wordt ook aangeboden door de Azure [Face](/azure/cognitive-services/face/) -service. Bekijk dit alternatief voor gedetailleerde analyse van het gezicht, inclusief gezichts identificatie en het detecteren van detectie. 

## <a name="face-detection-examples"></a>Voor beelden van gezichts detectie

In het volgende voor beeld wordt het JSON-antwoord gedemonstreerd dat wordt geretourneerd door Computer Vision voor een afbeelding met één menselijk gezicht.

![Vision-analyse Vrouw op dak-gezicht](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

In het volgende voor beeld wordt het JSON-antwoord gedemonstreerd dat is geretourneerd voor een afbeelding met meerdere menselijke gezichten.

![Vision Analyseer Family foto face](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="use-the-api"></a>De API gebruiken

De functie voor gezichts detectie maakt deel uit van de API voor het [analyseren van afbeeldingen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) . U kunt deze API aanroepen via een systeem eigen SDK of via REST-aanroepen. Neem `Faces` in de query parameter **visualFeatures** op. Wanneer u vervolgens het volledige JSON-antwoord krijgt, parseert u de teken reeks voor de inhoud `"faces"` van de sectie.

* [Snelstartgids: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Quick Start: een afbeelding analyseren (REST API)](./quickstarts/csharp-analyze.md)
