---
title: De vooraf samengestelde entiteit van de persoon-LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf opgebouwde entiteits gegevens in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 9777c62d97c70d4f6a0d0a4d912dea3fa8decd23
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499543"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>De vooraf samengestelde entiteit van de persoon voor een LUIS-app
De entiteit van de vooraf gedefinieerde persoons naam detecteert namen van personen. Omdat deze entiteit al is getraind, hoeft u geen voorbeeld uitingen toe te voegen die zijn toegevoegd aan de toepassings intentie. entiteit voor persoons wordt ondersteund in de Engelse en Chinese [cult uren](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Omzetting voor entiteit persoonnaam

De volgende entiteits objecten worden geretourneerd voor de query:

`Is Jill Jones in Cairo?`


#### <a name="v3-responsetabv3"></a>[V3-antwoord](#tab/V3)


De volgende JSON is waarvan de `verbose` para meter is ingesteld op `false`:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Uitgebreide respons van v3](#tab/V3-verbose)
De volgende JSON is waarvan de `verbose` para meter is ingesteld op `true`:

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
#### <a name="v2-responsetabv2"></a>[V2-antwoord](#tab/V2)

In het volgende voor beeld wordt de oplossing van de entiteit **Builtin. persoonnaam** weer gegeven.

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

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

Meer informatie over de entiteiten [e-mail](luis-reference-prebuilt-email.md), [nummer](luis-reference-prebuilt-number.md)en [rang telwoord](luis-reference-prebuilt-ordinal.md) . 
