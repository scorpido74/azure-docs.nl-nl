---
title: Leeftijd Prebuilt entiteit - LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat informatie over de voorgebouwde entiteit suc in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 1607b80276a9c35bf0ac2f0a00bd2226e2c07f97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270796"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Prebuilt entiteit leeftijd voor een LUIS-app
De vooraf gebouwde leeftijdsentiteit vangt de leeftijdswaarde zowel numeriek als in termen van dagen, weken, maanden en jaren. Omdat deze entiteit al is getraind, hoeft u geen voorbeelduitingen met leeftijd toe te voegen aan de toepassingsintenties. Leeftijd entiteit wordt ondersteund in [vele culturen](luis-reference-prebuilt-entities.md).

## <a name="types-of-age"></a>Leeftijdstypen
Leeftijd wordt beheerd vanuit de [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) GitHub repository

## <a name="resolution-for-prebuilt-age-entity"></a>Oplossing voor vooraf gebouwde leeftijdsentiteit



#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON `verbose` is met `false`de parameter ingesteld op:

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/V3-verbose)
De volgende JSON `verbose` is met `true`de parameter ingesteld op:

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ],
    "$instance": {
        "age": [
            {
                "type": "builtin.age",
                "text": "90 day old",
                "startIndex": 2,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2-antwoord](#tab/V2)

In het volgende voorbeeld ziet u de oplossing van de entiteit **builtin.age.**

```json
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over de [valuta,](luis-reference-prebuilt-currency.md) [datetimeV2](luis-reference-prebuilt-datetimev2.md)en [dimensieentiteiten.](luis-reference-prebuilt-dimension.md)
