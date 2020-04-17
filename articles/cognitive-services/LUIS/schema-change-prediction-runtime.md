---
title: App verlengen tijdens looptijd - LUIS
description: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: c0f9d71f5d89d73d9cdce2a2f646859d8eba3adc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538576"
---
# <a name="extend-app-at-prediction-runtime"></a>App uitbreiden bij voorspellingsruntime

Het schema van de app (modellen en functies) wordt getraind en gepubliceerd naar het voorspellingseindpunt. Dit gepubliceerde model wordt gebruikt op de voorspelling runtime. U nieuwe informatie, samen met de uiting van de gebruiker, doorgeven aan de voorspellingsruntime om de voorspelling te vergroten.

Twee wijzigingen in het voorspellingsschema zijn:
* [Externe entiteiten](#external-entities)
* [Dynamische lijsten](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Externe entiteiten

Externe entiteiten geven uw LUIS-app de mogelijkheid om entiteiten tijdens runtime te identificeren en te labelen, die kunnen worden gebruikt als functies voor bestaande entiteiten. Hiermee u uw eigen afzonderlijke en aangepaste entiteitsextractors gebruiken voordat u query's naar uw voorspellingseindpunt verzendt. Omdat dit gebeurt op het eindpunt van de queryvoorspelling, hoeft u uw model niet opnieuw te trainen en te publiceren.

De client-applicatie verstrekt een eigen entiteit extractor door het beheren van entiteit matching en het bepalen van de locatie binnen de uiting van die overeenkomende entiteit en vervolgens het verzenden van die informatie met de aanvraag.

Externe entiteiten zijn het mechanisme voor het uitbreiden van een entiteitstype terwijl ze nog steeds worden gebruikt als signalen naar andere modellen.

Dit is handig voor een entiteit die alleen gegevens beschikbaar heeft bij queryvoorspellingsruntijd. Voorbeelden van dit soort gegevens zijn voortdurend veranderende gegevens of specifieke per gebruiker. U een LUIS-contactpersoon uitbreiden met externe informatie uit de lijst met contactpersonen van een gebruiker.

Externe entiteiten maken deel uit van de V3-ontwerp-API. Meer informatie over [migreren](luis-migration-api-v3.md) naar deze versie.

### <a name="entity-already-exists-in-app"></a>Entiteit bestaat al in app

De waarde `entityName` van voor de externe entiteit, doorgegeven in de post-instantie eindpuntaanvraag, moet al bestaan in de getrainde en gepubliceerde app op het moment dat de aanvraag wordt ingediend. Het type entiteit doet er niet toe, alle typen worden ondersteund.

### <a name="first-turn-in-conversation"></a>Eerste beurt in gesprek

Overweeg een eerste uiting in een chatbotgesprek waarin een gebruiker de volgende onvolledige informatie invoert:

`Send Hazem a new message`

Het verzoek van de chat bot naar LUIS kan `Hazem` doorgeven in informatie in de POST lichaam over, zodat het direct wordt gekoppeld als een van de contacten van de gebruiker.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Het voorspellingsantwoord bevat die externe entiteit, met alle andere voorspelde entiteiten, omdat deze is gedefinieerd in de aanvraag.

### <a name="second-turn-in-conversation"></a>Tweede beurt in gesprek

De volgende gebruiker uiting in de chat bot maakt gebruik van een meer vage term:

`Send him a calendar reminder for the party.`

In deze draai van het `him` gesprek wordt `Hazem`de utterance gebruikt als verwijzing naar . De conversationele chatbot, in de `him` post-body, kan toewijzen aan `Hazem`de entiteitswaarde die uit de eerste utterance is geëxtraheerd, .

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Het voorspellingsantwoord bevat die externe entiteit, met alle andere voorspelde entiteiten, omdat deze is gedefinieerd in de aanvraag.

### <a name="override-existing-model-predictions"></a>Bestaande modelvoorspellingen overschrijven

De `preferExternalEntities` eigenschap Options geeft aan dat als de gebruiker een externe entiteit verzendt die overlapt met een voorspelde entiteit met dezelfde naam, LUIS de entiteit kiest die is doorgegeven of de entiteit die in het model bestaat.

Denk bijvoorbeeld aan `today I'm free`de query . LUIS `today` detecteert als een datetimeV2 met het volgende antwoord:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Als de gebruiker de externe entiteit verzendt:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Als `preferExternalEntities` de instelling `false`is ingesteld op , retourneert LUIS een antwoord alsof de externe entiteit niet is verzonden.

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Als `preferExternalEntities` de toepassing `true`is ingesteld op , retourneert LUIS een antwoord met de tekst:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Oplossing

De _optionele_ `resolution` eigenschap keert terug in het voorspellingsantwoord, zodat u de metagegevens die aan de externe entiteit zijn gekoppeld, doorgeven en deze vervolgens weer in het antwoord ontvangen.

Het primaire doel is om vooraf gebouwde entiteiten uit te breiden, maar het is niet beperkt tot dat entiteitstype.

De `resolution` eigenschap kan een getal, een tekenreeks, een object of een array zijn:

* "Dallas"
* {"tekst": "waarde"}
* 12345
* [a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Dynamische lijsten

Met dynamische lijsten u een bestaande getrainde en gepubliceerde lijstentiteit uitbreiden, die al in de LUIS-app is opgenomen.

Gebruik deze functie wanneer de waarden van uw lijstentiteit periodiek moeten worden gewijzigd. Met deze functie u een reeds getrainde en gepubliceerde lijstentiteit uitbreiden:

* Op het moment van de queryvoorspelling eindpunt aanvraag.
* Voor één verzoek.

De entiteit van de lijst kan leeg zijn in de LUIS-app, maar moet wel bestaan. De lijstentiteit in de LUIS-app wordt niet gewijzigd, maar de voorspellingsmogelijkheid op het eindpunt wordt uitgebreid tot maximaal 2 lijsten met ongeveer 1.000 items.

### <a name="dynamic-list-json-request-body"></a>Dynamische lijst JSON aanvraag body

Stuur de volgende JSON-instantie om een nieuwe sublijst met synoniemen aan de `LUIS`lijst toe `POST` te voegen en voorspel de lijstentiteit voor de tekst, met de queryvoorspellingsaanvraag:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

Het voorspellingsantwoord bevat die lijstentiteit, met alle andere voorspelde entiteiten, omdat deze is gedefinieerd in de aanvraag.

## <a name="next-steps"></a>Volgende stappen

* [Voorspellingsscore](luis-concept-prediction-score.md)
* [Wijzigingen in API V3](luis-migration-api-v3.md)
