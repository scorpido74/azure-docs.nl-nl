---
title: Detectie van kleurenschema's - Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot het detecteren van het kleurenschema in afbeeldingen met behulp van de Computer Vision API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: af0c39ed8211ac2041d143112437ad5d6b384259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244729"
---
# <a name="detect-color-schemes-in-images"></a>Kleurenschema's in afbeeldingen detecteren

Computer Vision analyseert de kleuren in een afbeelding om drie verschillende kenmerken te bieden: de dominante voorgrondkleur, de dominante achtergrondkleur en de set dominante kleuren voor de afbeelding als geheel. Geretourneerde kleuren behoren tot de set: zwart, blauw, bruin, grijs, groen, oranje, roze, paars, rood, groen, wit en geel. 

Computer Vision extraheert ook een accentkleur, die de meest levendige kleur in het beeld vertegenwoordigt, gebaseerd op een combinatie van dominante kleuren en verzadiging. De accentkleur wordt geretourneerd als een hexadecimale HTML-kleurcode. 

Computer Vision retourneert ook een booleaanse waarde die aangeeft of een afbeelding in zwart-wit is.

## <a name="color-scheme-detection-examples"></a>Voorbeelden van kleurschemadetectie

In het volgende voorbeeld wordt de JSON-respons weergegeven die door Computer Vision is geretourneerd bij het detecteren van het kleurenschema van de voorbeeldafbeelding. In dit geval is de voorbeeldafbeelding geen zwart-witafbeelding, maar de dominante voorgrond- en achtergrondkleuren zijn zwart en de dominante kleuren voor de afbeelding als geheel zijn zwart-wit.

![OpenluchtBerg bij zonsondergang, met het silhouet van een persoon](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Dominante kleurenvoorbeelden

In de volgende tabel worden de geretourneerde voorgrond-, achtergrond- en afbeeldingskleuren voor elke voorbeeldafbeelding weergegeven.

| Installatiekopie | Dominante kleuren |
|-------|-----------------|
|![Een witte bloem met een groene achtergrond](./Images/flower.png)| Voorgrond: Zwart<br/>Achtergrond: Wit<br/>Kleuren: Zwart, Wit, Groen|
![Een trein die door een post loopt](./Images/train_station.png) | Voorgrond: Zwart<br/>Achtergrond: Zwart<br/>Kleuren: Zwart |

### <a name="accent-color-examples"></a>Voorbeelden van accentkleuren

 In de volgende tabel wordt de geretourneerde accentkleur, als hexadecimale HTML-kleurwaarde, weergegeven voor elke voorbeeldafbeelding.

| Installatiekopie | Accentkleur |
|-------|--------------|
|![Een persoon die zich op een bergrots bij zonsondergang bevindt](./Images/mountain_vista.png) | #BB6D10 |
|![Een witte bloem met een groene achtergrond](./Images/flower.png) | #C6A205 |
|![Een trein die door een post loopt](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Voorbeelden van zwart & witdetectie

In de volgende tabel ziet u de zwart-witevaluatie van Computer Vision in de voorbeeldafbeeldingen.

| Installatiekopie | Zwart & wit? |
|-------|----------------|
|![Een zwart-wit beeld van gebouwen in Manhattan](./Images/bw_buildings.png) | waar |
|![Een blauw huis en de voortuin](./Images/house_yard.png) | false |

## <a name="use-the-api"></a>De API gebruiken

De functie detectie van het kleurenschema maakt deel uit van de API Voor het analyseren van [afbeelding.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) U deze API aanroepen via een native SDK of via REST-oproepen. Opnemen `Color` in de parameter **visualFeatures** query. Wanneer u vervolgens de volledige JSON-respons krijgt, ontleden `"color"` u gewoon de tekenreeks voor de inhoud van de sectie.

* [Snelstart: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snelstart: een afbeelding analyseren (REST API)](./quickstarts/csharp-analyze.md)
