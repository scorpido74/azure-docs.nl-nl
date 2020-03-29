---
title: Pattern.any entity type - LUIS
titleSuffix: Azure Cognitive Services
description: Pattern.any is een tijdelijke aanduiding met variabele lengte die alleen wordt gebruikt in de sjabloonutterance van een patroon om aan te geven waar de entiteit begint en eindigt.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 5164bf55ef8233cf34a470524da3bc852678d79a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75979167"
---
# <a name="patternany-entity"></a>Pattern.any-entiteit

Pattern.any is een tijdelijke aanduiding met variabele lengte die alleen wordt gebruikt in de sjabloonutterance van een patroon om aan te geven waar de entiteit begint en eindigt.  

Pattern.any entiteiten moeten worden gemarkeerd in de voorbeelden [van](luis-how-to-model-intent-pattern.md) patroonsjablonen, niet in de voorbeelden van de intentiegebruiker.

**De entiteit past goed wanneer:**

* Het einde van de entiteit kan worden verward met de resterende tekst van de utterance.

## <a name="usage"></a>Gebruik

Gezien een client applicatie die zoekt naar boeken op basis van titel, het patroon.alle uittreksels de volledige titel. Een sjabloonutterance met behulp van pattern.any voor deze boekzoekopdracht is `Was {BookTitle} written by an American this year[?]`.

In de volgende tabel heeft elke rij twee versies van de utterance. De bovenste utterance is hoe LUIS in eerste instantie de utterance ziet. Het is niet duidelijk waar de titel van het boek begint en eindigt. De onderste utterance gebruikt een patroon.een entiteit om het begin en einde van de entiteit te markeren.

|Utterance met entiteit in vet|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Was **the Man Who Mistook zijn vrouw voor een hoed en andere klinische verhalen** geschreven door een Amerikaan dit jaar?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Was **Half Asleep in Frog Pyjama's** geschreven door een Amerikaan dit jaar?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Was **the Particular Sadness of Lemon Cake: A Novel** geschreven door een Amerikaan dit jaar?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Was **er een Wocket in mijn zak!** geschreven door een Amerikaan dit jaar?|
||



## <a name="example-json"></a>Voorbeeld JSON

Laten we nu eens naar deze query kijken:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

Met de ingesloten formuliernaam om als patroon uit te pakken:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[V2 voorspelling eindpuntrespons](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 voorspelling eindpuntrespons](#tab/V3)

Dit is de `verbose=false` JSON als deze is ingesteld in de querytekenreeks:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Dit is de `verbose=true` JSON als deze is ingesteld in de querytekenreeks:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
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

Gebruik in deze [zelfstudie](luis-tutorial-pattern.md)de entiteit **Pattern.any** om gegevens te extraheren uit uitingen waarbij de uitingen goed zijn opgemaakt en waar het einde van de gegevens gemakkelijk kan worden verward met de resterende woorden van de utterance.
