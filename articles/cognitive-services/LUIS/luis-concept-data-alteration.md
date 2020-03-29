---
title: Wijziging van gegevens - LUIS
description: Meer informatie over hoe gegevens kunnen worden gewijzigd voordat voorspellingen worden gedaan in Taalbegrip (LUIS)
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: b3b36351a64a4e1a0bd13d5785a4e0609a80901d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292062"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Utterancegegevens voor of tijdens de voorspelling wijzigen
LUIS biedt manieren om de utterance voor of tijdens de voorspelling te manipuleren. Deze omvatten [het oplossen van spelling,](luis-tutorial-bing-spellcheck.md)en het oplossen van tijdzone problemen voor vooraf gebouwde [datetimeV2](luis-reference-prebuilt-datetimev2.md).

## <a name="correct-spelling-errors-in-utterance"></a>Spellingfouten in utterance corrigeren


### <a name="v3-runtime"></a>V3-runtime

Verwerk tekst voor spellingcorrecties voordat u de utterance naar LUIS verzendt. Gebruik voorbeelduitingen met de juiste spelling om ervoor te zorgen dat u de juiste voorspellingen krijgt.

Gebruik [Bing Spell Check](../bing-spell-check/overview.md) om tekst te corrigeren voordat u deze naar LUIS verzendt.

### <a name="prior-to-v3-runtime"></a>Voorafgaand aan V3 runtime

LUIS gebruikt [Bing Spell Check API V7](../Bing-Spell-Check/overview.md) om spelfouten in de utterance te corrigeren. LUIS heeft de sleutel nodig die aan die service is gekoppeld. Maak de sleutel en voeg de sleutel toe als parameter querystring op het [eindpunt.](https://go.microsoft.com/fwlink/?linkid=2092356)

Het eindpunt vereist twee param's voor spellingcorrecties om te werken:

|Param|Waarde|
|--|--|
|`spellCheck`|booleaans|
|`bing-spell-check-subscription-key`|[Bing Spell Check API V7-eindpunttoets](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Wanneer [Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) een fout detecteert, worden de oorspronkelijke utterance en de gecorrigeerde utterance geretourneerd, samen met voorspellingen van het eindpunt.

#### <a name="v2-prediction-endpoint-response"></a>[V2 voorspelling eindpuntrespons](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 voorspelling eindpuntrespons](#tab/V3)

```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * *

### <a name="list-of-allowed-words"></a>Lijst met toegestane woorden
De API voor spellingcontrole van Bing die in LUIS wordt gebruikt, biedt geen ondersteuning voor een lijst met woorden die u moet negeren tijdens wijzigingen bij de spellingcontrole. Als u een lijst met woorden of afkortingen moet toestaan, verwerkt u de utterance in de clienttoepassing voordat u de utterance naar LUIS verzendt voor intentievoorspelling.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Tijdzone wijzigen van vooraf gebouwde datumV2-entiteit
Wanneer een LUIS-app de vooraf gebouwde [datumV2-entiteit](luis-reference-prebuilt-datetimev2.md) gebruikt, kan een datumtijdwaarde worden geretourneerd in het voorspellingsantwoord. De tijdzone van de aanvraag wordt gebruikt om de juiste datumtijd te bepalen om terug te keren. Als de aanvraag afkomstig is van een bot of een andere gecentraliseerde toepassing voordat u naar LUIS gaat, corrigeert u de tijdzone die LUIS gebruikt.

### <a name="endpoint-querystring-parameter"></a>Parameter eindpuntquerytekenreeks
De tijdzone wordt gecorrigeerd door de tijdzone van [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) de gebruiker `timezoneOffset` toe te voegen aan het eindpunt met behulp van de param. De waarde `timezoneOffset` van moet het positieve of negatieve getal zijn, in minuten, om de tijd te wijzigen.

|Param|Waarde|
|--|--|
|`timezoneOffset`|positief of negatief getal, in minuten|

### <a name="daylight-savings-example"></a>Voorbeeld van zomertijd
Als u de geretourneerde vooraf gebouwde datetimeV2 wilt aanpassen `timezoneOffset` voor de zomertijd, moet u de querystringparameter met een +/- waarde in minuten gebruiken voor de [eindpuntquery.](https://go.microsoft.com/fwlink/?linkid=2092356)

#### <a name="v2-prediction-endpoint-request"></a>[V2 voorspelling eindpunt aanvraag](#tab/V2)

Voeg 60 minuten toe:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Verwijder 60 minuten:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v3-prediction-endpoint-request"></a>[V3 voorspelling eindpunt aanvraag](#tab/V3)

Voeg 60 minuten toe:

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Verwijder 60 minuten:

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=-60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

* * *

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C#-code bepaalt de juiste waarde van timezoneOffset
De volgende C#-code gebruikt de [FindSystemTimeZoneById-methode](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) van `timezoneOffset` de klasse [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) om de juiste te bepalen op basis van de systeemtijd:

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Spelfouten corrigeren met deze zelfstudie](luis-tutorial-bing-spellcheck.md)
