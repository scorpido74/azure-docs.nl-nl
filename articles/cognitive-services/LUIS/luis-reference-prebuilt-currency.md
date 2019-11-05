---
title: Valuta vooraf samengestelde entiteit-LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat informatie over de valuta vooraf opgebouwde entiteit in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 5b49dcc7e999757e119c399bdf01bed7cb312e02
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465044"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Voor de valuta vooraf samengestelde entiteit voor een LUIS-app
De vooraf samengestelde valuta-entiteit detecteert valuta in veel coupures en landen/regio's, ongeacht de LUIS-app-cultuur. Omdat deze entiteit al is getraind, hoeft u geen voor beeld-uitingen met valuta toe te voegen aan de toepassings intentie. De valuta-entiteit wordt ondersteund in [veel cultures](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Soorten valuta
Valuta wordt beheerd vanuit de map [recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) github

## <a name="resolution-for-currency-entity"></a>Oplossing voor valuta-entiteit

#### <a name="v3-responsetabv3"></a>[V3-antwoord](#tab/V3)

De volgende JSON is waarvan de `verbose` para meter is ingesteld op `false`:

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "units": "Dollar"
        }
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Uitgebreide respons van v3](#tab/V3-verbose)
De volgende JSON is waarvan de `verbose` para meter is ingesteld op `true`:

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "unit": "Dollar"
        }
    ],
    "$instance": {
        "money": [
            {
                "type": "builtin.currency",
                "text": "$10.99",
                "startIndex": 23,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```

#### <a name="v2-responsetabv2"></a>[V2-antwoord](#tab/V2)

In het volgende voor beeld ziet u de oplossing van de **inbuiltin. Currency** -entiteit.

```json
"entities": [
    {
        "entity": "$10.99",
        "type": "builtin.currency",
        "startIndex": 23,
        "endIndex": 28,
        "resolution": {
        "unit": "Dollar",
        "value": "10.99"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

Meer informatie over de entiteiten [datetimeV2](luis-reference-prebuilt-datetimev2.md), [Dimension](luis-reference-prebuilt-dimension.md)en [e-mail](luis-reference-prebuilt-email.md) . 
