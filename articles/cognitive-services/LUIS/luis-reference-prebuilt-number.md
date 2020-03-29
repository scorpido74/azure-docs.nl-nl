---
title: Vooraf gebouwde entiteit nummer - LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat vooraf gebouwde entiteitsinformatie in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 85e3589d7467691e2b9a11879510ab980bbd875a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273468"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Vooraf gebouwde entiteit nummer voor een LUIS-app
Er zijn vele manieren waarop numerieke waarden worden gebruikt om stukjes informatie te kwantificeren, uit te drukken en te beschrijven. Dit artikel behandelt slechts enkele mogelijke voorbeelden. LUIS interpreteert de variaties in gebruikersuitingen en retourneert consistente numerieke waarden. Omdat deze entiteit al is getraind, hoeft u geen voorbeelduitingen met nummer toe te voegen aan de toepassingsintenties.

## <a name="types-of-number"></a>Typen nummers
Nummer wordt beheerd vanuit de [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) GitHub repository

## <a name="examples-of-number-resolution"></a>Voorbeelden van getalresolutie

| Utterance        | Entiteit   | Oplossing |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      |
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS bevat de herkende **`builtin.number`** waarde van `resolution` een entiteit in het veld van het JSON-antwoord dat wordt geretourneerd.

## <a name="resolution-for-prebuilt-number"></a>Oplossing voor vooraf opgebouwd nummer

De volgende entiteitsobjecten worden geretourneerd voor de query:

`order two dozen eggs`

#### <a name="v3-response"></a>[V3-antwoord](#tab/V3)

De volgende JSON `verbose` is met `false`de parameter ingesteld op:

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/V3-verbose)

De volgende JSON `verbose` is met `true`de parameter ingesteld op:

```json
"entities": {
    "number": [
        24
    ],
    "$instance": {
        "number": [
            {
                "type": "builtin.number",
                "text": "two dozen",
                "startIndex": 6,
                "length": 9,
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

In het volgende voorbeeld ziet u een JSON-antwoord van LUIS, dat de resolutie van de waarde 24 bevat, voor de utterance "twee dozijn".

```json
"entities": [
  {
    "entity": "two dozen",
    "type": "builtin.number",
    "startIndex": 6,
    "endIndex": 14,
    "resolution": {
      "subtype": "integer",
      "value": "24"
    }
  }
]
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over de [valuta](luis-reference-prebuilt-currency.md), [ordinal](luis-reference-prebuilt-ordinal.md)en [percentage](luis-reference-prebuilt-percentage.md).
