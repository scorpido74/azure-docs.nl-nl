---
title: DatetimeV2 prebuiled entities-LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel vindt u datetimeV2 vooraf gedefinieerde entiteitgegevens in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4f46efaeddb0bfe789ef752abdd133c14da514da
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677694"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2 prebuiled-entiteit voor een LUIS-app

De **datetimeV2** vooraf gedefinieerde entiteit haalt de waarden voor datum en tijd. Deze waarden omzetten in een gestandaardiseerde indeling voor client-programma's te gebruiken. Wanneer een utterance heeft een datum of tijd die is niet voltooid, LUIS bevat _zowel afgelopen en toekomstige waarden_ in het antwoord van het eindpunt. Omdat deze entiteit wordt al getraind, hoeft u niet om toe te voegen voorbeeld uitingen met datetimeV2 naar de toepassing intents. 

## <a name="types-of-datetimev2"></a>Typen datetimeV2
DatetimeV2 wordt beheerd vanuit de map [recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) github

## <a name="example-json"></a>Voorbeeld van JSON 
Het volgende voorbeeld-JSON-antwoord heeft een `datetimeV2` entiteit met een subtype van `datetime`. Zie voor meer voorbeelden van andere typen entiteiten datetimeV2 [subtypen van datetimeV2](#subtypes-of-datetimev2)</a>.

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
  ```

## <a name="json-property-descriptions"></a>Beschrijvingen van JSON-eigenschap

|Naam van eigenschap |Eigenschap van het type en beschrijving|
|---|---|
|Entiteit|**tekenreeks** -uit de utterance met het type van de datum, tijd, datumbereik of tijdsbereik geëxtraheerde tekst.|
|type|**tekenreeks** : één van de [subtypen van datetimeV2](#subtypes-of-datetimev2)
|startIndex|**int** -de index in de utterance waarop de entiteit wordt gestart.|
|endIndex|**int** -de index in de utterance waarop de entiteit is beëindigd.|
|oplossing|Heeft een `values` matrix waarvoor een, twee of vier [waarden van het probleem zou moeten](#values-of-resolution).|
|einde|De eindwaarde van een tijd of datumbereik, op dezelfde indeling als `value`. Alleen gebruikt als `type` is `daterange`, `timerange`, of `datetimerange`|

## <a name="subtypes-of-datetimev2"></a>Subtypen van datetimeV2

De **datetimeV2** vooraf gedefinieerde entiteit heeft de volgende subtypen en voorbeelden van elk vindt u in de tabel die volgt op:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

## <a name="values-of-resolution"></a>Waarden van de oplossing
* De matrix heeft een element als de datum of tijd in de utterance volledig is opgegeven en niet-ambigue.
* De matrix heeft twee elementen als de waarde datetimeV2 niet eenduidig is. Dubbelzinnigheid bevat gebrek aan een bepaald jaar, tijd of tijdsbereik. Zie [niet-eenduidige datums](#ambiguous-dates) voor voorbeelden. Wanneer het tijd is niet eenduidig voor uur. of het uur van beide waarden zijn opgenomen.
* De matrix heeft vier elementen als de utterance bestaat uit twee elementen met dubbelzinnigheid. Deze onduidelijkheid bevat elementen die zijn:
  * Een datum of datumbereik dat is niet eenduidig garantie voor jaar
  * Een tijd of het tijdsbereik dat is niet eenduidig dat uur. of uur. Voorbeeld 3:00 3 April.

Elk element van de `values` matrix mogelijk de volgende velden: 

|Naam van eigenschap|Beschrijving van de eigenschap|
|--|--|
|Timex|tijd, datum of datumbereik uitgedrukt in TIMEX-indeling die volgt op de [ISO 8601-norm](https://en.wikipedia.org/wiki/ISO_8601) en de kenmerken TIMEX3 van aantekening met behulp van de taal TimeML. Deze aantekening wordt beschreven in de [TIMEX richtlijnen](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|type|Het subtype, dat een van de volgende items kan zijn: `datetime`, `date`, `time`, `daterange`, `timerange`, `datetimerange`, `duration`, `set`.|
|value|**Optioneel.** Een datum/tijd-object in de indeling yyyy:MM:dd (datum), uu: mm: (tijd) yyyy:MM:dd uu: mm: (datetime). Als `type` is `duration`, de waarde is het aantal seconden (duur) <br/> Alleen gebruikt als `type` is `datetime` of `date`, `time`, of ' duur.|

## <a name="valid-date-values"></a>Geldige date-waarden

De **datetimeV2** ondersteunt datums tussen de volgende bereiken:

| Min. | Max. |
|----------|-------------|
| 1 januari 1900   | 31 December 2099 |

## <a name="ambiguous-dates"></a>Niet-eenduidige datums

Als de datum in het verleden of in de toekomst, biedt LUIS beide waarden. Een voorbeeld is een utterance met de datum zonder het jaar en maand.  

Bijvoorbeeld, krijgt de utterance 'Mei 2e':
* Als de datum van vandaag mei 3e 2017 is, biedt LUIS "2017-05-02" en "2018-05-02" als waarden. 
* Wanneer de datum van vandaag 1 mei-2017 is, biedt LUIS '2016-05-02' en "2017-05-02" als waarden.

Het volgende voorbeeld ziet de resolutie van de entiteit 'mei 2e'. Deze oplossing wordt ervan uitgegaan dat de datum van vandaag een datum tussen mei 2e 2017 en mei 2018 op 1.
Velden met `X` in de `timex` veld zijn onderdelen van de datum die niet zijn expliciet worden opgegeven in de utterance.

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-numeric-date"></a>Datum bereik resolutie voorbeelden voor numerieke datum

De `datetimeV2` entiteit extraheert datum en tijd bereiken. De `start` en `end` velden geven het begin en einde van het bereik. Voor de utterance 'Mei 2e op 5 mei', LUIS biedt **daterange** waarden voor het huidige jaar en het komende jaar. In de `timex` veld, de `XXXX` waarden geven aan de dubbelzinnigheid van het jaar. `P3D` Geeft aan dat de periode is drie dagen lang is.

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-day-of-week"></a>Datum bereik resolutie voorbeelden voor dag van week

Het volgende voorbeeld laat zien hoe LUIS gebruikt **datetimeV2** om op te lossen de utterance "Dinsdag donderdag". In dit voorbeeld is de huidige datum 19 juni. LUIS bevat **daterange** waarden voor beide van de datumbereiken die voorafgaan aan en volgt u de huidige datum.

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
## <a name="ambiguous-time"></a>Niet-eenduidige tijd
De matrix met waarden is twee keer elementen als de tijd of bereik niet eenduidig is. Wanneer er een niet-eenduidige tijd, hebben waarden beide het uur. en uur. tijden.

## <a name="time-range-resolution-example"></a>Voorbeeld van de tijd range resolutie

Het volgende voorbeeld laat zien hoe LUIS gebruikt **datetimeV2** om op te lossen de utterance waarvoor een tijdsbereik.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)

DatetimeV2 JSON-antwoord is gewijzigd in de API v3. 

Wijzigingen van API v2:
* de eigenschap `datetimeV2.timex.type` wordt niet meer geretourneerd omdat deze wordt geretourneerd op het bovenliggende niveau, `datetimev2.type`. 
* De naam van de eigenschap `datetimeV2.timex` is gewijzigd in `datetimeV2.value`.

Voor de utterance, `8am on may 2nd 2017`, is de V3-versie van DatetimeV2:

```JSON
{
    "query": "8am on may 2nd 2017",
    "prediction": {
        "normalizedQuery": "8am on may 2nd 2017",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6826963
            }
        },
        "entities": {
            "datetimeV2": [
                {
                    "type": "datetime",
                    "values": [
                        {
                            "timex": "2017-05-02T08",
                            "value": "2017-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    }
}
```

De volgende JSON is met de para meter `verbose` ingesteld op `false`:

```json
{
    "query": "8am on may 2nd 2017",
    "prediction": {
        "normalizedQuery": "8am on may 2nd 2017",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6826963
            }
        },
        "entities": {
            "datetimeV2": [
                {
                    "type": "datetime",
                    "values": [
                        {
                            "timex": "2017-05-02T08",
                            "value": "2017-05-02 08:00:00"
                        }
                    ]
                }
            ],
            "$instance": {
                "datetimeV2": [
                    {
                        "type": "builtin.datetimeV2.datetime",
                        "text": "8am on may 2nd 2017",
                        "startIndex": 0,
                        "length": 19,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```


* * * 

## <a name="deprecated-prebuilt-datetime"></a>Afgeschafte vooraf gedefinieerde datum/tijd

De `datetime` vooraf gedefinieerde entiteit is afgeschaft en vervangen door **datetimeV2**. 

Vervang `datetime` met `datetimeV2` in uw LUIS-app, de volgende stappen uitvoeren:

1. Open de **entiteiten** deelvenster van de LUIS-webinterface. 
2. Verwijder de **datum-/** vooraf gedefinieerde entiteit.
3. Klik op **vooraf gedefinieerde entiteit toevoegen**
4. Selecteer **datetimeV2** en klikt u op **opslaan**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

Meer informatie over de [dimensie](luis-reference-prebuilt-dimension.md), [e](luis-reference-prebuilt-email.md) entiteiten, en [nummer](luis-reference-prebuilt-number.md). 

