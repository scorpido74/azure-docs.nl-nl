---
title: Percentage vooraf gebouwde entiteit - LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat percentage vooraf gebouwde entiteitsinformatie in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 31ea1c36139abcb1e102161ad76a203073ba4dfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270501"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Percentage vooraf gebouwde entiteit voor een LUIS-app
Percentagegetallen kunnen worden weergegeven `3 1/2`als breuken `2%`, of als percentage , . Omdat deze entiteit al is getraind, hoeft u geen voorbeelduitingen toe te voegen die percentage bevatten aan de toepassingsintenties. Percentage entiteit wordt ondersteund in [vele culturen](luis-reference-prebuilt-entities.md).

## <a name="types-of-percentage"></a>Soorten percentage
Percentage wordt beheerd vanuit de [GitHub-repository Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)

## <a name="resolution-for-prebuilt-percentage-entity"></a>Oplossing voor vooraf gebouwde percentageentiteit

De volgende entiteitsobjecten worden geretourneerd voor de query:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON `verbose` is met `false`de parameter ingesteld op:

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/V3-verbose)
De volgende JSON `verbose` is met `true`de parameter ingesteld op:

```json
"entities": {
    "percentage": [
        2
    ],
    "$instance": {
        "percentage": [
            {
                "type": "builtin.percentage",
                "text": "2%",
                "startIndex": 36,
                "length": 2,
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

In het volgende voorbeeld ziet u de resolutie van de entiteit **builtin.percentage.**

```json
"entities": [
    {
        "entity": "2%",
        "type": "builtin.percentage",
        "startIndex": 36,
        "endIndex": 37,
        "resolution": {
        "value": "2%"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over de [ordinale,](luis-reference-prebuilt-ordinal.md) [aantal-](luis-reference-prebuilt-number.md)en [temperatuurentiteiten.](luis-reference-prebuilt-temperature.md)
