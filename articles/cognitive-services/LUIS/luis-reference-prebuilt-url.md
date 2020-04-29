---
title: URL-preconstrueerde entiteiten-LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat URL-vooraf opgebouwde entiteits gegevens in Language Understanding (LUIS).
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270352"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>URL preconstrueerde entiteit voor een LUIS-app
URL-entiteit extraheert Url's met domein namen of IP-adressen. Omdat deze entiteit al is getraind, hoeft u geen voor beeld-uitingen met Url's aan de toepassing toe te voegen. URL-entiteit wordt alleen `en-us` ondersteund in cultuur.

## <a name="types-of-urls"></a>Typen Url's
URL wordt beheerd vanuit de map [recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) github

## <a name="resolution-for-prebuilt-url-entity"></a>Oplossing voor preingebouwde URL-entiteit

De volgende entiteits objecten worden geretourneerd voor de query:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON is met de `verbose` para meter ingesteld `false`op:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/V3-verbose)

De volgende JSON is met de `verbose` para meter ingesteld `true`op:

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

In het volgende voor beeld ziet u de https://www.luis.ai oplossing van het is een fantastische voor beeld van de cognitieve services van kunst matige intelligentie

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

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

Meer informatie over het [rang telwoord](luis-reference-prebuilt-ordinal.md), het [aantal](luis-reference-prebuilt-number.md)en de [Tempe ratuur](luis-reference-prebuilt-temperature.md) .
