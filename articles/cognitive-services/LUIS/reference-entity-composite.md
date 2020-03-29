---
title: Samengesteld entiteitstype - LUIS
titleSuffix: Azure Cognitive Services
description: Een samengestelde entiteit bestaat uit andere entiteiten, zoals vooraf gebouwde entiteiten, eenvoudige, reguliere expressie en lijstentiteiten. De afzonderlijke entiteiten vormen een hele entiteit.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71695157"
---
# <a name="composite-entity"></a>Samengestelde entiteit 

Een samengestelde entiteit bestaat uit andere entiteiten, zoals vooraf gebouwde entiteiten, eenvoudige, reguliere expressie en lijstentiteiten. De afzonderlijke entiteiten vormen een hele entiteit. 

**Deze entiteit past goed bij de gegevens:**

* Zijn met elkaar verbonden. 
* Ze zijn aan elkaar gerelateerd in de context van de utterance.
* Gebruik verschillende entiteitstypen.
* Moeten worden gegroepeerd en verwerkt door de clientapplicatie als een eenheid van informatie.
* Hebben een verscheidenheid van gebruiker uitingen die machine-learning vereisen.

![samengestelde entiteit](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Voorbeeld JSON

Overweeg een samengestelde entiteit `number` `Location::ToLocation` van vooraf gebouwde en met de volgende uiting:

`book 2 tickets to cairo`

Merk `2`op dat , `cairo`het nummer, en , de ToLocation hebben woorden tussen hen die geen deel uitmaken van een van de entiteiten. De groene onderstreping, gebruikt in een gelabelde utterance op de [LUIS-website,](luis-reference-regions.md) geeft een samengestelde entiteit aan.

![Samengestelde entiteit](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[V2 voorspelling eindpuntrespons](#tab/V2)

Samengestelde entiteiten worden `compositeEntities` geretourneerd in een array en alle entiteiten binnen de samengestelde worden ook geretourneerd in de `entities` array:

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 voorspelling eindpuntrespons](#tab/V3)

Dit is de `verbose=false` JSON als deze is ingesteld in de querytekenreeks:

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

Dit is de `verbose=true` JSON als deze is ingesteld in de querytekenreeks:

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


|Gegevensobject|Entiteitsnaam|Waarde|
|--|--|--|
|Vooraf gebouwde entiteit - getal|"builtin.nummer"|"2"|
|Voorgebouwde entiteit - GeografieV2|"Locatie::ToLocation"|"Caïro"|

## <a name="next-steps"></a>Volgende stappen

Voeg in deze [zelfstudie](luis-tutorial-composite-entity.md)een **samengestelde entiteit** toe om geëxtraheerde gegevens van verschillende typen te bundelen in één bevattende entiteit. Door de gegevens te bundelen, kan de clientapplicatie eenvoudig gerelateerde gegevens in verschillende gegevenstypen extraheren.
