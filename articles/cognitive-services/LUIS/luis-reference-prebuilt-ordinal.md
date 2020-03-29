---
title: Ordinale voorgebouwde entiteit - LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat ordinale vooraf gebouwde entiteitsinformatie in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bb3bb27db48255f534e873ed4e93ac62f07016af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273449"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Ordinale vooraf gebouwde entiteit voor een LUIS-app
Ordinaal getal is een numerieke weergave van `first` `second`een `third`object in een set: , , Omdat deze entiteit al is getraind, hoeft u geen voorbeelduitingen toe te voegen die ordinaal zijn, aan de toepassingsintenties. Ordinale entiteit wordt ondersteund in [vele culturen.](luis-reference-prebuilt-entities.md)

## <a name="types-of-ordinal"></a>Soorten ordinale
Ordinal wordt beheerd vanuit de [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) GitHub repository

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Oplossing voor vooraf gebouwde ordinale entiteit

De volgende entiteitsobjecten worden geretourneerd voor de query:

`Order the second option`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON `verbose` is met `false`de parameter ingesteld op:

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/V3-verbose)
De volgende JSON `verbose` is met `true`de parameter ingesteld op:

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
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

In het volgende voorbeeld ziet u de resolutie van de **entiteit builtin.ordinal.**

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over de [OrdinalV2,](luis-reference-prebuilt-ordinal-v2.md) [telefoonnummer](luis-reference-prebuilt-phonenumber.md)en [temperatuurentiteiten.](luis-reference-prebuilt-temperature.md)
