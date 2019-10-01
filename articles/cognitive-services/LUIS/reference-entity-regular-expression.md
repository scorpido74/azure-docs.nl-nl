---
title: Entiteits type van reguliere expressie-LUIS
titleSuffix: Azure Cognitive Services
description: Een reguliere expressie is het meest geschikt voor onbewerkte utterance-tekst. Deze aanvraag wordt genegeerd en worden genegeerd culturele variant.  Reguliere expressie die overeenkomt met is na spellingcontrole wijzigingen op het niveau van het teken, niet het niveau van de token toegepast.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: ae46df875d588186cd083134820f349158d7e307
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695217"
---
# <a name="regular-expression-entity"></a>Een entiteit in de vorm van een reguliere expressie 

Een reguliere expressie-entiteit extraheert een entiteit op basis van een reguliere expressie patroon dat u opgeeft.

Een reguliere expressie is het meest geschikt voor onbewerkte utterance-tekst. Deze aanvraag wordt genegeerd en worden genegeerd culturele variant.  Reguliere expressie die overeenkomt met is na spellingcontrole wijzigingen op het niveau van het teken, niet het niveau van de token toegepast. Als de reguliere expressie te complex is, zoals het gebruik van een groot aantal haken, kunt u de expressie niet toevoegen aan het model. Maakt gebruik van een deel, maar niet alle [.net regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) -bibliotheek. 

**De entiteit is goed geschikt wanneer:**

* De gegevens zijn consistent opgemaakt met elke variatie die ook consistent is.
* De reguliere expressie heeft niet meer dan 2 geneste niveaus nodig. 

![Een entiteit in de vorm van een reguliere expressie](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Gebruiks overwegingen

Reguliere expressies kunnen overeenkomen met meer dan verwacht. Een voor beeld hiervan is een numeriek woord dat overeenkomt met `one` en `two`. Een voor beeld is de volgende regex die overeenkomt met het aantal `one` en andere getallen:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
``` 

Deze regex-expressie komt ook overeen met een wille keurig woord dat met deze getallen eindigt, zoals `phone`. Als u problemen wilt oplossen, moet u ervoor zorgen dat de regex-overeenkomsten rekening houden met woord grenzen. De regex voor het gebruik van woord grenzen voor dit voor beeld wordt gebruikt in de volgende regex:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Voorbeeld van JSON

Bij het gebruik van `kb[0-9]{6}`, als de reguliere expressie-entiteits definitie, is de volgende JSON-reactie een voor beeld van een utterance met de geretourneerde reguliere expressie-entiteiten voor de query:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)


Dit is de JSON als `verbose=false` is ingesteld in de query reeks:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Dit is de JSON als `verbose=true` is ingesteld in de query reeks:

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 

## <a name="next-steps"></a>Volgende stappen

In deze [zelf studie](luis-quickstart-intents-regex-entity.md)maakt u een app om consistent opgemaakte gegevens te extra heren uit een utterance met behulp van de **reguliere expressie** -entiteit.