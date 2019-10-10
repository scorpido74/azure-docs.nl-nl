---
title: De Computer Vision-API aanroepen
titleSuffix: Azure Cognitive Services
description: Meer informatie over het aanroepen van de Computer Vision-API met behulp van de REST API in azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 298228eedb73298f00654f4f72c201d9ed671090
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177050"
---
# <a name="call-the-computer-vision-api"></a>De Computer Vision-API aanroepen

In dit artikel wordt beschreven hoe u de Computer Vision-API aanroept met behulp van de REST API. De voor beelden worden geschreven in C# met behulp van de computer vision-API-client bibliotheek en als http post of Get-aanroepen. Het artikel is gericht op:

- Tags, een beschrijving en categorieën ophalen
- Domein-specifieke informatie of ' beroemdheden ' ophalen

## <a name="prerequisites"></a>Vereisten

- Een afbeeldings-URL of een pad naar een lokaal opgeslagen afbeelding
- Ondersteunde invoer methoden: een onbewerkte binaire afbeelding in de vorm van een toepassing/octet-stream of een afbeeldings-URL
- Ondersteunde indelingen voor afbeeldings bestanden: JPEG, PNG, GIF en BMP
- Afbeeldings bestands grootte: 4 MB of minder
- Afmetingen afbeelding: 50 &times; 50 pixels of meer
  
In de voor beelden in dit artikel worden de volgende functies gedemonstreerd:

* Een afbeelding analyseren om een matrix met tags en een beschrijving te retour neren
* Het analyseren van een installatie kopie met een specifiek domein model (met name het ' beroemdheden-model) om het overeenkomstige resultaat in JSON te retour neren

De functies bieden de volgende opties:

- **Optie 1**: bereik analyse-alleen een opgegeven model analyseren
- **Optie 2**: verbeterde analyse-analyseren om aanvullende details te bieden met behulp van een [86-categorie taxonomie](../Category-Taxonomy.md)
  
## <a name="authorize-the-api-call"></a>De API-aanroep autoriseren

Voor elke aanroep naar de Computer Vision-API is een abonnementssleutel vereist. Deze sleutel moet worden door gegeven via een query reeks parameter of worden opgegeven in de aanvraag header.

Ga op een van de volgende manieren te werk om een gratis proef versie te verkrijgen:
* Ga naar de pagina [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) . 
* Ga naar de pagina [een Cognitive Services-account maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op computer vision.

U kunt de abonnements sleutel door geven door een van de volgende handelingen uit te voeren:

* Geef het door via een query reeks, zoals in dit Computer Vision-API voor beeld:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* Geef deze op in de header van de HTTP-aanvraag:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* Wanneer u de-client bibliotheek gebruikt, geeft u de sleutel door via de constructor van ComputerVisionClient en geeft u de regio op in een eigenschap van de client:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Een installatie kopie uploaden naar de Computer Vision-API-service

De basis manier om de Computer Vision-API-aanroep uit te voeren, is door een afbeelding rechtstreeks te uploaden naar de labels, een beschrijving en beroemdheden. U doet dit door een ' POST '-aanvraag te verzenden met de binaire installatie kopie in de HTTP-bericht, samen met de gegevens die uit de installatie kopie zijn gelezen. De upload methode is hetzelfde voor alle Computer Vision-API-aanroepen. Het enige verschil is de query parameters die u opgeeft. 

Voor een opgegeven installatie kopie, tags en een beschrijving ophalen met behulp van een van de volgende opties:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>Optie 1: een lijst met tags en een beschrijving ophalen

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>Optie 2: alleen een lijst met tags of een beschrijving ophalen

Voor alleen Tags voert u de volgende handelingen uit:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

Voer voor een beschrijving alleen een van de volgende handelingen uit:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Domein-specifieke analyse ophalen (beroemdheden)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Optie 1: bereik analyse-alleen een opgegeven model analyseren
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

Voor deze optie zijn alle andere queryparameters {visualFeatures, details} niet geldig. Als u alle ondersteunde modellen wilt zien, gebruikt u:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>Optie 2: verbeterde analyse-analyseren om aanvullende details te bieden met behulp van een 86-categorie taxonomie

Voor toepassingen waarvoor u een generieke installatie kopie wilt maken naast Details van een of meer domein-specifieke modellen, breidt u de V1 API uit met behulp van de query parameter model.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

Wanneer u deze methode aanroept, roept u eerst de classificatie van de [86-categorie](../Category-Taxonomy.md) aan. Als een van de categorieën overeenkomt met die van een bekend of overeenkomend model, treedt er een tweede doorgifte van classificatie aanroepen op. Als "Details = alle" of "Details" bijvoorbeeld "beroemdheden" bevat, roept u het beroemdheden-model aan nadat u de classificatie van de 86-categorie hebt aangeroepen. Het resultaat is inclusief de categorie persoon. In tegens telling tot optie 1 verhoogt deze methode de latentie voor gebruikers die geïnteresseerd zijn in beroemdheden.

In dit geval werken alle v1-query parameters op dezelfde manier. Als u geen visualFeatures = categorieën opgeeft, wordt deze impliciet ingeschakeld.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>De JSON-uitvoer voor analyse ophalen en begrijpen

Hier volgt een voorbeeld:

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Veld | Type | Inhoud
------|------|------|
Tags  | `object` | Het object op het hoogste niveau voor een matrix met tags.
tags[].Name | `string`  | Het sleutel woord van de classificatie Tags.
tags[].Score    | `number`  | De betrouwbaarheids score tussen 0 en 1.
description  | `object` | Het object op het hoogste niveau voor een beschrijving.
description.tags[] |    `string`    | De lijst met tags.  Als er onvoldoende vertrouwen is in de mogelijkheid om een bijschrift te maken, zijn de tags mogelijk de enige informatie die beschikbaar is voor de oproepende functie.
description.captions[].text | `string`  | Een zin die de afbeelding beschrijft.
description.captions[].confidence   | `number`  | De betrouwbaarheids score voor de woord groep.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>De JSON-uitvoer van domein-specifieke modellen ophalen en begrijpen

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Optie 1: bereik analyse-alleen een opgegeven model analyseren

De uitvoer is een matrix met tags, zoals wordt weer gegeven in het volgende voor beeld:

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>Optie 2: verbeterde analyse-analyseren om aanvullende details te bieden met behulp van de ' 86-categorieën ' taxonomie

Voor domein-specifieke modellen met behulp van optie 2 (verbeterde analyse) is het retour type van de categorie uitgebreid, zoals wordt weer gegeven in het volgende voor beeld:

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

Het veld Categorieën bevat een lijst met een of meer van de [86-categorieën](../Category-Taxonomy.md) in de oorspronkelijke taxonomie. Categorieën die eindigen op een onderstrepings teken, komen overeen met die categorie en de onderliggende items (bijvoorbeeld ' people_ ' of ' people_group ' voor het beroemdheden-model).

Veld   | Type  | Inhoud
------|------|------|
categorieën | `object`   | Het object op het hoogste niveau.
categories[].name    | `string` | De naam in de taxonomie lijst van de 86-categorie.
categories[].score  | `number`  | De betrouwbaarheids score tussen 0 en 1.
categories[].detail  | `object?`      | Beschrijving Het detail-object.

Als er meerdere categorieën overeenkomen (bijvoorbeeld: de classificatie van de 86-categorie retourneert een score voor zowel ' people_ ' als ' people_young ' wanneer model = beroemdheden), worden de details bijgevoegd bij de meest algemene level overeenkomst ("people_" in dat voor beeld).

## <a name="error-responses"></a>Fout berichten

Deze fouten zijn identiek aan die van Vision. Analyseer, met de extra NotSupportedModel-fout (HTTP 400), die mogelijk wordt geretourneerd in de scenario's van de optie 1 en optie 2. Voor optie 2 (verbeterde analyse), als een van de modellen die in de details zijn opgegeven, niet wordt herkend, retourneert de API een NotSupportedModel, zelfs als een of meer van beide zijn geldig. Als u wilt weten welke modellen worden ondersteund, kunt u listModels aanroepen.

## <a name="next-steps"></a>Volgende stappen

Als u de REST-API wilt gebruiken, gaat u naar [Computer Vision-API-referentie](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
