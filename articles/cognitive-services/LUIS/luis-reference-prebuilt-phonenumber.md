---
title: Telefoonnummer Vooraf gebouwde entiteiten - LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf gebouwde entiteitsinformatie in Taalbegrip (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4e2c8e27c6d4195252c6a5b423fa98b2a4247182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270471"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Vooraf gebouwde entiteit voor telefoonnummers voor een LUIS-app
De `phonenumber` entiteit haalt verschillende telefoonnummers, waaronder landcode. Omdat deze entiteit al is getraind, hoeft u geen voorbeelduitingen aan de toepassing toe te voegen. De `phonenumber` entiteit wordt `en-us` alleen ondersteund in cultuur.

## <a name="types-of-a-phone-number"></a>Typen van een telefoonnummer
`Phonenumber`wordt beheerd vanuit de [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) GitHub repository

## <a name="resolution-for-this-prebuilt-entity"></a>Oplossing voor deze vooraf gebouwde entiteit

De volgende entiteitsobjecten worden geretourneerd voor de query:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON `verbose` is met `false`de parameter ingesteld op:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/V3-verbose)
De volgende JSON `verbose` is met `true`de parameter ingesteld op:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ],
    "$instance": {

        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 (800) 642-7676",
                "startIndex": 13,
                "length": 16,
                "score": 1.0,
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

In het volgende voorbeeld ziet u de resolutie van de entiteit **builtin.phonenumber.**

```json
"entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over het [percentage,](luis-reference-prebuilt-percentage.md) [het aantal](luis-reference-prebuilt-number.md)en [de temperatuurentiteiten.](luis-reference-prebuilt-temperature.md)
