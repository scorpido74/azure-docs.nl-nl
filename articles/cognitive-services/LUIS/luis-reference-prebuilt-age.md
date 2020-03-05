---
title: Leeftijd preconstrueerde entiteit-LUIS
titleSuffix: Azure Cognitive Services
description: In dit artikel bevat leeftijd vooraf gedefinieerde entiteitgegevens in Language Understanding (LUIS).
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270796"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Leeftijd preconstrueerde entiteit voor een LUIS-app
De vooraf gedefinieerde leeftijd entiteit bevat de leeftijdwaarde zowel numeriek en in termen van dagen, weken, maanden en jaren. Omdat deze entiteit wordt al getraind, hoeft u niet om toe te voegen van de voorbeeld-uitingen met leeftijd aan de toepassing intents. De leeftijds entiteit wordt in [veel cultures](luis-reference-prebuilt-entities.md)ondersteund.

## <a name="types-of-age"></a>Typen leeftijd
De leeftijd wordt beheerd vanuit de map [recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) github

## <a name="resolution-for-prebuilt-age-entity"></a>Oplossing voor vooraf gedefinieerde leeftijd entiteit



#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON is waarvan de `verbose` para meter is ingesteld op `false`:

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
#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/V3-verbose)
De volgende JSON is waarvan de `verbose` para meter is ingesteld op `true`:

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

In het volgende voor beeld ziet u de resolutie van de **inbuiltin. Age** -entiteit.

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

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

Meer informatie over de [valuta](luis-reference-prebuilt-currency.md)-, [datetimeV2](luis-reference-prebuilt-datetimev2.md)-en [dimensie](luis-reference-prebuilt-dimension.md) -entiteiten.
