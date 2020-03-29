---
title: Luis Prebuilt entiteiten e-mailverwijzing
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf gebouwde informatie over e-mailentiteiten in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: f7aa2a341a82d30f7022af49e626a3cd358854b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273485"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Vooraf gebouwde entiteit e-mailen voor een LUIS-app
E-mailextractie bevat het volledige e-mailadres van een utterance. Omdat deze entiteit al is getraind, hoeft u geen voorbeelduitingen met e-mail toe te voegen aan de toepassingsintenties. E-mailentiteit wordt `en-us` alleen ondersteund in cultuur.

## <a name="resolution-for-prebuilt-email"></a>Oplossing voor vooraf gebouwde e-mail

De volgende entiteitsobjecten worden geretourneerd voor de query:

`please send the information to patti@contoso.com`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON `verbose` is met `false`de parameter ingesteld op:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/V3-verbose)

De volgende JSON `verbose` is met `true`de parameter ingesteld op:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ],
    "$instance": {
        "email": [
            {
                "type": "builtin.email",
                "text": "patti@contoso.com",
                "startIndex": 31,
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

In het volgende voorbeeld ziet u de oplossing van de entiteit **builtin.email.**

```json
"entities": [
    {
        "entity": "patti@contoso.com",
        "type": "builtin.email",
        "startIndex": 31,
        "endIndex": 55,
        "resolution": {
        "value": "patti@contoso.com"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over het [aantal](luis-reference-prebuilt-number.md) [, ordinal](luis-reference-prebuilt-ordinal.md)en [percentage](luis-reference-prebuilt-percentage.md).
