---
title: Wijziging van het eindpunt voorspellen in de V3-API
description: De queryvoorspellingeindpunt V3 API's zijn gewijzigd. Gebruik deze handleiding om te begrijpen hoe u migreren naar versie 3-eindpunt-API's.
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 9a8e8cb331dd11eebaddbcbf8f603c1148415aef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117384"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Wijziging van het eindpunt voor V3 voorspellen

De queryvoorspellingeindpunt V3 API's zijn gewijzigd. Gebruik deze handleiding om te begrijpen hoe u migreren naar versie 3-eindpunt-API's.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Algemeen beschikbare status** - deze V3-API bevat belangrijke JSON-aanvraag- en antwoordwijzigingen van v2-API.

De V3 API biedt de volgende nieuwe functies:

* [Externe entiteiten](#external-entities-passed-in-at-prediction-time)
* [Dynamische lijsten](#dynamic-lists-passed-in-at-prediction-time)
* [Prebuilt entiteit JSON wijzigingen](#prebuilt-entity-changes)

De voorspelling eindpunt [aanvraag](#request-changes) en [antwoord](#response-changes) hebben belangrijke wijzigingen ter ondersteuning van de nieuwe functies hierboven vermeld, met inbegrip van de volgende:

* [Wijziging van antwoordobject](#top-level-json-changes)
* [Verwijzingen naar de naam van entiteitsrollen in plaats van de naam van de entiteit](#entity-role-name-instead-of-entity-name)
* [Eigenschappen om entiteiten in uitingen te markeren](#marking-placement-of-entities-in-utterances)

[Referentiedocumentatie](https://aka.ms/luis-api-v3) is beschikbaar voor V3.

## <a name="v3-changes-from-preview-to-ga"></a>V3 verandert van preview naar GA

V3 heeft de volgende wijzigingen aangebracht in het kader van de overstap naar GA:

* De volgende vooraf gebouwde entiteiten hebben verschillende JSON-antwoorden:
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * Meetbare eenheidssleutelnaam van `units` tot`unit`

* Aanvraag body JSON verandering:
    * van `preferExternalEntities` tot`preferExternalEntities`
    * optionele `score` parameter voor externe entiteiten

* Reactielichaam JSON verandert:
    * `normalizedQuery`Verwijderd

## <a name="suggested-adoption-strategy"></a>Voorgestelde adoptiestrategie

Als u Bot Framework, Bing Spell Check V7 gebruikt of alleen uw LUIS-app wilt migreren, blijft u het V2-eindpunt gebruiken.

Als u weet dat geen van uw clienttoepassingen of integraties (Bot Framework en Bing Spell Check V7) wordt beïnvloed en u uw LUIS-app authoring en uw voorspellingseindpunt tegelijkertijd migreren, begint u met het V3-voorspellingseindpunt. De V2 voorspelling eindpunt zal nog steeds beschikbaar zijn en is een goede fall-back strategie.


## <a name="not-supported"></a>Niet ondersteund

### <a name="bing-spell-check"></a>Bing Spellingcontrole

Deze API wordt niet ondersteund in V3 voorspelling eindpunt - blijven V2 API voorspelling eindpunt te gebruiken voor spellingcorrecties. Als u spellingcorrectie nodig hebt tijdens het gebruik van V3 API, laat u de clienttoepassing de [Bing Spell Check](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview) API aanroepen en wijzigt u de tekst in de juiste spelling voordat u de tekst naar de LUIS API verzendt.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework- en Azure Bot-serviceclienttoepassingen

Blijf het V2 API-voorspellingseindpunt gebruiken totdat de V4.7 van het Bot Framework is vrijgegeven.

## <a name="v2-api-deprecation"></a>V2-API-afschrijving

De V2 voorspelling API zal niet worden afgeschaft voor ten minste 9 maanden na de V3 preview, 8 juni 2020.

## <a name="endpoint-url-changes"></a>URL-wijzigingen voor eindpunten

### <a name="changes-by-slot-name-and-version-name"></a>Wijzigingen op sleufnaam en versienaam

De indeling van de HTTP-aanroep V3 is gewijzigd.

Als u per versie wilt opvragen, moet `"directVersionPublish":true`u eerst publiceren via [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) met . Query the endpoint refering the version ID instead of the slot name.

|VOORSPELLING API-VERSIE|Methode|URL|
|--|--|--|
|V3|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>voorspelling</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|V3|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>voorspelling</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|V2|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>voorspelling</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/versies/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|V2|POST|https://<b>{REGIO}</b>.api.cognitive.microsoft.com/luis/<b>voorspelling</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/versies/<b>{VERSION-ID}</b>/predict|

|Geldige waarden voor`SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Wijzigingen aanvragen

### <a name="query-string-changes"></a>Wijzigingen in querytekenreeksen

De V3-API heeft verschillende querytekenreeksparameters.

|Param-naam|Type|Versie|Standaard|Doel|
|--|--|--|--|--|
|`log`|booleaans|V2 & V3|false|Query opslaan in logboekbestand. De standaardwaarde is onwaar.|
|`query`|tekenreeks|V3 alleen|Geen standaard - dit is vereist in de GET-aanvraag|**In V2**bevindt de te `q` voorspellen uiting zich in de parameter. <br><br>**In V3**wordt de functionaliteit `query` doorgegeven in de parameter.|
|`show-all-intents`|booleaans|V3 alleen|false|Retourneer alle intenties met de bijbehorende score in het object **prediction.intents.** Intents worden geretourneerd als `intents` objecten in een bovenliggend object. Dit maakt programmatische toegang mogelijk zonder de intentie `prediction.intents.give`in een array te hoeven vinden: . In V2, werden deze teruggegeven in een array. |
|`verbose`|booleaans|V2 & V3|false|**In V2**, wanneer ingesteld op waar, alle voorspelde bedoelingen werden geretourneerd. Als u alle voorspelde bedoelingen nodig hebt, `show-all-intents`gebruikt u de V3-param van .<br><br>**In V3**biedt deze parameter alleen entiteitsmetagegevens details van entiteitsvoorspelling.  |
|`timezoneOffset`|tekenreeks|V2|-|Tijdzone toegepast op datumtijdV2 entiteiten.|
|`datetimeReference`|tekenreeks|V3|-|[Tijdzone toegepast](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) op datumtijdV2 entiteiten. Vervangt `timezoneOffset` van V2.|


### <a name="v3-post-body"></a>V3 POST lichaam

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Eigenschap|Type|Versie|Standaard|Doel|
|--|--|--|--|--|
|`dynamicLists`|matrix|V3 alleen|Niet vereist.|[Met dynamische lijsten](#dynamic-lists-passed-in-at-prediction-time) u een bestaande getrainde en gepubliceerde lijstentiteit uitbreiden, die al in de LUIS-app is opgenomen.|
|`externalEntities`|matrix|V3 alleen|Niet vereist.|[Externe entiteiten](#external-entities-passed-in-at-prediction-time) geven uw LUIS-app de mogelijkheid om entiteiten tijdens runtime te identificeren en te labelen, die kunnen worden gebruikt als functies voor bestaande entiteiten. |
|`options.datetimeReference`|tekenreeks|V3 alleen|Geen standaard|Wordt gebruikt om [de datumtijdV2-verschuiving](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)te bepalen . De notatie voor de datumtijdReferentie is [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|booleaans|V3 alleen|false|Hiermee geeft u op of de externe entiteit van de gebruiker [(met dezelfde naam als bestaande entiteit)](#override-existing-model-predictions) wordt gebruikt of dat de bestaande entiteit in het model wordt gebruikt voor voorspelling. |
|`query`|tekenreeks|V3 alleen|Vereist.|**In V2**bevindt de te `q` voorspellen uiting zich in de parameter. <br><br>**In V3**wordt de functionaliteit `query` doorgegeven in de parameter.|



## <a name="response-changes"></a>Reactiewijzigingen

De JSON voor queryrespons is gewijzigd om een grotere programmatische toegang tot de gegevens die het vaakst worden gebruikt, mogelijk te maken.

### <a name="top-level-json-changes"></a>Json op topniveau verandert



De beste JSON-eigenschappen voor `verbose` V2 zijn ingesteld op true, waardoor alle `intents` intenties en hun scores in de eigenschap worden geretourneerd:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

De beste JSON-eigenschappen voor V3 zijn:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {},
        "entities":{}
    }
}
```

Het `intents` object is een ongeordende lijst. Ga er niet van `intents` uit dat `topIntent`het eerste kind in de overeenstemt met de . Gebruik in `topIntent` plaats daarvan de waarde om de score te vinden:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

De reactie JSON schema wijzigingen zorgen voor:

* Duidelijk onderscheid tussen `query`originele uiting, `prediction`en geretourneerde voorspelling, .
* Gemakkelijker programmatische toegang tot voorspelde gegevens. In plaats van een array in V2 op te sommen, u waarden op **naam** openen voor zowel intents als entiteiten. Voor voorspelde entiteitsrollen wordt de rolnaam geretourneerd omdat deze uniek is in de hele app.
* Gegevenstypen worden, indien bepaald, gerespecteerd. Numerieke gegevens worden niet meer geretourneerd als tekenreeksen.
* Onderscheid tussen eerste prioriteit voorspelling informatie en `$instance` extra metadata, geretourneerd in het object.

### <a name="entity-response-changes"></a>Wijzigingen in het antwoord van entiteiten

#### <a name="marking-placement-of-entities-in-utterances"></a>Plaatsing van entiteiten in uitingen markeren

**In V2**werd een entiteit gemarkeerd `startIndex` in `endIndex`een uiting met de en .

**In V3**is de `startIndex` entiteit `entityLength`gemarkeerd met en .

#### <a name="access-instance-for-entity-metadata"></a>Toegang `$instance` voor metagegevens van entiteiten

Als u metagegevens van entiteiten `verbose=true` nodig hebt, moet de `$instance` querytekenreeks de vlag gebruiken en bevat het antwoord de metagegevens in het object. Voorbeelden worden weergegeven in de JSON-antwoorden in de volgende secties.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Elke voorspelde entiteit wordt weergegeven als een array

Het `prediction.entities.<entity-name>` object bevat een array omdat elke entiteit meer dan één keer in de utterance kan worden voorspeld.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Vooraf gebouwde entiteitswijzigingen

Het Antwoordobject V3 bevat wijzigingen in vooraf gebouwde entiteiten. Bekijk [specifieke vooraf gebouwde entiteiten](luis-reference-prebuilt-entities.md) voor meer informatie.

#### <a name="list-entity-prediction-changes"></a>Wijzigingen in de voorspelling van de lijstentiteit

De voorspelling van JSON voor een lijstentiteit is gewijzigd in een array met arrays:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Elke binnenarray komt overeen met tekst in de utterance. Het interieurobject is een array omdat dezelfde tekst kan worden weergegeven in meer dan één sublijst van een lijstentiteit.

Bij toewijzing `entities` tussen het `$instance` object aan het object blijft de volgorde van objecten behouden voor de voorspellingen van de lijstentiteit.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Rolnaam entiteit in plaats van naam van de entiteit

In V2 `entities` heeft de array alle voorspelde entiteiten geretourneerd waarbij de entiteitsnaam de unieke id is. Als de entiteit in V3 rollen gebruikt en de voorspelling voor een entiteitsrol is, is de primaire id de rolnaam. Dit is mogelijk omdat entiteitsrolnamen uniek moeten zijn voor de hele app, inclusief andere namen van modellen (intent, entity).

In het volgende voorbeeld: overweeg een `Yellow Bird Lane`utterance met de tekst. Deze tekst wordt voorspeld `Location` als de `Destination`rol van een aangepaste entiteit van .

|Utterancetekst|Entiteitsnaam|Rolnaam|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

In V2 wordt de entiteit geïdentificeerd door de _entiteitsnaam_ met de rol als eigenschap van het object:

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

In V3 wordt de entiteit verwezen door de _entiteitsrol_, als de voorspelling voor de rol is:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

In V3 wordt hetzelfde `verbose` resultaat met de vlag gebruikt om entiteitsmetagegevens terug te sturen:

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

## <a name="external-entities-passed-in-at-prediction-time"></a>Externe entiteiten die worden doorgegeven op het moment van voorspelling

Externe entiteiten geven uw LUIS-app de mogelijkheid om entiteiten tijdens runtime te identificeren en te labelen, die kunnen worden gebruikt als functies voor bestaande entiteiten. Hiermee u uw eigen afzonderlijke en aangepaste entiteitsextractors gebruiken voordat u query's naar uw voorspellingseindpunt verzendt. Omdat dit gebeurt op het eindpunt van de queryvoorspelling, hoeft u uw model niet opnieuw te trainen en te publiceren.

De client-applicatie verstrekt een eigen entiteit extractor door het beheren van entiteit matching en het bepalen van de locatie binnen de uiting van die overeenkomende entiteit en vervolgens het verzenden van die informatie met de aanvraag.

Externe entiteiten zijn het mechanisme voor het uitbreiden van een entiteitstype terwijl ze nog steeds worden gebruikt als signalen naar andere modellen zoals rollen, composiet en andere.

Dit is handig voor een entiteit die alleen gegevens beschikbaar heeft bij queryvoorspellingsruntijd. Voorbeelden van dit soort gegevens zijn voortdurend veranderende gegevens of specifieke per gebruiker. U een LUIS-contactpersoon uitbreiden met externe informatie uit de lijst met contactpersonen van een gebruiker.

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

In de vorige utterance `him` wordt de `Hazem`utterance gebruikt als verwijzing naar . De conversationele chatbot, in de `him` post-body, kan toewijzen aan `Hazem`de entiteitswaarde die uit de eerste utterance is geëxtraheerd, .

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



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Dynamische lijsten doorgegeven op voorspellingstijd

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

## <a name="deprecation"></a>Afschaffing

De V2 API wordt niet afgeschaft gedurende ten minste 9 maanden na de V3 preview.

## <a name="next-steps"></a>Volgende stappen

Gebruik de V3 API-documentatie om bestaande REST-aanroepen bij te werken naar [LUIS-eindpunt-API's.](https://aka.ms/luis-api-v3)
