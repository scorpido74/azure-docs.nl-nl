---
title: Door de machine geleerde entiteits type-LUIS
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 2289e8ac7744a7b4cbee300e77efda89d29ee2f5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74017217"
---
# <a name="machine-learned-entity"></a>Door de machine geleerde entiteit 



**De entiteit is goed geschikt wanneer de tekst gegevens:**


![lijst met entiteiten](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>Voorbeeld van JSON

Stel dat de app heeft een lijst met de naam `Cities`, zodat voor variaties in plaats van luchthaven (Sea-tac), luchthaven code (SEA), postcode (98101) en het netnummer telefoon (206) zoals plaatsnamen.

|Lijstitem|Item synoniemen|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

In de vorige utterance, het woord `paris` is toegewezen aan het item Parijs als onderdeel van de `Cities` lijst van de entiteit. De entiteit lijst komt overeen met zowel genormaliseerde naam van het item als het item synoniemen.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)


Dit is de JSON als `verbose=false` is ingesteld in de query reeks:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Dit is de JSON als `verbose=true` is ingesteld in de query reeks:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 

|Data-object|De naam van de entiteit|Waarde|
|--|--|--|
|Entiteit weer geven|`Cities`|`paris`|


## <a name="next-steps"></a>Volgende stappen

Meer informatie over de entiteit [lijst](reference-entity-list.md) entiteit en [reguliere expressie](reference-entity-regular-expression.md) .