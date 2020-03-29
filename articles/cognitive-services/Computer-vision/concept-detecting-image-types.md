---
title: Detectie van afbeeldingstype - Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot de functie detectie van het beeldtype van de Computer Vision API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4e6c2db5333962d7ae43534998ffc1c48b0dba45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244559"
---
# <a name="detecting-image-types-with-computer-vision"></a>Afbeeldingstypen detecteren met Computer Vision

Met de [ANALYSE Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API kan Computer Vision het inhoudstype afbeeldingen analyseren, waarbij wordt aangegeven of een afbeelding illustraties of een lijntekening is.

## <a name="detecting-clip-art"></a>Illustraties detecteren

Computer Vision analyseert een afbeelding en beoordeelt de kans dat de afbeelding wordt illustraties op een schaal van 0 tot 3, zoals beschreven in de volgende tabel.

| Waarde | Betekenis |
|-------|---------|
| 0 | Non-clip-art (geen illustratie) |
| 1 | Dubbelzinnig |
| 2 | Normale illustraties |
| 3 | Goed-clip-art |

### <a name="clip-art-detection-examples"></a>Voorbeelden van illustratiesdetectie

De volgende JSON-antwoorden laten zien wat Computer Vision retourneert bij het beoordelen van de waarschijnlijkheid dat de voorbeeldafbeeldingen illustraties worden.

![Een afbeelding van een illustratieafbeelding van een kaasschijfje](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Een blauw huis en de voortuin](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Lijntekeningen detecteren

Computer Vision analyseert een afbeelding en retourneert een booleaanse waarde die aangeeft of de afbeelding een lijntekening is.

### <a name="line-drawing-detection-examples"></a>Voorbeelden van detectie van lijntekening

De volgende JSON-antwoorden laten zien wat Computer Vision retourneert wanneer wordt aangegeven of de voorbeeldafbeeldingen lijntekeningen zijn.

![Een lijntekeningsbeeld van een leeuw](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Een witte bloem met een groene achtergrond](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>De API gebruiken

De functie detectie van het beeldtype maakt deel uit van de [API Voor afbeelding analyseren.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) U deze API aanroepen via een native SDK of via REST-oproepen. Opnemen `ImageType` in de parameter **visualFeatures** query. Wanneer u vervolgens de volledige JSON-respons krijgt, ontleden `"imageType"` u gewoon de tekenreeks voor de inhoud van de sectie.

* [Snelstart: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snelstart: een afbeelding analyseren (REST API)](./quickstarts/csharp-analyze.md)
