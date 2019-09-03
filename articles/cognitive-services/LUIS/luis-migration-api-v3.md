---
title: Versie 2 tot v3 API-migratie
titleSuffix: Azure Cognitive Services
description: De Api's van versie 3-eind punten zijn gewijzigd. Gebruik deze hand leiding voor informatie over het migreren naar versie 3-eindpunt-Api's.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: diberry
ms.openlocfilehash: 5b0516f3d610c0a518d6afc461dddebfb68a7c5d
ms.sourcegitcommit: ac29357a47cc05afdf0f84834de5277598f4d87c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70213513"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>Preview: Migreren naar API versie 3. x voor LUIS-apps

De Api's voor query voorspelling-eind punten zijn gewijzigd. Gebruik deze hand leiding voor informatie over het migreren naar versie 3-eindpunt-Api's. 

Deze v3 API biedt de volgende nieuwe functies, waaronder aanzienlijke wijzigingen in JSON-aanvragen en/of reacties: 

* [Externe entiteiten](#external-entities-passed-in-at-prediction-time)
* [Dynamische lijsten](#dynamic-lists-passed-in-at-prediction-time)
* [JSON-wijzigingen van entiteit vooraf gebouwd](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

De [aanvraag](#request-changes) en het [antwoord](#response-changes) op de query Voorspellings eindpunt bevatten belang rijke wijzigingen ter ondersteuning van de nieuwe functies die hierboven worden vermeld, waaronder de volgende:

* [Wijzigingen in het reactie object](#top-level-json-changes)
* [Namen van entiteits rollen in plaats van naam van entiteit](#entity-role-name-instead-of-entity-name)
* [Eigenschappen voor het markeren van entiteiten in uitingen](#marking-placement-of-entities-in-utterances)

De volgende LUIS-functies worden **niet ondersteund** in de V3 API:

* Bing Spellingcontrole V7

[Referentie documentatie](https://aka.ms/luis-api-v3) is beschikbaar voor v3.

## <a name="endpoint-url-changes-by-slot-name"></a>Eind punt-URL wordt gewijzigd per sleuf naam

De indeling van de HTTP-aanroep van het v3-eind punt is gewijzigd.

|METHODE|URL|
|--|--|
|GET|https://<b>{Region}</b>. API.Cognitive.Microsoft.com/Luis/<b>v 3.0-Preview</b>/apps/<b>{App-ID}</b>/slots/<b>{sleuf-name}</b>/predict? query =<b>{query}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|||

Geldige waarden voor sleuven:

* `production`
* `staging`

## <a name="endpoint-url-changes-by-version-id"></a>Eind punt-URL wordt gewijzigd door versie-ID

Als u een query wilt uitvoeren op versie, moet u eerst [via API publiceren](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) met de `"directVersionPublish":true`. Zoek het eind punt naar de versie-ID in plaats van de naam van de sleuf.


|METHODE|URL|
|--|--|
|GET|https://<b>{Region}</b>. API.Cognitive.Microsoft.com/Luis/<b>v 3.0-Preview</b>/apps/<b>{App-ID}</b>/versions/<b>{versie-id}</b>/predict? query =<b>{query}</b>|
|POST|https://<b>{Region}</b>. API.Cognitive.Microsoft.com/Luis/<b>v 3.0: Preview</b>/apps/<b>{App-ID}</b>/versions/<b>{versie-id}</b>/predict|
|||

## <a name="prebuilt-entities-with-new-json"></a>Vooraf gemaakte entiteiten met nieuwe JSON

De wijzigingen in het v3-antwoord object bevatten [vooraf gemaakte entiteiten](luis-reference-prebuilt-entities.md). 

## <a name="request-changes"></a>Wijzigingen aanvragen 

### <a name="query-string-parameters"></a>Query reeks parameters

De V3 API heeft verschillende query reeks parameters.

|Parameter naam|type|Version|Standaard|Doel|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|Sla de query op in het logboek bestand.| 
|`query`|string|Alleen v3|Geen standaard-dit is vereist in de GET-aanvraag|**In v2**bevindt de utterance die moet worden voor speld `q` in de para meter. <br><br>**In v3**wordt de functionaliteit door gegeven in de `query` para meter.|
|`show-all-intents`|boolean|Alleen v3|false|Alle intenties retour neren met de bijbehorende Score in het object voor spellingen. Intenties worden geretourneerd als objecten in een bovenliggend `intents` object. Zo kunt u programmatisch toegang krijgen zonder dat u de bedoeling in een matrix hoeft te `prediction.intents.give`vinden:. In v2 zijn deze geretourneerd in een matrix. |
|`verbose`|boolean|V2 & V3|false|**In v2**zijn alle voorspelde intenten geretourneerd als deze zijn ingesteld op True. Als u alle voorspelde intenties nodig hebt, gebruikt u de V3 `show-all-intents`-para meter van.<br><br>**In v3**biedt deze para meter alleen details van entiteits-meta gegevens van de voor spelling van de entiteit.  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>De JSON-hoofd tekst van de query voorspelling `POST` voor de aanvraag

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "overridePredictions": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Eigenschap|type|Version|Standaard|Doel|
|--|--|--|--|--|
|`dynamicLists`|array|Alleen v3|Niet vereist.|Met [dynamische lijsten](#dynamic-lists-passed-in-at-prediction-time) kunt u een bestaande getrainde en gepubliceerde lijst entiteit uitbreiden, al in de Luis-app.|
|`externalEntities`|array|Alleen v3|Niet vereist.|[Externe entiteiten](#external-entities-passed-in-at-prediction-time) bieden uw Luis-app de mogelijkheid om entiteiten tijdens runtime te identificeren en te labelen, die kunnen worden gebruikt als functies voor bestaande entiteiten. |
|`options.datetimeReference`|string|Alleen v3|Geen standaard waarde|Wordt gebruikt om de [datetimeV2-offset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)te bepalen. De indeling voor de datetimeReference is [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.overridePredictions`|boolean|Alleen v3|false|Hiermee wordt aangegeven of de externe entiteit van de gebruiker [(met dezelfde naam als bestaande entiteit)](#override-existing-model-predictions) wordt gebruikt of dat de bestaande entiteit in het model wordt gebruikt voor de voor spelling. |
|`query`|string|Alleen v3|Vereist.|**In v2**bevindt de utterance die moet worden voor speld `q` in de para meter. <br><br>**In v3**wordt de functionaliteit door gegeven in de `query` para meter.|



## <a name="response-changes"></a>Reactie wijzigingen

De JSON van het query-antwoord is gewijzigd zodat meer programmatische toegang mogelijk is tot de gegevens die het vaakst worden gebruikt. 

### <a name="top-level-json-changes"></a>JSON-wijzigingen op het hoogste niveau

De bovenste JSON-eigenschappen voor v2 zijn, `verbose` wanneer is ingesteld op True, waarmee alle intents en hun scores in `intents` de eigenschap worden geretourneerd:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

De bovenste JSON-eigenschappen voor v3 zijn:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

<!--
The `alteredQuery` contains spelling corrections. This corresponds to the V2 API property `alteredQuery`.  
-->

Het `intents` object is een niet-geordende lijst. Ga er niet van uit dat het eerste `intents` onderliggende item in `topIntent`de overeenkomt met de. Gebruik in plaats daarvan `topIntent` de waarde om de score te vinden:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

De wijzigingen in het JSON-schema van het antwoord zijn toegestaan voor:

* Maak onderscheid tussen de oorspronkelijke utterance `query`,, en het resultaat van de `prediction`voor spelling, niet.
* Eenvoudiger programmatische toegang tot voorspelde gegevens. In plaats van te inventariseren via een matrix in v2, hebt u toegang tot waarden op **naam** voor zowel de intenties als de entiteiten. Voor voorspelde entiteits rollen wordt de rolnaam geretourneerd, omdat deze uniek is voor de hele app.
* Gegevens typen, indien bepaald, worden in acht genomen. Numerieke tekens worden niet langer geretourneerd als teken reeksen.
* Onderscheid tussen informatie over de voor spellingen van de eerste prioriteit en aanvullende meta `$instance` gegevens, geretourneerd in het object. 

### <a name="access-instance-for-entity-metadata"></a>Toegang `$instance` voor entiteits-meta gegevens

Als u meta gegevens van de entiteit nodig hebt, moet de query `verbose=true` reeks de vlag gebruiken en het antwoord bevat de `$instance` meta gegevens in het object. Voor beelden worden weer gegeven in de JSON-antwoorden in de volgende secties.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Elke voorspelde entiteit wordt weer gegeven als een matrix

Het `prediction.entities.<entity-name>` object bevat een matrix omdat elke entiteit meermaals kan worden voorspeld in de utterance. 

### <a name="list-entity-prediction-changes"></a>Wijzigingen in de voor spelling van een lijst entiteit

De JSON voor de voor spelling van een lijst entiteit is gewijzigd in een matrix met matrices:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Elke Interior-matrix correspondeert met tekst in de utterance. Het object Interior is een matrix omdat dezelfde tekst in meer dan één sublijst van een lijst entiteit kan worden weer gegeven. 

Bij het toewijzen van `entities` een object aan `$instance` het object, wordt de volg orde van de objecten behouden voor de voor spellingen van de lijst entiteit.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>De rolnaam van de entiteit in plaats van de naam van de entiteit 

In v2 heeft de `entities` matrix alle voorspelde entiteiten geretourneerd met de naam van de entiteit als de unieke id. In v3 geldt dat als de entiteit rollen gebruikt en de voor spelling voor een entiteits functie is, de primaire id de rolnaam is. Dit is mogelijk omdat de namen van entiteits rollen uniek moeten zijn voor de hele app, met inbegrip van andere model namen (intentie, entiteit).

In het volgende voor beeld: overweeg een utterance die de tekst bevat `Yellow Bird Lane`,. Deze tekst wordt voor speld als aangepaste `Location` entiteits rollen van. `Destination`

|Utterance tekst|Naam van de entiteit|Rolnaam|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

In v2 wordt de entiteit geïdentificeerd door de naam van de _entiteit_ met de rol als eigenschap van het object:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

In v3 wordt naar de entiteit verwezen door de rol van de _entiteit_, als de voor spelling voor de rol is:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

In v3 heeft hetzelfde resultaat als de `verbose` markering voor het retour neren van meta gegevens van entiteit:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>Externe entiteiten die zijn door gegeven tijdens de Voorspellings tijd

Externe entiteiten bieden uw LUIS-app de mogelijkheid om entiteiten tijdens runtime te identificeren en te labelen, die kunnen worden gebruikt als functies voor bestaande entiteiten. Zo kunt u uw eigen afzonderlijke en aangepaste entiteits-extracten gebruiken voordat u query's naar uw Voorspellings eindpunt verzendt. Omdat dit op het query Voorspellings eindpunt wordt gedaan, hoeft u uw model niet opnieuw te trainen en te publiceren.

De client toepassing levert een eigen entiteits extractie door het beheren van entiteits overeenkomsten en het bepalen van de locatie binnen de utterance van die overeenkomende entiteit en vervolgens die informatie met de aanvraag te verzenden. 

Externe entiteiten zijn het mechanisme voor het uitbreiden van elk type entiteit, terwijl ze nog steeds worden gebruikt als signalen voor andere modellen, zoals rollen, samen stelling en andere.

Dit is handig voor een entiteit met gegevens die alleen beschikbaar zijn voor de runtime van de Voorspellings functie voor query's. Voor beelden van dit type gegevens zijn voortdurend veranderende gegevens of specifieke per gebruiker. U kunt een LUIS-contact persoon uitbreiden met externe informatie uit de lijst met contact personen van een gebruiker. 

### <a name="entity-already-exists-in-app"></a>De entiteit bestaat al in de app

De waarde van `entityName` voor de externe entiteit, door gegeven in de hoofd tekst van het eindpunt verzoek, moet al aanwezig zijn in de getrainde en gepubliceerde app op het moment dat de aanvraag wordt ingediend. Het entiteits type is niet van belang, alle typen worden ondersteund.

### <a name="first-turn-in-conversation"></a>Eerst gesprek inschakelen

Bekijk een eerste utterance in een chat-bot waarbij een gebruiker de volgende onvolledige gegevens invoert:

`Send Hazem a new message`

De aanvraag van de chat-bot naar Luis kan informatie geven in de hoofd tekst `Hazem` , zodat deze direct overeenkomt met de contact personen van de gebruiker.

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

Het Voorspellings antwoord bevat die externe entiteit, met alle andere voorspelde entiteiten, omdat deze is gedefinieerd in de aanvraag.  

### <a name="second-turn-in-conversation"></a>Tweede gesprek inschakelen

De volgende gebruiker utterance in de chat-bot maakt gebruik van een meer vague-term:

`Send him a calendar reminder for the party.`

In de vorige utterance wordt de utterance gebruikt `him` als verwijzing naar. `Hazem` De bot van de conversatie in de hoofd tekst kan worden toegewezen `him` aan de entiteit waarde die is geëxtraheerd uit de eerste utterance `Hazem`,.

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

Het Voorspellings antwoord bevat die externe entiteit, met alle andere voorspelde entiteiten, omdat deze is gedefinieerd in de aanvraag.  

### <a name="override-existing-model-predictions"></a>Bestaande model voorspellingen onderdrukken

De `overridePredictions` eigenschap Options geeft aan dat als de gebruiker een externe entiteit verzendt die overlapt met een voorspelde entiteit met dezelfde naam, Luis kiest voor de entiteit die is door gegeven of de entiteit die in het model is opgenomen. 

Denk bijvoorbeeld aan de query `today I'm free`. Luis detecteert `today` als een datetimeV2 met het volgende antwoord:

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

Als is ingesteld op `false`, retourneert Luis een antwoord alsof de externe entiteit niet is verzonden. `overridePredictions` 

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

Als is `overridePredictions` ingesteld op `true`, Luis retourneert een antwoord met de volgende instellingen:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Oplossing

De _optionele_ `resolution` eigenschap retourneert in het Voorspellings antwoord, zodat u de meta gegevens kunt door geven die aan de externe entiteit zijn gekoppeld. Vervolgens ontvangt u deze in het antwoord. 

Het belangrijkste doel is om vooraf gemaakte entiteiten uit te breiden, maar dit is niet beperkt tot dat entiteits type. 

De `resolution` eigenschap kan een getal, een teken reeks, een object of een matrix zijn:

* Filiaal
* {"tekst": "waarde"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Dynamische lijsten die zijn door gegeven tijdens de Voorspellings tijd

Met dynamische lijsten kunt u een bestaande getrainde en gepubliceerde lijst entiteit uitbreiden, al in de LUIS-app. 

Gebruik deze functie wanneer de waarden van de lijst entiteit periodiek moeten worden gewijzigd. Met deze functie kunt u een al getrainde en gepubliceerde lijst entiteit uitbreiden:

* Op het moment van de aanvraag voor het uitvoeren van een query Voorspellings eindpunt.
* Voor een enkele aanvraag.

De lijst entiteit kan leeg zijn in de LUIS-app, maar deze moet bestaan. De entiteit lijst in de LUIS-app is niet gewijzigd, maar de Voorspellings mogelijkheid op het eind punt wordt uitgebreid tot twee lijsten met ongeveer 1.000 items.

### <a name="dynamic-list-json-request-body"></a>Hoofd tekst JSON-aanvraag van dynamische lijst

Verzend in de volgende JSON-hoofd tekst om een nieuwe sublijst met synoniemen toe te voegen aan de lijst, en voor te voors pellen `POST` dat de lijst entiteit voor de tekst wordt `LUIS`weer geven, met de aanvraag voor de voor spelling van query's:

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

De Voorspellings reactie omvat die lijst entiteit, met alle andere voorspelde entiteiten, omdat deze is gedefinieerd in de aanvraag. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>Time zone offset is gewijzigd in datetimeReference

**In v2**wordt de `timezoneOffset` [para meter](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) in de Voorspellings aanvraag verzonden als een query reeks parameter, ongeacht of de aanvraag wordt verzonden als een Get-of post-aanvraag. 

**In v3**wordt dezelfde functionaliteit gegeven als de para meter post Body, `datetimeReference`. 

## <a name="marking-placement-of-entities-in-utterances"></a>Plaatsing van entiteiten in uitingen markeren

**In v2**werd een entiteit in een utterance gemarkeerd met de `startIndex` and. `endIndex` 

**In v3**wordt de entiteit gemarkeerd met `startIndex` en. `entityLength`

## <a name="deprecation"></a>Afschaffing 

De v2 API wordt niet afgeschaft gedurende ten minste negen maanden na de preview-versie van v3. 

## <a name="next-steps"></a>Volgende stappen

Gebruik de V3 API-documentatie om bestaande REST-aanroepen bij te werken naar LUIS [endpoint](https://aka.ms/luis-api-v3) -api's. 
