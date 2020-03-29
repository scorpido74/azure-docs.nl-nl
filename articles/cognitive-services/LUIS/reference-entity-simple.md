---
title: Eenvoudig entiteitstype - LUIS
titleSuffix: Azure Cognitive Services
description: Een eenvoudige entiteit beschrijft één concept uit de machine-geleerde context. Voeg een woordenlijst toe wanneer u een eenvoudige entiteit gebruikt om de resultaten te verbeteren.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 8b92aa6057c81ec9442372c5b85918cb92196d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74894765"
---
# <a name="simple-entity"></a>Eenvoudige entiteit

Een eenvoudige entiteit is een algemene entiteit die één concept beschrijft en wordt geleerd uit de door de machine geleerde context. Omdat eenvoudige entiteiten over het algemeen namen zijn, zoals bedrijfsnamen, productnamen of andere categorieën namen, voegt u een [woordgroeplijst](luis-concept-feature.md) toe wanneer u een eenvoudige entiteit gebruikt om het signaal van de gebruikte namen te stimuleren.

**De entiteit past goed wanneer:**

* De gegevens zijn niet consistent opgemaakt, maar geven hetzelfde aan.

![eenvoudige entiteit](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Voorbeeld JSON

`Bob Jones wants 3 meatball pho`

In de vorige `Bob Jones` utterance wordt het `Customer` label als een eenvoudige entiteit aangeduid.

De gegevens die vanaf het eindpunt worden geretourneerd, omvatten de entiteitsnaam, de gedetecteerde tekst uit de utterance, de locatie van de gedetecteerde tekst en de score:

#### <a name="v2-prediction-endpoint-response"></a>[V2 voorspelling eindpuntrespons](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 voorspelling eindpuntrespons](#tab/V3)

Dit is de `verbose=false` JSON als deze is ingesteld in de querytekenreeks:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|Gegevensobject|Entiteitsnaam|Waarde|
|--|--|--|
|Eenvoudige entiteit|`Customer`|`bob jones`|

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Patroonsyntaxis leren](reference-pattern-syntax.md)