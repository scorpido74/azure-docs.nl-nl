---
title: Type samengestelde entiteit-LUIS
titleSuffix: Azure Cognitive Services
description: Een samengestelde entiteit bestaat uit andere entiteiten, zoals vooraf gebouwde entiteiten, eenvoudige, reguliere expressies en lijst entiteiten. De afzonderlijke entiteiten vormen een hele entiteit.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695157"
---
# <a name="composite-entity"></a>Samengestelde entiteit 

Een samengestelde entiteit bestaat uit andere entiteiten, zoals vooraf gebouwde entiteiten, eenvoudige, reguliere expressies en lijst entiteiten. De afzonderlijke entiteiten vormen een hele entiteit. 

**Deze entiteit is geschikt voor de volgende gegevens:**

* Aan elkaar zijn gerelateerd. 
* Ze zijn aan elkaar gerelateerd in de context van de utterance.
* Verschillende Entiteitstypen gebruiken.
* Moeten worden gegroepeerd en verwerkt door de client toepassing als een gegevens eenheid.
* Hebben een aantal uitingen voor gebruikers die machine learning nodig hebben.

![Samengestelde entiteit](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Voorbeeld van JSON

Overweeg een samengestelde entiteit van vooraf gemaakte `number` en `Location::ToLocation` met de volgende utterance:

`book 2 tickets to cairo`

U ziet dat `2`, het aantal en `cairo`, de ToLocation woorden tussen deze die geen deel uitmaken van een van de entiteiten hebben. De groene lijn, gebruikt in een gelabelde utterance in de [LUIS](luis-reference-regions.md) -website geeft aan dat een samengestelde entiteit.

![Samengestelde entiteit](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

Samengestelde entiteiten worden geretourneerd in een `compositeEntities` matrix en alle entiteiten in de samengestelde worden ook weergegeven in de `entities` matrix:

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```    

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)

Dit is de JSON als `verbose=false` is ingesteld in de query reeks:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

Dit is de JSON als `verbose=true` is ingesteld in de query reeks:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 


|Data-object|De naam van de entiteit|Waarde|
|--|--|--|
|Vooraf gedefinieerde entiteit - nummer|"builtin.number"|"2"|
|Preconstrueerde entiteit-GeographyV2|"Location::ToLocation"|Cairo|

## <a name="next-steps"></a>Volgende stappen

In deze [zelf studie](luis-tutorial-composite-entity.md)voegt u een **samengestelde entiteit** toe om geëxtraheerde gegevens van verschillende typen te bundelen in één container met een entiteit. De clienttoepassing kan door de gegevens bundeling, gerelateerde gegevens in verschillende gegevenstypen eenvoudig extraheren.
