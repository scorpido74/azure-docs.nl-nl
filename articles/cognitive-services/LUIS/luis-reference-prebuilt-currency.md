---
title: Valuta prebuilt entiteit - LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf gebouwde informatie over entiteiten in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 56d264fa976967ae4772f50b4aa6f58b30355d6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270770"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Vooraf gebouwde valuta voor een LUIS-app
De vooraf gebouwde valutaentiteit detecteert valuta in veel coupures en landen/regio's, ongeacht de luis-app-cultuur. Omdat deze entiteit al is getraind, hoeft u geen voorbeelduitingen met valuta toe te voegen aan de toepassingsintenties. Valuta entiteit wordt ondersteund in [vele culturen](luis-reference-prebuilt-entities.md).

## <a name="types-of-currency"></a>Soorten valuta
Valuta wordt beheerd vanuit de [GitHub-repository met Recognizers-tekst](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Resolutie voor valutaentiteit

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON `verbose` is met `false`de parameter ingesteld op:

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
#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/V3-verbose)
De volgende JSON `verbose` is met `true`de parameter ingesteld op:

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

#### <a name="v2-response"></a>[V2-antwoord](#tab/V2)

In het volgende voorbeeld ziet u de resolutie van de entiteit **builtin.currency.**

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

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over de [datumtijdV2,](luis-reference-prebuilt-datetimev2.md) [dimensie](luis-reference-prebuilt-dimension.md)en [e-mailentiteiten.](luis-reference-prebuilt-email.md)
