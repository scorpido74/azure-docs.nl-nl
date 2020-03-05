---
title: URL-preconstrueerde entiteiten-LUIS
titleSuffix: Azure Cognitive Services
description: In dit artikel bevat een url vooraf gedefinieerde entiteitgegevens in Language Understanding (LUIS).
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270352"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>URL preconstrueerde entiteit voor een LUIS-app
URL-entiteit extraheert URL's met domeinnamen of IP-adressen. Omdat deze entiteit wordt al getraind, hoeft u niet om toe te voegen van de voorbeeld-uitingen met URL's naar de toepassing. URL-entiteit wordt alleen ondersteund in `en-us` cultuur.

## <a name="types-of-urls"></a>Typen van URL 's
URL wordt beheerd vanuit de map [recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) github

## <a name="resolution-for-prebuilt-url-entity"></a>Oplossing voor vooraf gedefinieerde URL-entiteit

De volgende entiteits objecten worden geretourneerd voor de query:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON is waarvan de `verbose` para meter is ingesteld op `false`:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/V3-verbose)

De volgende JSON is waarvan de `verbose` para meter is ingesteld op `true`:

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

In het volgende voor beeld ziet u de oplossing van de https://www.luis.ai is een fantastisch voor beeld van een fraaie cognitieve service van kunst matige intelligentie

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
