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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "71695157"
---
# <a name="composite-entity"></a>Samengestelde entiteit 

Een samengestelde entiteit bestaat uit andere entiteiten, zoals vooraf gebouwde entiteiten, eenvoudige, reguliere expressies en lijst entiteiten. De afzonderlijke entiteiten vormen een hele entiteit. 

**Deze entiteit is geschikt voor de volgende gegevens:**

* Zijn aan elkaar gerelateerd. 
* Ze zijn aan elkaar gerelateerd in de context van de utterance.
* Verschillende typen entiteit gebruiken.
* Moeten worden gegroepeerd en verwerkt door de client toepassing als een gegevens eenheid.
* Hebben een aantal uitingen voor gebruikers die machine learning nodig hebben.

![samengestelde entiteit](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Voor beeld JSON

Overweeg een samengestelde entiteit van vooraf `number` gebouwde `Location::ToLocation` en met de volgende utterance:

`book 2 tickets to cairo`

U ziet `2`dat, het nummer en `cairo`, de ToLocation woorden hebben die geen deel uitmaken van een van de entiteiten. De groene onderstreping, die wordt gebruikt in een gelabelde utterance in de [Luis](luis-reference-regions.md) -website, geeft een samengestelde entiteit aan.

![Samengestelde entiteit](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

Samengestelde entiteiten worden geretourneerd in een `compositeEntities` matrix en alle entiteiten binnen de samen stelling worden ook geretourneerd in `entities` de matrix:

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

#### <a name="v3-prediction-endpoint-response"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)

Dit is de JSON als `verbose=false` deze is ingesteld in de query reeks:

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

Dit is de JSON als `verbose=true` deze is ingesteld in de query reeks:

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


|Gegevens object|Entiteitsnaam|Waarde|
|--|--|--|
|Vooraf gebouwde entiteits nummer|Builtin. Number|twee|
|Preconstrueerde entiteit-GeographyV2|"Locatie:: ToLocation"|Cairo|

## <a name="next-steps"></a>Volgende stappen

In deze [zelf studie](luis-tutorial-composite-entity.md)voegt u een **samengestelde entiteit** toe om geëxtraheerde gegevens van verschillende typen te bundelen in één container met een entiteit. Door de gegevens te bundelen, kan de client toepassing eenvoudig gerelateerde gegevens in verschillende gegevens typen ophalen.
