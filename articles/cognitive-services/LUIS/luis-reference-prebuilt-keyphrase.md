---
title: Vooraf samengestelde LUIS van de woordgroepen-entiteit
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf opgemaakte woordgroepen entiteits gegevens in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 51d1bd515651824545d486207ad4a74476aa7092
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491271"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>vooraf samengestelde woordgroepen instantie voor een LUIS-app
De entiteit woordgroep haalt een aantal sleutel zinnen op uit een utterance. U hoeft geen voorbeeld uitingen met een Woordgroeps groep aan de toepassing toe te voegen. De woordgroepen entiteit wordt in [veel cultures](luis-language-support.md#languages-supported) ondersteund als onderdeel van de functie voor [tekst analyse](../text-analytics/overview.md) . 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Oplossing voor vooraf gebouwde woordgroepen entiteit

De volgende entiteits objecten worden geretourneerd voor de query:

`where is the educational requirements form for the development and engineering group`

#### <a name="v3-responsetabv3"></a>[V3-antwoord](#tab/V3)

De volgende JSON is waarvan de `verbose` para meter is ingesteld op `false`:

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Uitgebreide respons van v3](#tab/V3-verbose)
De volgende JSON is waarvan de `verbose` para meter is ingesteld op `true`:

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
#### <a name="v2-responsetabv2"></a>[V2-antwoord](#tab/V2)

In het volgende voor beeld ziet u de resolutie van de entiteit **Builtin. Superwoord groep** .

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

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

Meer informatie over de eenheden [percentage](luis-reference-prebuilt-percentage.md), [aantal](luis-reference-prebuilt-number.md)en [leeftijd](luis-reference-prebuilt-age.md) .
