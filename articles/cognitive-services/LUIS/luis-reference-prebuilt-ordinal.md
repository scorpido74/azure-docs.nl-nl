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
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: b2a2d9e78a0b152da14bb737079cf0dfdef0dc05
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491240"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Vooraf gebouwde ordinale entiteit voor een LUIS-app
Rang nummer is een numerieke representatie van een object binnen een set: `first`, `second``third`. Omdat deze entiteit al is getraind, hoeft u geen voor beeld-uitingen met een rang telwoord toe te voegen aan de toepassings intentie. Ordinale entiteit wordt ondersteund in [veel cult uren](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Typen rang telwoord
Rang telwoord wordt beheerd vanuit de map [recognizers-text github-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) opslag plaats

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Oplossing voor de vooraf gedefinieerde ordinale entiteit

De volgende entiteits objecten worden geretourneerd voor de query:

`Order the second option`

#### <a name="v3-responsetabv3"></a>[V3-antwoord](#tab/V3)

De volgende JSON is waarvan de `verbose` para meter is ingesteld op `false`:

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Uitgebreide respons van v3](#tab/V3-verbose)
De volgende JSON is waarvan de `verbose` para meter is ingesteld op `true`:

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

#### <a name="v2-responsetabv2"></a>[V2-antwoord](#tab/V2)

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
