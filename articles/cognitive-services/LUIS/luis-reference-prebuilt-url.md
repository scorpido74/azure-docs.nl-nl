---
title: URL-voorgebouwde entiteiten - LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf ingebouwde informatie over de url in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 49f145ce3e9022826abad5d274dd611bb2cc6530
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270352"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>URL-voorgebouwde entiteit voor een LUIS-app
URL-entiteit haalt URL's uit met domeinnamen of IP-adressen. Omdat deze entiteit al is getraind, hoeft u geen voorbeelduitingen met URL's aan de toepassing toe te voegen. URL-entiteit wordt `en-us` alleen ondersteund in cultuur.

## <a name="types-of-urls"></a>Typen URL's
Url wordt beheerd vanuit de [GitHub-repository Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)

## <a name="resolution-for-prebuilt-url-entity"></a>Oplossing voor vooraf gebouwde URL-entiteit

De volgende entiteitsobjecten worden geretourneerd voor de query:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON `verbose` is met `false`de parameter ingesteld op:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/V3-verbose)

De volgende JSON `verbose` is met `true`de parameter ingesteld op:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ],
    "$instance": {
        "url": [
            {
                "type": "builtin.url",
                "text": "https://www.luis.ai",
                "startIndex": 0,
                "length": 17,
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

Het volgende voorbeeld toont https://www.luis.ai de resolutie van de is een groot voorbeeld van cognitieve diensten van kunstmatige intelligentie

```json
"entities": [
    {
        "entity": "https://www.luis.ai",
        "type": "builtin.url",
        "startIndex": 0,
        "endIndex": 17
    }
]
```

* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over de [ordinale,](luis-reference-prebuilt-ordinal.md) [aantal-](luis-reference-prebuilt-number.md)en [temperatuurentiteiten.](luis-reference-prebuilt-temperature.md)
