---
title: DatetimeV2 Voorgebouwde entiteiten - LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel heeft datumtijdV2 vooraf gebouwde entiteitsinformatie in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/07/2020
ms.author: diberry
ms.openlocfilehash: 30132983f37323e798efd330f5cc8f15c0a9d2b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270739"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2-voorgebouwde entiteit voor een LUIS-app

De vooraf gebouwde **entiteit datetimeV2** haalt datum- en tijdwaarden uit. Deze waarden worden in een gestandaardiseerde indeling opgelost voor clientprogramma's om te consumeren. Wanneer een utterance een datum of tijd heeft die niet is voltooid, bevat LUIS _zowel waarden uit het verleden als toekomstige waarden_ in het eindpuntantwoord. Omdat deze entiteit al is getraind, hoeft u geen voorbeelduitingen met datumtijdV2 toe te voegen aan de toepassingsintenties.

## <a name="types-of-datetimev2"></a>Typen datumtijdV2
DatetimeV2 wordt beheerd vanuit de [GitHub-repository met recognizers-tekst.](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml)

## <a name="example-json"></a>Voorbeeld JSON

De volgende utterance en de gedeeltelijke JSON-reactie worden hieronder weergegeven.

`8am on may 2nd 2019`

#### <a name="v3-response"></a>[V3-antwoord](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
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
```

#### <a name="v2-response"></a>[V2-antwoord](#tab/1-3)

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

|Naam van eigenschap |Type en beschrijving van de eigenschap|
|---|---|
|Entiteit|**tekenreeks** - Tekst die uit de utterance wordt geëxtraheerd met type datum, tijd, datumbereik of tijdsbereik.|
|type|**tekenreeks** - Een van de [subtypen datumtijdV2](#subtypes-of-datetimev2)
|Startindex|**int** - De index in de utterance waarbij de entiteit begint.|
|Endindex|**int** - De index in de utterance waarde entiteit eindigt.|
|Resolutie|Heeft `values` een array met één, twee of vier [resolutiewaarden.](#values-of-resolution)|
|beëindigen|De eindwaarde van een tijd of datumbereik `value`in dezelfde notatie als . Alleen gebruikt `type` `daterange`als `timerange`wordt , , of`datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>Subtypen van datetimeV2

De **voorgebouwde voorgebouwde entiteit datetimeV2** heeft de volgende subtypen en voorbeelden van elk zijn opgenomen in de volgende tabel:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Waarden van resolutie
* De array heeft één element als de datum of tijd in de utterance volledig is opgegeven en ondubbelzinnig is.
* De array heeft twee elementen als de datumtijdV2-waarde dubbelzinnig is. Ambiguïteit omvat gebrek aan specifiek jaar, tijd of tijdsbereik. Zie [Dubbelzinnige datums](#ambiguous-dates) voor voorbeelden. Als de tijd dubbelzinnig is voor A.M. of P.M., beide waarden zijn opgenomen.
* De array heeft vier elementen als de utterance twee elementen heeft met ambiguïteit. Deze ambiguïteit omvat elementen die:
  * Een datum- of datumbereik dat dubbelzinnig is ten opzichte van het jaar
  * Een tijd- of tijdsbereik dat dubbelzinnig is ten opzichte van A.M. of P.M. Bijvoorbeeld, 3:00 3 april.

Elk element `values` van de array kan de volgende velden hebben:

|Naam van eigenschap|Beschrijving van de eigenschap|
|--|--|
|Timex|tijd, datum of datumbereik, uitgedrukt in TIMEX-indeling dat de [ISO 8601-standaard](https://en.wikipedia.org/wiki/ISO_8601) en de TIMEX3-kenmerken voor annotatie volgt met behulp van de TimeML-taal. Deze annotatie wordt beschreven in de [TIMEX-richtlijnen](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|Mod|term die wordt gebruikt om te `before` `after`beschrijven hoe de waarde moet worden gebruikt, zoals , .|
|type|Het subtype, dat een van de `datetime` `date`volgende `time` `daterange`punten `timerange` `datetimerange`kan `duration` `set`zijn: , , , , , , .|
|waarde|**Optionele.** Een object met datumtijd in de indeling yyyy-MM-dd (datum), HH:mm:ss (tijd) yyyy-MM-dd HH:mm:ss (datum). Als `type` `duration`dat zo is, is de waarde het aantal seconden (duur) <br/> Alleen gebruikt `type` `datetime` als `date` `time`is of , , of 'duur.|

## <a name="valid-date-values"></a>Geldige datumwaarden

De **datumV2** ondersteunt datums tussen de volgende bereiken:

| Min. | Max. |
|----------|-------------|
| 1 januari 1900   | 31 december 2099 |

## <a name="ambiguous-dates"></a>Dubbelzinnige data

Als de datum in het verleden of de toekomst kan zijn, biedt LUIS beide waarden. Een voorbeeld is een utterance die de maand en datum zonder het jaar bevat.

Bijvoorbeeld, gezien de volgende uiting:

`May 2nd`

* Als de datum van vandaag 3 mei 2017 is, biedt LUIS zowel "2017-05-02" als "2018-05-02" als waarden.
* Wanneer de datum van vandaag 1 mei 2017 is, biedt LUIS zowel "2016-05-02" als "2017-05-02" als waarden.

In het volgende voorbeeld ziet u de resolutie van de entiteit "may 2nd". In deze resolutie wordt ervan uitgegaan dat de datum van vandaag een datum is tussen 2 mei 2017 en 1 mei 2018.
Velden `X` met `timex` in het veld zijn delen van de datum die niet expliciet zijn opgegeven in de utterance.

## <a name="date-resolution-example"></a>Voorbeeld van datumresolutie


De volgende utterance en de gedeeltelijke JSON-reactie worden hieronder weergegeven.

`May 2nd`

#### <a name="v3-response"></a>[V3-antwoord](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
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

#### <a name="v2-response"></a>[V2-antwoord](#tab/2-3)

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
* * *

## <a name="date-range-resolution-examples-for-numeric-date"></a>Voorbeelden van datumbereikresolutie voor numerieke datum

De `datetimeV2` entiteit haalt datum- en tijdsbereiken uit. De `start` `end` velden en velden geven het begin en einde van het bereik op. Voor de `May 2nd to May 5th`utterance geeft LUIS **datumbereikwaarden** voor zowel het huidige jaar als het volgende jaar. In `timex` het veld `XXXX` geven de waarden de ambiguïteit van het jaar aan. `P3D`geeft aan dat de periode drie dagen lang is.

De volgende utterance en de gedeeltelijke JSON-reactie worden hieronder weergegeven.

`May 2nd to May 5th`

#### <a name="v3-response"></a>[V3-antwoord](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
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

#### <a name="v2-response"></a>[V2-antwoord](#tab/3-3)

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
* * *

## <a name="date-range-resolution-examples-for-day-of-week"></a>Voorbeelden van datumbereikresolutie voor dag van de week

In het volgende voorbeeld ziet u hoe LUIS `Tuesday to Thursday` **datetimeV2** gebruikt om de utterance op te lossen. In dit voorbeeld is de huidige datum 19 juni. LUIS bevat **datumbereikwaarden** voor beide datumbereiken die voorafgaan aan en de huidige datum volgen.

De volgende utterance en de gedeeltelijke JSON-reactie worden hieronder weergegeven.

`Tuesday to Thursday`

#### <a name="v3-response"></a>[V3-antwoord](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
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
```

#### <a name="v2-response"></a>[V2-antwoord](#tab/4-3)

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
* * *

## <a name="ambiguous-time"></a>Dubbelzinnige tijd
De array waarden heeft twee tijdselementen als de tijd of het tijdsbereik dubbelzinnig is. Als er een dubbelzinnige tijd is, hebben waarden zowel de A.M. en P.M. Keer.

## <a name="time-range-resolution-example"></a>Voorbeeld van het oplossen van tijdsbereik

DatetimeV2 JSON-respons is gewijzigd in de API V3. In het volgende voorbeeld ziet u hoe LUIS **datetimeV2** gebruikt om de utterance met een tijdsbereik op te lossen.

Wijzigingen ten opzichte van API V2:
* `datetimeV2.timex.type`eigenschap wordt niet meer geretourneerd omdat deze `datetimev2.type`wordt geretourneerd op bovenliggend niveau, .
* Het `datetimeV2.value` pand is omgedoopt `datetimeV2.timex`tot .

De volgende utterance en de gedeeltelijke JSON-reactie worden hieronder weergegeven.

`from 6pm to 7pm`

#### <a name="v3-response"></a>[V3-antwoord](#tab/5-1)

De volgende JSON `verbose` is met `false`de parameter ingesteld op:

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/5-2)

De volgende JSON `verbose` is met `true`de parameter ingesteld op:

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
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
#### <a name="v2-response"></a>[V2-antwoord](#tab/5-3)

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

* * *

## <a name="time-resolution-example"></a>Voorbeeld van tijdoplossing

De volgende utterance en de gedeeltelijke JSON-reactie worden hieronder weergegeven.

`8am`

#### <a name="v3-response"></a>[V3-antwoord](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 verbose reactie](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
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
#### <a name="v2-response"></a>[V2-antwoord](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * *

## <a name="deprecated-prebuilt-datetime"></a>Afgeschafte vooraf gebouwde datumtijd

De `datetime` voorgebouwde entiteit wordt afgeschaft en vervangen door **datetimeV2**.

Voer `datetime` de `datetimeV2` volgende stappen uit om te vervangen door in uw LUIS-app:

1. Open het deelvenster **Entiteiten** van de LUIS-webinterface.
2. Verwijder de vooraf gebouwde **entiteit van datumtijd.**
3. Klik **op Vooraf opgebouwde entiteit toevoegen**
4. Selecteer **datetimeV2** en klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Meer informatie over de [dimensie,](luis-reference-prebuilt-dimension.md) [e-mailentiteiten](luis-reference-prebuilt-email.md) en [nummer](luis-reference-prebuilt-number.md).

