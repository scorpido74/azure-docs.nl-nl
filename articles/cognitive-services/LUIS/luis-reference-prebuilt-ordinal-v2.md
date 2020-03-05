---
title: Rang telwoord v2 vooraf gebouwde entiteit-LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf opgemaakte nummer v2-entiteits gegevens in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 5e852313db75e598da647ea0f985e2ee18af16de
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270489"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Rang telwoord v2 vooraf gebouwde entiteit voor een LUIS-app
Ordinal v2-nummer breidt [rang telwoord](luis-reference-prebuilt-ordinal.md) uit om relatieve verwijzingen zoals `next`, `last`en `previous`op te geven. Deze worden niet geëxtraheerd met behulp van de vooraf gebouwde ordinale entiteit.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Oplossing voor vooraf ontwikkelde rang telwoord v2-entiteit

De volgende entiteits objecten worden geretourneerd voor de query:

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON is waarvan de `verbose` para meter is ingesteld op `false`:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Uitgebreide respons van v3](#tab/V3-verbose)

De volgende JSON is waarvan de `verbose` para meter is ingesteld op `true`:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ],
    "$instance": {
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2.relative",
                "text": "the second to last",
                "startIndex": 8,
                "length": 18,
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

In het volgende voor beeld ziet u de oplossing van de **ingebouwde entiteit. ordinalV2** .

```json
"entities": [
    {
        "entity": "the second to last",
        "type": "builtin.ordinalV2.relative",
        "startIndex": 8,
        "endIndex": 25,
        "resolution": {
            "offset": "-1",
            "relativeTo": "end"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

Meer informatie over het [percentage](luis-reference-prebuilt-percentage.md), het [telefoon nummer](luis-reference-prebuilt-phonenumber.md)en de [temperatuur](luis-reference-prebuilt-temperature.md) entiteiten.
