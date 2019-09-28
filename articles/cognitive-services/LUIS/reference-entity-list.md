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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 2a9e3d16f745e8f51d1d375a774d7c687e987efe
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350816"
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

|Data-object|De naam van de entiteit|Value|
|--|--|--|
|Entiteit weer geven|`Cities`|`paris`|

Een ander voorbeeld-utterance, met behulp van een synoniem voor Parijs:

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

|Data-object|De naam van de entiteit|Value|
|--|--|--|
|Entiteit weer geven|`Cities`|`roissy`|

## <a name="next-steps"></a>Volgende stappen

In deze [zelf studie](luis-quickstart-intent-and-list-entity.md)leert u hoe u een **lijst entiteit** kunt gebruiken om exacte overeenkomsten met tekst te extra heren uit een lijst met bekende items. 
