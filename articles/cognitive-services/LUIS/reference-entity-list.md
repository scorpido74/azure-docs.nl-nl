---
title: Type lijstentiteit - LUIS
description: Lijstentiteiten vertegenwoordigen een vaste, gesloten reeks verwante woorden, samen met hun synoniemen. LUIS ontdekt geen extra waarden voor lijstentiteiten. Gebruik de functie Aanbevelen om suggesties voor nieuwe woorden te bekijken op basis van de huidige lijst.
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 795d16bc2e0c4223ff3ac283a72493923d3ab355
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297234"
---
# <a name="list-entity"></a>Lijstentiteit

Lijstentiteiten vertegenwoordigen een vaste, gesloten reeks verwante woorden, samen met hun synoniemen. LUIS ontdekt geen extra waarden voor lijstentiteiten. Gebruik de functie **Aanbevelen** om suggesties voor nieuwe woorden te bekijken op basis van de huidige lijst. Als er meer dan één lijstentiteit met dezelfde waarde is, wordt elke entiteit geretourneerd in de eindpuntquery.

Een lijstentiteit wordt niet machine-geleerd. Het is een exacte tekst overeenkomst. LUIS markeert elke overeenkomst met een item in een lijst als een entiteit in het antwoord.

**De entiteit past goed bij de gegevens van de tekst:**

* Zijn een bekende set.
* Dat verandert niet vaak. Als u de lijst vaak moet wijzigen of wilt dat de lijst zichzelf wordt uitgebreid, is een eenvoudige entiteit die is geboost met een woordenlijst een betere keuze.
* De set maximale [begrenzingen](luis-boundaries.md) van LUIS voor dit entiteitstype niet overschrijdt.
* De tekst in de utterance is een case-ongevoelige overeenkomst met een synoniem of de canonieke naam. LUIS gebruikt de lijst niet verder dan de wedstrijd. Fuzzy matching, stemming, meervouden en andere varianten worden niet opgelost met een lijstentiteit. Hiervoor kunt u overwegen een [patroon](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) met de optionele tekstsyntaxis te gebruiken.

![lijstentiteit](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Voorbeeld .json om te importeren in lijstentiteit

  U waarden importeren in een bestaande lijstentiteit met behulp van de volgende JSON-indeling:

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

Stel dat de app `Cities`een lijst heeft met de naam , waarmee variaties van stadsnamen kunnen worden toegestaan, waaronder de luchthavenstad (Sea-tac), luchthavencode (SEA), postcode (98101) en telefoonnummer (206).

|Lijstitem|Objectsynoniemen|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

In de vorige utterance `paris` wordt het woord toegewezen aan `Cities` het item in Parijs als onderdeel van de lijstentiteit. De lijstentiteit komt overeen met zowel de genormaliseerde naam van het item als de itemsynoniemen.

#### <a name="v2-prediction-endpoint-response"></a>[V2 voorspelling eindpuntrespons](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3 voorspelling eindpuntrespons](#tab/V3)


Dit is de `verbose=false` JSON als deze is ingesteld in de querytekenreeks:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Dit is de `verbose=true` JSON als deze is ingesteld in de querytekenreeks:

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

|Gegevensobject|Entiteitsnaam|Waarde|
|--|--|--|
|Lijstentiteit|`Cities`|`paris`|


## <a name="next-steps"></a>Volgende stappen

In deze [zelfstudie](tutorial-list-entity.md)leert u hoe u een **lijstentiteit** gebruikt om exacte overeenkomsten met tekst uit een lijst met bekende items te extraheren.
