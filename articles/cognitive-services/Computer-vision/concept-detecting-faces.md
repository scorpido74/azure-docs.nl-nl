---
title: Gezichtsherkenning - Computer Vision
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot de functie voor gezichtsherkenning van de Computer Vision API.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244697"
---
# <a name="face-detection-with-computer-vision"></a>Gezichtsherkenning met Computer Vision

Computer Vision kan menselijke gezichten in een afbeelding detecteren en de leeftijd, het geslacht en de rechthoek voor elk gedetecteerd gezicht genereren. 

> [!NOTE]
> Deze functie wordt ook aangeboden door de Azure [Face-service.](/azure/cognitive-services/face/) Zie dit alternatief voor meer gedetailleerde gezichtsanalyse, inclusief gezichtsidentificatie en posedetectie. 

## <a name="face-detection-examples"></a>Voorbeelden van gezichtsherkenning

In het volgende voorbeeld wordt de JSON-reactie weergegeven die door Computer Vision is geretourneerd voor een afbeelding die één menselijk gezicht bevat.

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

In het volgende voorbeeld wordt de JSON-reactie weergegeven die is geretourneerd voor een afbeelding met meerdere menselijke gezichten.

![Vision Analyseer familie foto gezicht](./Images/family_photo_face.png)

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

De functie voor gezichtsherkenning maakt deel uit van de Api Voor het analyseren van [afbeeldingen.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) U deze API aanroepen via een native SDK of via REST-oproepen. Opnemen `Faces` in de parameter **visualFeatures** query. Wanneer u vervolgens de volledige JSON-respons krijgt, ontleden `"faces"` u gewoon de tekenreeks voor de inhoud van de sectie.

* [Snelstart: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snelstart: een afbeelding analyseren (REST API)](./quickstarts/csharp-analyze.md)
