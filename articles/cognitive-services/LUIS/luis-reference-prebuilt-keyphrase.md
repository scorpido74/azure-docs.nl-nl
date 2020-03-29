---
title: Sleutelzin voorgebouwde entiteit - LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat keyphrase prebuilt entity information in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 53be1b13f1e2744e143a4be0777e3a8e3135460e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270533"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>sleutelzinnen voor een vooraf gebouwde entiteit voor een LUIS-app
De entiteit keyPhrase haalt een verscheidenheid aan sleutelzinnen uit een utterance. U hoeft geen voorbeelduitingen met keyPhrase aan de toepassing toe te voegen. De entiteit keyPhrase wordt in [veel culturen](luis-language-support.md#languages-supported) ondersteund als onderdeel van de functies [voor tekstanalyse.](../text-analytics/overview.md)

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Oplossing voor vooraf gebouwde entiteit KeyPhrase

De volgende entiteitsobjecten worden geretourneerd voor de query:

`where is the educational requirements form for the development and engineering group`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON `verbose` is met `false`de parameter ingesteld op:

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/V3-verbose)
De volgende JSON `verbose` is met `true`de parameter ingesteld op:

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "educational requirements",
                "startIndex": 13,
                "length": 24,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "development",
                "startIndex": 51,
                "length": 11,
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

In het volgende voorbeeld wordt de resolutie van de entiteit **builtin.keyPhrase** weergegeven.

```json
"entities": [
    {
        "entity": "development",
        "type": "builtin.keyPhrase",
        "startIndex": 51,
        "endIndex": 61
    },
    {
        "entity": "educational requirements",
        "type": "builtin.keyPhrase",
        "startIndex": 13,
        "endIndex": 36
    }
]
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over het [percentage,](luis-reference-prebuilt-percentage.md) [het aantal](luis-reference-prebuilt-number.md)en [de leeftijdsentiteiten.](luis-reference-prebuilt-age.md)
