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
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 1307e6cfca0debe7623eb775c69527a74584033d
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74011993"
---
# <a name="list-entity"></a>Lijstentiteit 

Lijst entiteiten vertegenwoordigen een vaste, gesloten set verwante woorden samen met hun synoniemen. LUIS detecteert geen aanvullende waarden voor de lijst met entiteiten. Gebruik de **raden** functie om te bekijken van suggesties voor nieuwe woorden op basis van de huidige lijst. Als er meer dan één lijst entiteit met dezelfde waarde, wordt elke entiteit in de query eindpunt geretourneerd. 

Een lijst entiteit is niet door de machine geleerd. Het is een overeenkomst exact overeenkomende tekst. LUIS markeert een overeenkomst aan een item in een lijst als een entiteit in het antwoord. 

**De entiteit is goed geschikt wanneer de tekst gegevens:**

* Zijn een bekende set.
* Verandert niet vaak. Als u de lijst vaak wilt wijzigen of de lijst zelf wilt uitbreiden, is een eenvoudige entiteit met een woordgroepen lijst een betere keuze. 
* De set maximale [begrenzingen](luis-boundaries.md) van LUIS voor dit entiteitstype niet overschrijdt.
* De tekst in de utterance is een exact overeenkomst met een synoniem of de canonieke naam. LUIS gebruikt de lijst alleen voor exact tekstovereenkomsten. Fuzzy matching, hoofdletter ongevoeligheid, ontleding, meervouden en andere variaties worden niet opgelost met een lijst entiteit. Hiervoor kunt u overwegen een [patroon](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) met de optionele tekstsyntaxis te gebruiken.

![lijst met entiteiten](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Voor beeld. json om in lijst entiteit te importeren

  U kunt waarden importeren in een bestaande lijst entiteit met behulp van de volgende JSON-indeling:

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]  
  ```

## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

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

In deze [zelf studie](luis-quickstart-intent-and-list-entity.md)leert u hoe u een **lijst entiteit** kunt gebruiken om exacte overeenkomsten met tekst te extra heren uit een lijst met bekende items. 
