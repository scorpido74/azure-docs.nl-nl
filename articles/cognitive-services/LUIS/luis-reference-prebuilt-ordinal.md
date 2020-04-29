---
title: Vooraf samengestelde ordinale entiteit-LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf opgemaakte informatie over de volg orde van de gegevens in Language Understanding (LUIS).
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273449"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Vooraf gebouwde ordinale entiteit voor een LUIS-app
Rang nummer is een numerieke representatie van een object in een set: `first`, `second`, `third`. Omdat deze entiteit al is getraind, hoeft u geen voor beeld-uitingen met een rang telwoord toe te voegen aan de toepassings intentie. Ordinale entiteit wordt ondersteund in [veel cult uren](luis-reference-prebuilt-entities.md).

## <a name="types-of-ordinal"></a>Typen rang telwoord
Rang telwoord wordt beheerd vanuit de map [recognizers-text github-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) opslag plaats

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Oplossing voor de vooraf gedefinieerde ordinale entiteit

De volgende entiteits objecten worden geretourneerd voor de query:

`Order the second option`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON is met de `verbose` para meter ingesteld `false`op:

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/V3-verbose)
De volgende JSON is met de `verbose` para meter ingesteld `true`op:

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

In het volgende voor beeld ziet u de resolutie van de **inbuiltin. ordinal** -entiteit.

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

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

Meer informatie over de [OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), het [telefoon nummer](luis-reference-prebuilt-phonenumber.md)en de [temperatuur](luis-reference-prebuilt-temperature.md) entiteiten.
