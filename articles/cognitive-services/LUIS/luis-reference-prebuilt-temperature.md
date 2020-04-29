---
title: Preconstrueerde temperatuur eenheid-LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat informatie over de standaard temperatuur van de entiteit in Language Understanding (LUIS).
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270372"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Preconstrueerde temperatuur eenheid voor een LUIS-app
Met de Tempe ratuur wordt een aantal verschillende temperatuur typen geëxtraheerd. Omdat deze entiteit al is getraind, hoeft u geen voor beeld-uitingen met een Tempe ratuur aan de toepassing toe te voegen. De temperatuur entiteit wordt ondersteund in [veel cult uren](luis-reference-prebuilt-entities.md).

## <a name="types-of-temperature"></a>Typen Tempe ratuur
De Tempe ratuur wordt beheerd vanuit de [recognizers-tekst github-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) opslag plaats

## <a name="resolution-for-prebuilt-temperature-entity"></a>Oplossing voor vooraf samengestelde temperatuur entiteit

De volgende entiteits objecten worden geretourneerd voor de query:

`set the temperature to 30 degrees`


#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON is met de `verbose` para meter ingesteld `false`op:

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
#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/V3-verbose)
De volgende JSON is met de `verbose` para meter ingesteld `true`op:

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

In het volgende voor beeld ziet u de resolutie van de **inbuiltin. Tempe ratuur** -entiteit.

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

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

Meer informatie over de eenheden [percentage](luis-reference-prebuilt-percentage.md), [aantal](luis-reference-prebuilt-number.md)en [leeftijd](luis-reference-prebuilt-age.md) .
