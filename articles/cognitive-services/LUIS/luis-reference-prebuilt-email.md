---
title: Naslag informatie voor LUIS-vooraf gemaakte entiteiten
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf opgemaakte e-mail gegevens van de entiteit in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6f262752a50b58eae8ffbea81b8e7fc4d8c65b98
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464976"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>E-mail vooraf gebouwde entiteit voor een LUIS-app
E-mail extractie bevat het volledige e-mail adres van een utterance. Omdat deze entiteit al is getraind, hoeft u geen voor beeld-uitingen met een e-mail bericht aan de toepassings intentie toe te voegen. De e-mail entiteit wordt alleen ondersteund in `en-us` cultuur. 

## <a name="resolution-for-prebuilt-email"></a>Oplossing voor vooraf gegenereerde e-mail

De volgende entiteits objecten worden geretourneerd voor de query:

`please send the information to patti@contoso.com`

#### <a name="v3-responsetabv3"></a>[V3-antwoord](#tab/V3)

De volgende JSON is waarvan de `verbose` para meter is ingesteld op `false`:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Uitgebreide respons van v3](#tab/V3-verbose)

De volgende JSON is waarvan de `verbose` para meter is ingesteld op `true`:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ],
    "$instance": {
        "email": [
            {
                "type": "builtin.email",
                "text": "patti@contoso.com",
                "startIndex": 31,
                "length": 17,
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

In het volgende voor beeld ziet u de oplossing van de **ingebouwde e-mail-** entiteit.

```json
"entities": [
    {
        "entity": "patti@contoso.com",
        "type": "builtin.email",
        "startIndex": 31,
        "endIndex": 55,
        "resolution": {
        "value": "patti@contoso.com"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

Meer informatie over het [aantal](luis-reference-prebuilt-number.md), het [rang telwoord](luis-reference-prebuilt-ordinal.md)en het [percentage](luis-reference-prebuilt-percentage.md). 
