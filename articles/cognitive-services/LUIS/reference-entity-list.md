---
title: Entiteits type van lijst-LUIS
titleSuffix: Azure Cognitive Services
description: Lijst entiteiten vertegenwoordigen een vaste, gesloten set verwante woorden samen met hun synoniemen. LUIS detecteert geen aanvullende waarden voor de lijst met entiteiten. Gebruik de functie aanbevolen om suggesties voor nieuwe woorden op basis van de huidige lijst te bekijken.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 1757faf8ab2be0b62956b6939ee068929f9275a4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695241"
---
# <a name="list-entity"></a>Lijstentiteit 

Lijst entiteiten vertegenwoordigen een vaste, gesloten set verwante woorden samen met hun synoniemen. LUIS detecteert geen aanvullende waarden voor de lijst met entiteiten. Gebruik de **raden** functie om te bekijken van suggesties voor nieuwe woorden op basis van de huidige lijst. Als er meer dan één lijst entiteit met dezelfde waarde, wordt elke entiteit in de query eindpunt geretourneerd. 

Een lijst entiteit is niet door de machine geleerd. Het is een overeenkomst exact overeenkomende tekst. LUIS markeert een overeenkomst aan een item in een lijst als een entiteit in het antwoord. 

**De entiteit is goed geschikt wanneer de tekst gegevens:**

* Zijn een bekende set.
* Verandert niet vaak. Als u de lijst vaak wilt wijzigen of de lijst zelf wilt uitbreiden, is een eenvoudige entiteit met een woordgroepen lijst een betere keuze. 
* De set maximale [begrenzingen](luis-boundaries.md) van LUIS voor dit entiteitstype niet overschrijdt.
* De tekst in de utterance is een exact overeenkomst met een synoniem of de canonieke naam. LUIS gebruikt de lijst alleen voor exact tekstovereenkomsten. Fuzzy matching, hoofdletter ongevoeligheid, ontleding, meervouden en andere variaties worden niet opgelost met een lijst entiteit. Hiervoor kunt u overwegen een [patroon](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) met de optionele tekstsyntaxis te gebruiken.

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

|Data-object|De naam van de entiteit|Value|
|--|--|--|
|Entiteit weer geven|`Cities`|`paris`|


## <a name="next-steps"></a>Volgende stappen

In deze [zelf studie](luis-quickstart-intent-and-list-entity.md)leert u hoe u een **lijst entiteit** kunt gebruiken om exacte overeenkomsten met tekst te extra heren uit een lijst met bekende items. 
