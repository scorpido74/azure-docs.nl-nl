---
title: Temperatuur prebuilt entiteit - LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf ingebouwde informatie over de entiteit in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 7e2b48c6353f56ab2269a8718146cb765797adba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270372"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Vooraf gebouwde entiteit voor temperatuur voor een LUIS-app
Temperatuur haalt een verscheidenheid aan temperatuurtypes. Omdat deze entiteit al is getraind, hoeft u geen voorbeelduitingen met temperatuur toe te voegen aan de toepassing. Temperatuur entiteit wordt ondersteund in [vele culturen](luis-reference-prebuilt-entities.md).

## <a name="types-of-temperature"></a>Soorten temperatuur
Temperatuur wordt beheerd vanuit de [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) GitHub repository

## <a name="resolution-for-prebuilt-temperature-entity"></a>Oplossing voor vooraf gebouwde temperatuurentiteit

De volgende entiteitsobjecten worden geretourneerd voor de query:

`set the temperature to 30 degrees`


#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON `verbose` is met `false`de parameter ingesteld op:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/V3-verbose)
De volgende JSON `verbose` is met `true`de parameter ingesteld op:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ],
    "$instance": {
        "temperature": [
            {
                "type": "builtin.temperature",
                "text": "30 degrees",
                "startIndex": 23,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2-antwoord](#tab/V2)

In het volgende voorbeeld ziet u de resolutie van de entiteit **builtin.temperature.**

```json
"entities": [
    {
        "entity": "30 degrees",
        "type": "builtin.temperature",
        "startIndex": 23,
        "endIndex": 32,
        "resolution": {
        "unit": "Degree",
        "value": "30"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over het [percentage,](luis-reference-prebuilt-percentage.md) [het aantal](luis-reference-prebuilt-number.md)en [de leeftijdsentiteiten.](luis-reference-prebuilt-age.md)
