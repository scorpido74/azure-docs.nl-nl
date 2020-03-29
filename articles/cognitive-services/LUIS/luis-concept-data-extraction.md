---
title: Gegevensextractie - LUIS
description: Gegevens extraheren uit utterancetekst met intents en entiteiten. Ontdek wat voor soort gegevens kunnen worden geëxtraheerd uit Language Understanding (LUIS).
author: diberry
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 1c1a744c06e5347625fb96518bd809481ee797e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221082"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Gegevens uit utterancetekst met intents en entiteiten extraheren
LUIS geeft u de mogelijkheid om informatie te krijgen van de natuurlijke taaluitingen van een gebruiker. De informatie wordt geëxtraheerd op een manier die kan worden gebruikt door een programma, toepassing of chatbot om actie te ondernemen. In de volgende secties leest u welke gegevens worden geretourneerd van intenties en entiteiten met voorbeelden van JSON.

De moeilijkste gegevens te extraheren is de machine-geleerde gegevens, omdat het niet een exacte tekst overeenkomen. Gegevensextractie van de door de machine geleerde [entiteiten](luis-concept-entity-types.md) moet deel uitmaken van de [ontwerpcyclus](luis-concept-app-iteration.md) totdat u er zeker van bent dat u de gegevens ontvangt die u verwacht.

## <a name="data-location-and-key-usage"></a>Gegevenslocatie en sleutelgebruik
LUIS verstrekt de gegevens van het gepubliceerde [eindpunt](luis-glossary.md#endpoint). Het **HTTPS-verzoek** (POST of GET) bevat de utterance en enkele optionele configuraties, zoals faserings- of productieomgevingen.

#### <a name="v2-prediction-endpoint-request"></a>[V2 voorspelling eindpunt aanvraag](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-request"></a>[V3 voorspelling eindpunt aanvraag](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

* * *

De `appID` pagina is beschikbaar op de pagina **Instellingen** van uw `/apps/`LUIS-app en een deel van de URL (na ) wanneer u die LUIS-app bewerkt. Dit `subscription-key` is de eindpuntsleutel die wordt gebruikt voor het opvragen van uw app. Hoewel u uw gratis ontwerp-/startsleutel gebruiken terwijl u LUIS leert, is het belangrijk om de eindpuntsleutel te wijzigen in een sleutel die uw [verwachte LUIS-gebruik](luis-boundaries.md#key-limits)ondersteunt. Het `timezoneOffset` apparaat duurt minuten.

Het **HTTPS-antwoord** bevat alle intenties en entiteitsinformatie die LUIS kan bepalen op basis van het huidige gepubliceerde model van het faserings- of productieeindpunt. De URL van het eindpunt is te vinden op de [LUIS-website,](luis-reference-regions.md) in de sectie **Beheren,** op de pagina **Sleutels en eindpunten.**

## <a name="data-from-intents"></a>Gegevens uit intenties
De primaire gegevens zijn de **intentienaam**topscore . Het eindpunt antwoord is:

#### <a name="v2-prediction-endpoint-response"></a>[V2 voorspelling eindpuntrespons](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 voorspelling eindpuntrespons](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

* * *

|Gegevensobject|Gegevenstype|Locatie van gegevens|Waarde|
|--|--|--|--|
|Intentie|Tekenreeks|topScoringIntent.intent|"GetStoreInfo"|

Als uw chatbot of LUIS-calling-app een beslissing neemt op basis van meer dan één intentiescore, geeft u alle scores van de intenties terug.


#### <a name="v2-prediction-endpoint-response"></a>[V2 voorspelling eindpuntrespons](#tab/V2)

Stel de parameter `verbose=true`querytekenreeks in. Het eindpunt antwoord is:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 voorspelling eindpuntrespons](#tab/V3)

Stel de parameter `show-all-intents=true`querytekenreeks in. Het eindpunt antwoord is:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

* * *

De intenties worden geordend van de hoogste naar de laagste score.

|Gegevensobject|Gegevenstype|Locatie van gegevens|Waarde|Score|
|--|--|--|--|:--|
|Intentie|Tekenreeks|intentie[0].intent|"GetStoreInfo"|0.984749258|
|Intentie|Tekenreeks|intentie[1].intentie|"Geen"|0.0168218873|

Als u vooraf gebouwde domeinen toevoegt, geeft de `Utilties` `Communication` intentienaam het domein aan, zoals of evenals de intentie:

#### <a name="v2-prediction-endpoint-response"></a>[V2 voorspelling eindpuntrespons](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 voorspelling eindpuntrespons](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

* * *

|Domain|Gegevensobject|Gegevenstype|Locatie van gegevens|Waarde|
|--|--|--|--|--|
|Hulpprogramma's|Intentie|Tekenreeks|intentie[0].intent|"<b>Nutsbedrijven</b>. ShowNext"|
|Communicatie|Intentie|Tekenreeks|intentie[1].intentie|<b>Communicatie</b>. StartOver"|
||Intentie|Tekenreeks|intentie[2].intent|"Geen"|


## <a name="data-from-entities"></a>Gegevens van entiteiten
De meeste chatbots en applicaties hebben meer nodig dan de intentienaam. Deze aanvullende, optionele gegevens zijn afkomstig van entiteiten die in de utterance zijn ontdekt. Elk type entiteit retourneert verschillende informatie over de overeenkomst.

Eén woord of woordgroep in een utterance kan overeenkomen met meer dan één entiteit. In dat geval wordt elke overeenkomende entiteit geretourneerd met zijn score.

Alle entiteiten worden geretourneerd in de **array entiteiten** van het antwoord vanaf het eindpunt:

#### <a name="v2-prediction-endpoint-response"></a>[V2 voorspelling eindpuntrespons](#tab/V2)

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 voorspelling eindpuntrespons](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

* * *

## <a name="tokenized-entity-returned"></a>Tokenized entiteit geretourneerd

Bekijk de [tokenondersteuning](luis-language-support.md#tokenization) in LUIS.

## <a name="simple-entity-data"></a>Eenvoudige entiteitsgegevens

Een [eenvoudige entiteit](reference-entity-simple.md) is een door de machine geleerde waarde. Het kan een woord of zin zijn.

## <a name="composite-entity-data"></a>Samengestelde entiteitsgegevens

Een [samengestelde entiteit](reference-entity-composite.md) bestaat uit andere entiteiten, zoals vooraf gebouwde entiteiten, eenvoudige, reguliere expressie en lijstentiteiten. De afzonderlijke entiteiten vormen een hele entiteit.

## <a name="list-entity-data"></a>Gegevens van entiteiten weergeven

[Lijstentiteiten](reference-entity-list.md) vertegenwoordigen een vaste, gesloten reeks verwante woorden, samen met hun synoniemen. LUIS ontdekt geen extra waarden voor lijstentiteiten. Gebruik de functie **Aanbevelen** om suggesties voor nieuwe woorden te bekijken op basis van de huidige lijst. Als er meer dan één lijstentiteit met dezelfde waarde is, wordt elke entiteit geretourneerd in de eindpuntquery.

## <a name="prebuilt-entity-data"></a>Vooraf gebouwde entiteitsgegevens
[Vooraf gebouwde](luis-concept-entity-types.md) entiteiten worden ontdekt op basis van een reguliere expressie overeenkomst met behulp van de open-source [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) project. Vooraf gebouwde entiteiten worden geretourneerd in de array entiteiten `builtin::`en gebruiken de typenaam die vooraf is gekoppeld aan . De volgende tekst is een voorbeeldutterance met de geretourneerde vooraf gebouwde entiteiten:

`Dec 5th send to +1 360-555-1212`

#### <a name="v2-prediction-endpoint-response"></a>[V2 voorspelling eindpuntrespons](#tab/V2)

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 voorspelling eindpuntrespons](#tab/V3)

Zonder de parameter `verbose=true`querystring:

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ]
}
```

Met de parameter `verbose=true`querystring:

```json

"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "Dec 5th",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "number": [
            {
                "type": "builtin.number",
                "text": "1 360",
                "startIndex": 17,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "555",
                "startIndex": 23,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "1212",
                "startIndex": 27,
                "length": 4,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 360-555-1212",
                "startIndex": 17,
                "length": 14,
                "score": 1.0,
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

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

* * *
## <a name="regular-expression-entity-data"></a>Entiteitsgegevens voor reguliere expressies

Een [entiteit met reguliere expressie](reference-entity-regular-expression.md) haalt een entiteit uit op basis van een regelmatig expressiepatroon dat u opgeeft.

## <a name="extracting-names"></a>Namen extraheren
Het krijgen van namen uit een uiting is moeilijk omdat een naam kan bijna elke combinatie van letters en woorden. Afhankelijk van het type naam dat u aan het extraheren bent, hebt u verschillende opties. De volgende suggesties zijn geen regels, maar meer richtlijnen.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Vooraf gebouwde personname- en geografieV2-entiteiten toevoegen

[PersonName-](luis-reference-prebuilt-person.md) en [GeographyV2-entiteiten](luis-reference-prebuilt-geographyV2.md) zijn beschikbaar in sommige [taalculturen.](luis-reference-prebuilt-entities.md)

### <a name="names-of-people"></a>Namen van mensen

De naam van mensen kan een klein formaat hebben, afhankelijk van taal en cultuur. Gebruik een vooraf gebouwde **[personName-entiteit](luis-reference-prebuilt-person.md)** of een **[eenvoudige entiteit](luis-concept-entity-types.md#simple-entity)** met [rollen](luis-concept-roles.md) met voor- en achternaam.

Als u de eenvoudige entiteit gebruikt, moet u voorbeelden geven die de voor- en achternaam gebruiken in verschillende delen van de utterance, in uitingen van verschillende lengtes en uitingen in alle intenties, waaronder de intentie Geen. [Controleer](luis-how-to-review-endoint-utt.md) regelmatig eindpuntuitingen om namen te labelen die niet correct zijn voorspeld.

### <a name="names-of-places"></a>Namen van plaatsen

Locatienamen zijn ingesteld en bekend, zoals steden, provincies, staten, provincies en landen/regio's. Gebruik de vooraf gebouwde entiteit **[geografieV2](luis-reference-prebuilt-geographyv2.md)** om locatiegegevens te extraheren.

### <a name="new-and-emerging-names"></a>Nieuwe en opkomende namen

Sommige apps moeten nieuwe en opkomende namen kunnen vinden, zoals producten of bedrijven. Dit soort namen zijn de moeilijkste vorm van gegevensextractie. Begin met een **[eenvoudige entiteit](luis-concept-entity-types.md#simple-entity)** en voeg een [woordenlijst](luis-concept-feature.md)toe. [Controleer](luis-how-to-review-endoint-utt.md) regelmatig eindpuntuitingen om namen te labelen die niet correct zijn voorspeld.

## <a name="pattern-roles-data"></a>Gegevens over patroonrollen
Rollen zijn contextuele verschillen tussen entiteiten.


#### <a name="v2-prediction-endpoint-response"></a>[V2 voorspelling eindpuntrespons](#tab/V2)

De naam `Location`van de `Origin` entiteit `Destination`is, met twee rollen, en .

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Employee",
    "startIndex": 5,
    "endIndex": 13,
    "score": 0.922820568,
    "role": ""
  },
  {
    "entity": "seattle",
    "type": "Location",
    "startIndex": 20,
    "endIndex": 26,
    "score": 0.948008537,
    "role": "Origin"
  },
  {
    "entity": "redmond",
    "type": "Location",
    "startIndex": 31,
    "endIndex": 37,
    "score": 0.7047979,
    "role": "Destination"
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 voorspelling eindpuntrespons](#tab/V3)

In V3 is de **rolnaam** de primaire naam van het object.

De naam `Location`van de `Origin` entiteit `Destination`is, met twee rollen, en .

Zonder de parameter `verbose=true`querystring:

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "Origin": [
        "seattle"
    ],
    "Destination": [
        "redmond"
    ]
}
```

Met de parameter `verbose=true`querystring:

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "LocationOrigin": [
        "seattle"
    ],
    "LocationDestination": [
        "redmond"
    ],
    "$instance": {
        "Employee": [
            {
                "type": "Employee",
                "text": "bob jones",
                "startIndex": 5,
                "length": 9,
                "score": 0.982873261,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Origin": [
            {
                "role": "Origin",
                "type": "Location",
                "text": "seattle",
                "startIndex": 20,
                "length": 7,
                "score": 0.9913306,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "redmond",
                "startIndex": 31,
                "length": 7,
                "score": 0.898179531,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]

}
```

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

* * *

## <a name="patternany-entity-data"></a>Patroon.alle entiteitsgegevens

[Pattern.any](reference-entity-pattern-any.md) is een tijdelijke aanduiding met variabele lengte die alleen wordt gebruikt in de sjabloonutterance van een patroon om aan te geven waar de entiteit begint en eindigt.

## <a name="sentiment-analysis"></a>Sentimentanalyse
Als sentimentanalyse is geconfigureerd, bevat de LUIS-json-respons sentimentanalyse. Meer informatie over sentimentanalyse vindt u in de [text analytics-documentatie.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)

### <a name="sentiment-data"></a>Sentimentgegevens
Sentimentgegevens zijn een score tussen 1 en 0 die het positieve (dichter bij 1) of negatieve (dichter bij 0) sentiment van de gegevens aangeven.

Wanneer cultuur `en-us`is, is het antwoord:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Voor alle andere culturen is het antwoord:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Gegevens van de sleutelzinextractieentiteit
De sleutelzinextractieentiteit retourneert de hoofdzinnen in de utterance, geleverd door [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).


#### <a name="v2-prediction-endpoint-response"></a>[V2 voorspelling eindpuntrespons](#tab/V2)

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 voorspelling eindpuntrespons](#tab/V3)

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Zonder de parameter `verbose=true`querystring:

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ]
}
```

Met de parameter `verbose=true`querystring:

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "map of places",
                "startIndex": 11,
                "length": 13,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "beautiful views",
                "startIndex": 30,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "favorite trail",
                "startIndex": 51,
                "length": 14,
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

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

* * *


## <a name="data-matching-multiple-entities"></a>Gegevens die overeenkomen met meerdere entiteiten

LUIS retourneert alle entiteiten die in de utterance zijn gedetecteerd. Als gevolg hiervan moet uw chatbot mogelijk een beslissing nemen op basis van de resultaten. Een utterance kan veel entiteiten in een utterance hebben:

`book me 2 adult business tickets to paris tomorrow on air france`

Het LUIS-eindpunt kan dezelfde gegevens in verschillende entiteiten ontdekken.

#### <a name="v2-prediction-endpoint-response"></a>[V2 voorspelling eindpuntrespons](#tab/V2)

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 voorspelling eindpuntrespons](#tab/V3)

Zonder `verbose=true` als querystring parameter.

```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ]
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ]
}
```

Met `verbose=true` als querystringparameter.


```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 8,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "PassengerCategory": [
                    {
                        "type": "PassengerCategory",
                        "text": "adult",
                        "startIndex": 10,
                        "length": 5,
                        "score": 0.9503733,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "TravelClass": [
                    {
                        "type": "TravelClass",
                        "text": "business",
                        "startIndex": 16,
                        "length": 8,
                        "score": 0.950095,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ],
    "$instance": {
        "TicketsOrder": [
            {
                "type": "TicketsOrder",
                "text": "2 adult business",
                "startIndex": 8,
                "length": 16,
                "score": 0.942183256,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Location::LocationTo": [
            {
                "type": "Location::LocationTo",
                "text": "paris",
                "startIndex": 36,
                "length": 5,
                "score": 0.9905354,
                "modelTypeId": 3,
                "modelType": "Hierarchical Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "tomorrow",
                "startIndex": 42,
                "length": 8,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Airline": [
            {
                "type": "Airline",
                "text": "air france",
                "startIndex": 54,
                "length": 10,
                "score": 0.9455415,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

* * *

## <a name="data-matching-multiple-list-entities"></a>Gegevens die overeenkomen met meerdere lijstentiteiten

Als een woord of woordgroep overeenkomt met meer dan één lijstentiteit, retourneert de eindpuntquery elke entiteit van de lijst.

Voor de `when is the best time to go to red rock?`query en de `red` app heeft het woord in meer dan één lijst, herkent LUIS alle entiteiten en retourneert een array met entiteiten als onderdeel van het JSON-eindpuntantwoord:

#### <a name="v2-prediction-endpoint-response"></a>[V2 voorspelling eindpuntrespons](#tab/V2)

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```



#### <a name="v3-prediction-endpoint-response"></a>[V3 voorspelling eindpuntrespons](#tab/V3)

Zonder `verbose=true` in de querytekenreeks:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ]
        }
    }
}
```


Met `verbose=true` in de querytekenreeks:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ],
            "$instance": {
                "Colors": [
                    {
                        "type": "Colors",
                        "text": "red",
                        "startIndex": 31,
                        "length": 3,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "Cities": [
                    {
                        "type": "Cities",
                        "text": "red rock",
                        "startIndex": 31,
                        "length": 8,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
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

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

* * *

## <a name="next-steps"></a>Volgende stappen

Zie [Entiteiten toevoegen](luis-how-to-add-entities.md) voor meer informatie over het toevoegen van entiteiten aan uw LUIS-app.
