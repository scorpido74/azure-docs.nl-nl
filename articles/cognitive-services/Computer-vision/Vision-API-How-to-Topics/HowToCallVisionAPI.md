---
title: De Computer Vision-API aanroepen
titleSuffix: Azure Cognitive Services
description: Meer informatie over het aanroepen van de Computer Vision API met behulp van de REST API in Azure Cognitive Services.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72177050"
---
# <a name="call-the-computer-vision-api"></a>De Computer Vision-API aanroepen

In dit artikel wordt uitgelegd hoe u de Computer Vision API aanroepen met behulp van de REST API. De voorbeelden worden zowel in C# geschreven met behulp van de Computer Vision API-clientbibliotheek als als HTTP POST- of GET-oproepen. Het artikel richt zich op:

- Tags, een beschrijving en categorieën downloaden
- Domeinspecifieke informatie krijgen, of 'beroemdheden'

## <a name="prerequisites"></a>Vereisten

- Een afbeeldings-URL of een pad naar een lokaal opgeslagen afbeelding
- Ondersteunde invoermethoden: een binaire raw-afbeelding in de vorm van een toepassing/octetstream of een afbeeldings-URL
- Ondersteunde afbeeldingsbestandsindelingen: JPEG, PNG, GIF en BMP
- Afbeeldingsbestandsgrootte: 4 MB of minder
- Afbeeldingsafmetingen: 50 &times; 50 pixels of meer
  
De voorbeelden in dit artikel tonen de volgende kenmerken aan:

* Een afbeelding analyseren om een array met tags en een beschrijving terug te geven
* Een afbeelding analyseren met een domeinspecifiek model (specifiek het "beroemdheden"-model) om het overeenkomstige resultaat in JSON terug te geven

De functies bieden de volgende opties:

- **Optie 1**: Scoped Analysis - Alleen een opgegeven model analyseren
- **Optie 2**: Verbeterde analyse - Analyseren om aanvullende details te verstrekken met behulp van [86-categorieën taxonomie](../Category-Taxonomy.md)
  
## <a name="authorize-the-api-call"></a>De API-aanroep autoriseren

Voor elke aanroep naar de Computer Vision-API is een abonnementssleutel vereist. Deze sleutel moet worden doorgegeven via een parameter querytekenreeks of opgegeven in de hoofdtekst van de aanvraag.

Ga als volgt te werk om een gratis proefsleutel te krijgen:
* Ga naar de pagina [Cognitieve services uitproberen.](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) 
* Ga naar de accountpagina [Een cognitive services maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) om u te abonneren op Computer Vision.

U de abonnementssleutel doorgeven door een van de volgende handelingen uit te voeren:

* Geef het door een querytekenreeks, zoals in dit voorbeeld van de Computer Vision API:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* Geef het op in de koptekst van de HTTP-aanvraag:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* Wanneer u de clientbibliotheek gebruikt, geeft u de sleutel door via de constructor van ComputerVisionClient en geeft u de regio op in een eigenschap van de klant:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Een afbeelding uploaden naar de API-service Computer Vision

De basismanier om de Computer Vision API-aanroep uit te voeren, is door een afbeelding rechtstreeks te uploaden om tags, een beschrijving en beroemdheden terug te sturen. U doet dit door het verzenden van een "POST" verzoek met de binaire afbeelding in de HTTP-body samen met de gegevens gelezen uit de afbeelding. De uploadmethode is hetzelfde voor alle Computer Vision API-aanroepen. Het enige verschil is de queryparameters die u opgeeft. 

Download tags en een beschrijving voor een bepaalde afbeelding met behulp van een van de volgende opties:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>Optie 1: Een lijst met tags en een beschrijving downloaden

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

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>Optie 2: Alleen een lijst met tags of een beschrijving

Voer alleen voor tags uit:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

Voer voor een beschrijving het als gevolg:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Domeinspecifieke analyse (beroemdheden)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Optie 1: Scoped-analyse - Alleen een opgegeven model analyseren
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

Voor deze optie zijn alle andere queryparameters {visualFeatures, details} niet geldig. Als u alle ondersteunde modellen wilt zien, gebruikt u:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>Optie 2: Verbeterde analyse - Analyseren om aanvullende details te geven met behulp van 86-categorieën taxonomie

Voor toepassingen waarbij u naast details van een of meer domeinspecifieke modellen een algemene beeldanalyse wilt ontvangen, breidt u de v1-API uit met behulp van de queryparameter models.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

Wanneer u deze methode aanroept, belt u eerst de classificatie [van 86 categorieën.](../Category-Taxonomy.md) Als een van de categorieën overeenkomt met die van een bekend of overeenkomend model, vindt er een tweede pas van classifier aanroepen plaats. Als 'details=alles' of 'details' bijvoorbeeld 'beroemdheden' bevatten, belt u het beroemdhedenmodel nadat u de classificatie van 86 categorieën hebt gebeld. Het resultaat omvat de categoriepersoon. In tegenstelling tot optie 1 verhoogt deze methode de latentie voor gebruikers die geïnteresseerd zijn in beroemdheden.

In dit geval gedragen alle v1-queryparameters zich op dezelfde manier. Als u visualFeatures=-categorieën niet opgeeft, is deze impliciet ingeschakeld.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>De JSON-uitvoer ophalen en begrijpen voor analyse

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
Tags  | `object` | Het object op het hoogste niveau voor een reeks tags.
tags[].Name | `string`  | Het trefwoord van de tags classifier.
tags[].Score    | `number`  | De betrouwbaarheidsscore, tussen 0 en 1.
description  | `object` | Het object op het hoogste niveau voor een beschrijving.
description.tags[] |    `string`    | De lijst met tags.  Als er onvoldoende vertrouwen is in de mogelijkheid om een bijschrift te produceren, zijn de tags mogelijk de enige informatie die beschikbaar is voor de beller.
description.captions[].text | `string`  | Een zin die de afbeelding beschrijft.
description.captions[].confidence   | `number`  | De betrouwbaarheidsscore voor de zin.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>De JSON-uitvoer van domeinspecifieke modellen ophalen en begrijpen

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Optie 1: Scoped-analyse - Alleen een opgegeven model analyseren

De uitvoer is een array met tags, zoals in het volgende voorbeeld wordt weergegeven:

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

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>Optie 2: Verbeterde analyse - Analyseren om aanvullende details te geven met behulp van de "86-categorieën" taxonomie

Voor domeinspecifieke modellen met optie 2 (verbeterde analyse) wordt het retourtype van de categorieën uitgebreid, zoals in het volgende voorbeeld wordt weergegeven:

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

Het veld categorieën is een lijst van een of meer van de [86 categorieën](../Category-Taxonomy.md) in de oorspronkelijke taxonomie. Categorieën die eindigen in een underscore komen overeen met die categorie en de kinderen (bijvoorbeeld 'people_' of 'people_group', voor het beroemdhedenmodel).

Veld   | Type  | Inhoud
------|------|------|
categorieën | `object`   | Het object op het hoogste niveau.
categories[].name    | `string` | De naam van de taxonomielijst met 86 categorieën.
categories[].score  | `number`  | De betrouwbaarheidsscore, tussen 0 en 1.
categories[].detail  | `object?`      | (Optioneel) Het detailobject.

Als meerdere categorieën overeenkomen (bijvoorbeeld de classificatie van 86 categorieën geeft een score voor zowel people_" als 'people_young', wanneer model=beroemdheden) worden geretourneerd, worden de details gekoppeld aan de meest algemene niveauovereenkomst ('people_', in dat voorbeeld).

## <a name="error-responses"></a>Foutreacties

Deze fouten zijn identiek aan die in vision.analyze, met de extra NotSupportedModel-fout (HTTP 400), die kan worden geretourneerd in zowel de optie 1- als optie 2-scenario's. Voor optie 2 (verbeterde analyse) wordt de API een NotSupportedModel geretourneerd als een van de modellen die in de details zijn opgegeven, niet wordt herkend. Om erachter te komen welke modellen worden ondersteund, u listModels bellen.

## <a name="next-steps"></a>Volgende stappen

Als u de REST-API wilt gebruiken, gaat u naar [Computer Vision-API-referentie](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
