---
title: Aantal vooraf samengestelde entiteit-LUIS
titleSuffix: Azure Cognitive Services
description: In dit artikel bevat een aantal vooraf gedefinieerde entiteitgegevens in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: cb97cc5b0004442e00b970202dd01f76aa971a2a
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677576"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Aantal vooraf samengestelde entiteiten voor een LUIS-app
Er zijn veel manieren waarin numerieke waarden te kwantificeren, express en beschrijven stukjes informatie worden gebruikt. Dit artikel behandelt alleen enkele van de mogelijke voorbeelden. LUIS interpreteert de variaties faciliteren voor de gebruiker uitingen en consistente numerieke waarden als resultaat. Omdat deze entiteit wordt al getraind, hoeft u niet om toe te voegen voorbeeld uitingen met getal tot de toepassing intents. 

## <a name="types-of-number"></a>Typen van getal
Nummer wordt beheerd vanuit de map [recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) github

## <a name="examples-of-number-resolution"></a>Voorbeelden van het aantal resolutie

| Utterance        | Entiteit   | Oplossing |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS omvat de herkende waarde van een **`builtin.number`** entiteit in de `resolution` veld van het JSON-antwoord wordt geretourneerd.

## <a name="resolution-for-prebuilt-number"></a>Oplossing voor vooraf gedefinieerde getal


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

Het volgende voorbeeld ziet een JSON-antwoord van LUIS, die de resolutie van de waarde van 24 uur per dag, voor de utterance "tientallen" bevat.

```json
{
  "query": "order two dozen eggs",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.105443209
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.105443209
    },
    {
      "intent": "OrderFood",
      "score": 0.9468431361
    },
    {
      "intent": "Help",
      "score": 0.000399122015
    },
  ],
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "subtype": "integer",
        "value": "24"
      }
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)

De volgende JSON is met de para meter `verbose` ingesteld op `false`:

```json
{
    "query": "order two dozen eggs",
    "prediction": {
        "normalizedQuery": "order two dozen eggs",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "number": [
                24
            ]
        }
    }
}
```

De volgende JSON is met de para meter `verbose` ingesteld op `true`:

```json
{
    "query": "order two dozen eggs",
    "prediction": {
        "normalizedQuery": "order two dozen eggs",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "number": [
                24
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "two dozen",
                        "startIndex": 6,
                        "length": 9,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
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

Meer informatie over de [valuta](luis-reference-prebuilt-currency.md), [rangtelwoord](luis-reference-prebuilt-ordinal.md), en [percentage](luis-reference-prebuilt-percentage.md). 
