---
title: Detectie van installatie kopie type-Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot de functie voor het detecteren van het afbeeldings type van de Computer Vision-API.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80244559"
---
# <a name="detecting-image-types-with-computer-vision"></a>Afbeeldings typen detecteren met Computer Vision

Met de API voor het [analyseren](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) van afbeeldingen kunt Computer Vision het inhouds type van installatie kopieën analyseren om aan te geven of een afbeelding een illustratie of een lijn tekening is.

## <a name="detecting-clip-art"></a>Illustraties detecteren

Computer Vision analyseert een afbeelding en verkleint de kans dat de afbeelding wordt geillustratied op een schaal van 0 tot 3, zoals wordt beschreven in de volgende tabel.

| Waarde | Betekenis |
|-------|---------|
| 0 | Non-clip-art (geen illustratie) |
| 1 | Dubbel |
| 2 | Normaal-clip-art |
| 3 | Goede clip art |

### <a name="clip-art-detection-examples"></a>Voor beelden van clip art-detectie

In de volgende JSON-antwoorden ziet u wat Computer Vision als resultaat geeft wanneer u de kans op de voorbeeld afbeeldingen die illustraties worden, wilt beoordelen.

![Een illustratie afbeelding van een segment van kaas](./Images/cheese_clipart.png)

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

![Een blauw huis en de voor tuin](./Images/house_yard.png)

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

## <a name="detecting-line-drawings"></a>Lijn tekeningen detecteren

Computer Vision analyseert een afbeelding en retourneert een Booleaanse waarde die aangeeft of de afbeelding een lijn tekening is.

### <a name="line-drawing-detection-examples"></a>Voor beelden van regel tekening detectie

In de volgende JSON-antwoorden ziet u wat Computer Vision retourneert wanneer wordt aangegeven of de voorbeeld afbeeldingen lijn tekeningen zijn.

![Een lijn teken afbeelding van een Lion](./Images/lion_drawing.png)

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

De functie detectie van het afbeeldings type maakt deel uit van de API voor het [analyseren van afbeeldingen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) . U kunt deze API aanroepen via een systeem eigen SDK of via REST-aanroepen. Neem `ImageType` in de query parameter **visualFeatures** op. Wanneer u vervolgens het volledige JSON-antwoord krijgt, parseert u de teken reeks voor de inhoud `"imageType"` van de sectie.

* [Snelstartgids: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Quick Start: een afbeelding analyseren (REST API)](./quickstarts/csharp-analyze.md)
