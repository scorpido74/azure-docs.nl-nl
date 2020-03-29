---
title: PersonName prebuilt entiteit - LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat personName prebuilt entity information in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 768c719211e8a8f2133d3798343d076e795a3da0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273420"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>PersonName-voorgebouwde entiteit voor een LUIS-app
De vooraf gebouwde entiteit personName detecteert namen van personen. Omdat deze entiteit al is getraind, hoeft u geen voorbeelduitingen met personName toe te voegen aan de toepassingsintenties. personName entiteit wordt ondersteund in het Engels en Chinese [culturen](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Oplossing voor de entiteit personName

De volgende entiteitsobjecten worden geretourneerd voor de query:

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)


De volgende JSON `verbose` is met `false`de parameter ingesteld op:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/V3-verbose)
De volgende JSON `verbose` is met `true`de parameter ingesteld op:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[V2-antwoord](#tab/V2)

In het volgende voorbeeld ziet u de oplossing van de entiteit **builtin.personName.**

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over de [e-mail,](luis-reference-prebuilt-email.md) [het nummer](luis-reference-prebuilt-number.md)en [de ordinale](luis-reference-prebuilt-ordinal.md) entiteiten.
