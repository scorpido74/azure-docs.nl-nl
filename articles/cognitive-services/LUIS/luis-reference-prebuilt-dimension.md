---
title: Dimensies vooraf gemaakte entiteiten-LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf samengestelde entiteits gegevens in Language Understanding (LUIS).
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 6699b1617ccd1fef9a507e71fdd73a02b0e98bea
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465031"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Samengestelde dimensie-entiteit voor een LUIS-app
De vooraf samengestelde dimensie-entiteit detecteert verschillende typen dimensies, ongeacht de LUIS-app-cultuur. Omdat deze entiteit al is getraind, hoeft u geen voorbeeld uitingen met dimensies toe te voegen aan de toepassings intentie. Dimensie-entiteit wordt ondersteund in [veel cult uren](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Typen dimensie

Dimensie wordt beheerd vanuit de GitHub-opslag plaats voor [tekst herkenning](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) .

## <a name="resolution-for-dimension-entity"></a>Oplossing voor dimensie-entiteit

De volgende entiteits objecten worden geretourneerd voor de query:

`10 1/2 miles of cable`

#### <a name="v3-responsetabv3"></a>[V3-antwoord](#tab/V3)

De volgende JSON is waarvan de `verbose` para meter is ingesteld op `false`:

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
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Uitgebreide respons van v3](#tab/V3-verbose)
De volgende JSON is waarvan de `verbose` para meter is ingesteld op `true`:

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

#### <a name="v2-responsetabv2"></a>[V2-antwoord](#tab/V2)

In het volgende voor beeld ziet u de resolutie van de **inbuiltin. dimensie** -entiteit.

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

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

Meer informatie over de entiteiten [e-mail](luis-reference-prebuilt-email.md), [nummer](luis-reference-prebuilt-number.md)en [rang telwoord](luis-reference-prebuilt-ordinal.md) . 
