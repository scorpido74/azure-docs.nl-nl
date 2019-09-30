---
title: Vooraf gemaakte telefoon nummers-LUIS
titleSuffix: Azure Cognitive Services
description: In dit artikel bevat vooraf gedefinieerde entiteit telefoonnummergegevens in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 9e237b09bd2b0ecc3e91e10697f6c025f73057da
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677441"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Op telefoon nummer vooraf gebouwde entiteit voor een LUIS-app
De `phonenumber` entiteit extraheert tal van telefoonnummers, inclusief landcode. Omdat deze entiteit wordt al getraind, hoeft u geen voorbeeld uitingen toevoegen aan de toepassing. De `phonenumber` entiteit wordt ondersteund in `en-us` alleen de cultuur. 

## <a name="types-of-a-phone-number"></a>Typen van een telefoon nummer
`Phonenumber` wordt beheerd vanuit de [recognizers-tekst github-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) opslag plaats

## <a name="resolution-for-this-prebuilt-entity"></a>Oplossing voor deze vooraf gebouwde entiteit

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

Het volgende voorbeeld ziet u de resolutie van de **builtin.phonenumber** entiteit.

```json
{
  "query": "my mobile is 1 (800) 642-7676",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)

De volgende JSON is met de para meter `verbose` ingesteld op `false`:

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ]
        }
    }
}
```

De volgende JSON is met de para meter `verbose` ingesteld op `true`:

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ],
            "$instance": {
                "phonenumber": [
                    {
                        "type": "builtin.phonenumber",
                        "text": "1 (800) 642-7676",
                        "startIndex": 13,
                        "length": 16,
                        "score": 1,
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

Meer informatie over de [percentage](luis-reference-prebuilt-percentage.md), [getal](luis-reference-prebuilt-number.md), en [temperatuur](luis-reference-prebuilt-temperature.md) entiteiten. 
