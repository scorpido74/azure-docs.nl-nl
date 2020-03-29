---
title: Merkdetectie - Computer Vision
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt een gespecialiseerde modus voor objectdetectie besproken; merk- en/of logodetectie met behulp van de Computer Vision API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 50e4fe1e2573c8566bbdf5697bb81b025a00935c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131739"
---
# <a name="detect-popular-brands-in-images"></a>Populaire merken in afbeeldingen detecteren

Merkdetectie is een gespecialiseerde modus voor [objectdetectie](concept-object-detection.md) die een database van duizenden wereldwijde logo's gebruikt om commerciële merken in afbeeldingen of video's te identificeren. U kunt deze functie bijvoorbeeld gebruiken om te ontdekken welke merken het populairst zijn op sociale media of het meest voorkomen in productplaatsing in de media.

De Computer Vision-service detecteert of er merklogo's in een bepaalde afbeelding zitten; als dat het zo is, geeft het de merknaam, een betrouwbaarheidsscore en de coördinaten van een selectiekader rond het logo terug.

De ingebouwde logodatabase omvat populaire merken in consumentenelektronica, kleding en meer. Als u merkt dat het merk dat u zoekt niet wordt gedetecteerd door de Computer Vision-service, u beter worden bediend met het maken en trainen van uw eigen logodetector met behulp van de [Custom Vision-service.](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)

## <a name="brand-detection-example"></a>Voorbeeld van merkdetectie

De volgende JSON-antwoorden illustreren wat Computer Vision retourneert bij het detecteren van merken in de voorbeeldafbeeldingen.

![Een rood overhemd met een etiket van Microsoft en embleem op het](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

In sommige gevallen zal de merkdetector zowel het logobeeld als de gestileerde merknaam als twee afzonderlijke logo's oppikken.

![Een grijs sweatshirt met een Microsoft-label en logo erop](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>De API gebruiken

De functie merkdetectie maakt deel uit van de [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API. U deze API aanroepen via een native SDK of via REST-oproepen. Opnemen `Brands` in de parameter **visualFeatures** query. Wanneer u vervolgens de volledige JSON-respons krijgt, ontleden `"brands"` u gewoon de tekenreeks voor de inhoud van de sectie.

* [Snelstart: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snelstart: een afbeelding analyseren (REST API)](./quickstarts/csharp-analyze.md)
