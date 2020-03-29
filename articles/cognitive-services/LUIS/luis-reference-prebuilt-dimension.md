---
title: Dimensie vooraf gebouwde entiteiten - LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf gebouwde entiteitsinformatie in Language Understanding (LUIS).
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 3c7bebb38d0331c76eeaaa1d9d6464c568a61b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270636"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Dimensie vooraf gebouwde entiteit voor een LUIS-app
De vooraf gebouwde dimensieentiteit detecteert verschillende soorten dimensies, ongeacht de LUIS-app-cultuur. Omdat deze entiteit al is getraind, hoeft u geen voorbeelduitingen met afmetingen toe te voegen aan de toepassingsintenties. Dimension entiteit wordt ondersteund in [vele culturen](luis-reference-prebuilt-entities.md).

## <a name="types-of-dimension"></a>Soorten afmetingen

Dimension wordt beheerd vanuit de [GitHub-repository met Recognizers-tekst.](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml)

## <a name="resolution-for-dimension-entity"></a>Oplossing voor dimensieentiteit

De volgende entiteitsobjecten worden geretourneerd voor de query:

`10 1/2 miles of cable`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON `verbose` is met `false`de parameter ingesteld op:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/V3-verbose)
De volgende JSON `verbose` is met `true`de parameter ingesteld op:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ],
    "$instance": {
        "dimension": [
            {
                "type": "builtin.dimension",
                "text": "10 1/2 miles",
                "startIndex": 0,
                "length": 12,
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

In het volgende voorbeeld ziet u de resolutie van de entiteit **builtin.dimension.**

```json
{
    "entity": "10 1/2 miles",
    "type": "builtin.dimension",
    "startIndex": 0,
    "endIndex": 11,
    "resolution": {
    "unit": "Mile",
    "value": "10.5"
    }
}
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over de [e-mail,](luis-reference-prebuilt-email.md) [het nummer](luis-reference-prebuilt-number.md)en [de ordinale](luis-reference-prebuilt-ordinal.md) entiteiten.
