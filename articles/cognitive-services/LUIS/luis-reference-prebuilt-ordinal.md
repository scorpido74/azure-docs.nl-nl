---
title: Vooraf samengestelde ordinale entiteit-LUIS
titleSuffix: Azure Cognitive Services
description: In dit artikel bevat rangtelwoord vooraf gedefinieerde entiteitgegevens in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: d1d69b256c4fc7e7b9d1c84b7c409d01a9f8ce52
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677544"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Vooraf gebouwde ordinale entiteit voor een LUIS-app
Rangtelwoord is een numerieke representatie van een object in een set: `first`, `second`, `third`. Omdat deze entiteit wordt al getraind, hoeft u niet om toe te voegen van de voorbeeld-uitingen met rangtelwoord voor de toepassing intents. Rangtelwoord entiteit wordt ondersteund in [veel culturen](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Typen volgnummer
Rang telwoord wordt beheerd vanuit de map [recognizers-text github-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) opslag plaats

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Oplossing voor vooraf gedefinieerde rangtelwoord entiteit

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

Het volgende voorbeeld ziet u de resolutie van de **builtin.ordinal** entiteit.

```json
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)

De volgende JSON is met de para meter `verbose` ingesteld op `false`:

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ]
        }
    }
}
```

De volgende JSON is met de para meter `verbose` ingesteld op `true`:

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ],
            "$instance": {
                "ordinal": [
                  {
                    "type": "builtin.ordinal",
                    "text": "second",
                    "startIndex": 10,
                    "length": 6,
                    "modelTypeId": 2,
                    "modelType": "Prebuilt Entity Extractor",
                    "recognitionSources": [
                        "model"
                    ]
                  }
                ]
            }
        }
    }
}
```

* * * 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

Meer informatie over de [OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), het [telefoon nummer](luis-reference-prebuilt-phonenumber.md)en de [temperatuur](luis-reference-prebuilt-temperature.md) entiteiten. 
