---
title: Entiteitstype voor reguliere expressies - LUIS
titleSuffix: Azure Cognitive Services
description: Een reguliere expressie is het beste voor ruwe utterancetekst. Het negeert geval en negeert culturele variant.  Regelmatige expressie matching wordt toegepast na spellingcontrole wijzigingen op het tekenniveau, niet het tokenniveau.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: b9da76a80183f353a74d43e667bf6c9219eb6c05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74841214"
---
# <a name="regular-expression-entity"></a>Een entiteit in de vorm van een reguliere expressie

Een entiteit met reguliere expressie haalt een entiteit uit op basis van een regelmatig expressiepatroon dat u opgeeft.

Een reguliere expressie is het beste voor ruwe utterancetekst. Het negeert geval en negeert culturele variant.  Regelmatige expressie matching wordt toegepast na spellingcontrole wijzigingen op het tekenniveau, niet het tokenniveau. Als de reguliere expressie te complex is, zoals het gebruik van veel haakjes, u de expressie niet aan het model toevoegen. Gebruikt een deel, maar niet alle [.NET Regex-bibliotheek.](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions)

**De entiteit past goed wanneer:**

* De gegevens zijn consistent opgemaakt met elke variatie die ook consistent is.
* De reguliere expressie heeft niet meer dan 2 nestniveaus nodig.

![Een entiteit in de vorm van een reguliere expressie](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Gebruiksoverwegingen

Reguliere expressies kunnen meer overeenkomen dan u verwacht. Een voorbeeld hiervan is numerieke `one` woordmatching zoals en `two`. Een voorbeeld is de volgende regex, die overeenkomt met het nummer `one` samen met andere nummers:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Deze regex-expressie komt ook overeen met `phone`alle woorden die eindigen met deze getallen, zoals . Om dit soort problemen op te lossen, moet u ervoor zorgen dat de regex-overeenkomsten rekening houden met woordgrenzen. De regex om woordgrenzen voor dit voorbeeld te gebruiken, wordt gebruikt in de volgende regex:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Voorbeeld JSON

Bij `kb[0-9]{6}`het gebruik van de definitie van de entiteit voor reguliere expressieen is het volgende JSON-antwoord een voorbeeldutterance met de geretourneerde reguliere expressieentiteiten voor de query:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[V2 voorspelling eindpuntrespons](#tab/V2)

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


#### <a name="v3-prediction-endpoint-response"></a>[V3 voorspelling eindpuntrespons](#tab/V3)


Dit is de `verbose=false` JSON als deze is ingesteld in de querytekenreeks:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Dit is de `verbose=true` JSON als deze is ingesteld in de querytekenreeks:

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

Maak in deze [zelfstudie](tutorial-regex-entity.md)een app om consistent opgemaakte gegevens uit een utterance te extraheren met behulp van de entiteit **Reguliere expressie.**