---
title: Inhoudstags - Computer Vision
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot de functie voor het taggen van afbeeldingen van de Computer Vision API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 887f6d9163f800ecbe8a5ab9a0715fd171f1d3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244508"
---
# <a name="applying-content-tags-to-images"></a>Inhoudstags toepassen op afbeeldingen

Computer Vision retourneert tags op basis van duizenden herkenbare objecten, levende wezens, landschappen en acties. Wanneer tags ambigu of niet algemeen bekend zijn, geeft de API-reactie tips om de betekenis van de tag in de context van een bekende situatie te verduidelijken. Tags zijn niet ingedeeld als taxonomie en er bestaan geen overnamehiërarchieën. Een verzameling inhoudstags vormt de basis voor een 'beschrijving van de afbeelding, weergegeven als voor mensen leesbare taal opgemaakt in volledige zinnen. Houd er rekening mee dat Engels op dit moment de enige ondersteunde taal is voor de beschrijving van afbeeldingen.

Na het uploaden van een afbeelding of het specificeren van een afbeeldings-URL, leveren de algoritmen van Computer Vision als output tags die zijn gebaseerd op objecten, levende wezens en acties die in de afbeelding zijn herkend. U kunt tagging niet alleen gebruiken voor het hoofdonderwerp, zoals een persoon op de voorgrond, maar ook voor de omgeving (binnen of buiten), meubels, gereedschap, planten, dieren, accessoires, gadgets enzovoort.

## <a name="image-tagging-example"></a>Voorbeeld van het taggen van afbeeldingen

De volgende JSON-respons illustreert wat Computer Vision retourneert bij het taggen van visuele functies die in de voorbeeldafbeelding zijn gedetecteerd.

![Een blauw huis en de voortuin](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>De API gebruiken

De tagfunctie maakt deel uit van de API Voor het analyseren van [afbeelding.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) U deze API aanroepen via een native SDK of via REST-oproepen. Opnemen `Tags` in de parameter **visualFeatures** query. Wanneer u vervolgens de volledige JSON-respons krijgt, ontleden `"tags"` u gewoon de tekenreeks voor de inhoud van de sectie.

* [Snelstart: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snelstart: een afbeelding analyseren (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Volgende stappen

Leer de bijbehorende concepten van [het categoriseren](concept-categorizing-images.md) van afbeeldingen en [het beschrijven van afbeeldingen.](concept-describing-images.md)
