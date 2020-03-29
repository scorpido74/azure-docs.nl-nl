---
title: Ordinal V2 voorgebouwde entiteit - LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat prebuilt informatie over de entiteit v2 in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 5e852313db75e598da647ea0f985e2ee18af16de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270489"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Ordinale V2-voorgebouwde entiteit voor een LUIS-app
Ordinal V2-nummer breidt [Ordinal](luis-reference-prebuilt-ordinal.md) uit `next`met `last`relatieve `previous`verwijzingen zoals , en . Deze worden niet geëxtraheerd met behulp van de ordinale voorgebouwde entiteit.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Oplossing voor prebuilt ordinale V2-entiteit

De volgende entiteitsobjecten worden geretourneerd voor de query:

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON `verbose` is met `false`de parameter ingesteld op:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/V3-verbose)

De volgende JSON `verbose` is met `true`de parameter ingesteld op:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ],
    "$instance": {
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2.relative",
                "text": "the second to last",
                "startIndex": 8,
                "length": 18,
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

In het volgende voorbeeld ziet u de resolutie van de entiteit **builtin.ordinalV2.**

```json
"entities": [
    {
        "entity": "the second to last",
        "type": "builtin.ordinalV2.relative",
        "startIndex": 8,
        "endIndex": 25,
        "resolution": {
            "offset": "-1",
            "relativeTo": "end"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over het [percentage,](luis-reference-prebuilt-percentage.md) [telefoonnummer](luis-reference-prebuilt-phonenumber.md)en [temperatuurentiteiten.](luis-reference-prebuilt-temperature.md)
