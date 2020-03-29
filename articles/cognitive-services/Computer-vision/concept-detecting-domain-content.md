---
title: Domeinspecifieke inhoud - Computer Vision
titleSuffix: Azure Cognitive Services
description: Meer informatie over het opgeven van een afbeeldingscategorisatiedomein om meer gedetailleerde informatie over een afbeelding te retourneren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8d6dc91ae7bb0f6d7a24064749d9295558a7d39c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68946327"
---
# <a name="detect-domain-specific-content"></a>Domeinspecifieke inhoud detecteren

Naast tagging en categorisering op hoog niveau ondersteunt Computer Vision ook verdere domeinspecifieke analyse met behulp van modellen die zijn getraind op gespecialiseerde gegevens.

Er zijn twee manieren om de domeinspecifieke modellen te gebruiken: op zichzelf (scoped analysis) of als een verbetering van de categorisatiefunctie.

### <a name="scoped-analysis"></a>Scoped-analyse

U een afbeelding analyseren met alleen het gekozen domeinspecifieke model door de [API\<Modellen/model\>/Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) aan te roepen.

Het volgende is een voorbeeld JSON reactie geretourneerd door de **modellen / beroemdheden / api analyseren** voor de gegeven afbeelding:

![Satya Nadella die zich bevindt, het glimlachen](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>Verbeterde categorisatieanalyse

U ook domeinspecifieke modellen gebruiken om algemene beeldanalyse aan te vullen. U doet dit als onderdeel van indeling op hoog niveau door domeinspecifieke modellen op te geven in de *parameter details* van de [API-aanroep](concept-categorizing-images.md) [analyseren.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)

In dit geval wordt de 86-categorie taxonomie classifier als eerste genoemd. Als een van de gedetecteerde categorieën een overeenkomend domeinspecifiek model heeft, wordt de afbeelding ook door dat model geleid en worden de resultaten toegevoegd.

De volgende JSON-respons laat zien hoe domeinspecifieke analyse als knooppunt `detail` kan worden opgenomen in een bredere categoriseringsanalyse.

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>De domeinspecifieke modellen weergeven

Momenteel ondersteunt Computer Vision de volgende domeinspecifieke modellen:

| Name | Beschrijving |
|------|-------------|
| Beroemdheden | Celebrity erkenning, ondersteund voor beelden `people_` ingedeeld in de categorie |
| Bezienswaardigheden | Herkenning van oriëntatiepunten, ondersteund `outdoor_` voor `building_` afbeeldingen die zijn ingedeeld in de of categorieën |

Als u de [API voor modellen](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) aanroept, worden deze gegevens teruggegeven samen met de categorieën waarop elk model van toepassing kan zijn:

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het categoriseren van afbeeldingen](concept-categorizing-images.md).
