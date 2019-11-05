---
title: Leeftijd preconstrueerde entiteit-LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat informatie over de leeftijds voorgedefinieerde entiteit in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 2a49cf406dea045302f84e95318eca1557fc3e22
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465103"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Leeftijd preconstrueerde entiteit voor een LUIS-app
De vooraf gemaakte leeftijds entiteit legt de leeftijds waarde zowel numeriek als in termen van dagen, weken, maanden en jaren vast. Omdat deze entiteit al is getraind, hoeft u geen voor beeld-uitingen met leeftijd toe te voegen aan de doel stellingen van de toepassing. De leeftijds entiteit wordt in [veel cultures](luis-reference-prebuilt-entities.md)ondersteund. 

## <a name="types-of-age"></a>Typen leeftijd
De leeftijd wordt beheerd vanuit de map [recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) github

## <a name="resolution-for-prebuilt-age-entity"></a>Oplossing voor vooraf samengestelde leeftijds entiteit



#### <a name="v3-responsetabv3"></a>[V3-antwoord](#tab/V3)

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
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Uitgebreide respons van v3](#tab/V3-verbose)
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
#### <a name="v2-responsetabv2"></a>[V2-antwoord](#tab/V2)

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
