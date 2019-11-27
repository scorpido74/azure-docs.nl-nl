---
title: Gegevens wijziging-LUIS
titleSuffix: Azure Cognitive Services
description: Informatie over hoe gegevens kunnen worden gewijzigd voordat voorspellingen in Language Understanding (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: a199821c4db7fd8131ec54700b8c999dfe604a6e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74222026"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>ALTER utterance gegevens vóór of tijdens de voorspelling
LUIS biedt methoden voor het bewerken van de utterance vóór of tijdens de voorspelling. Dit zijn onder andere het corrigeren van de [Spelling](luis-tutorial-bing-spellcheck.md)en het oplossen van problemen met de tijd zone voor vooraf ontwikkelde [datetimeV2](luis-reference-prebuilt-datetimev2.md). 

## <a name="correct-spelling-errors-in-utterance"></a>Corrigeren van spelfouten in utterance

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


LUIS maakt gebruik van [Bing spellingcontrole-API V7](../Bing-Spell-Check/overview.md) om spel fouten in de utterance te corrigeren. LUIS moet de sleutel die is gekoppeld aan die service. Maak de sleutel en voeg vervolgens de sleutel toe als een query reeks parameter op het [eind punt](https://go.microsoft.com/fwlink/?linkid=2092356). 

<!--
You can also correct spelling errors in the **Test** panel by [entering the key](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key is kept as a session variable in the browser for the Test panel. Add the key to the Test panel in each browser session you want spelling corrected. 

Usage of the key in the test panel and at the endpoint count toward the [key usage](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) quota. LUIS implements Bing Spell Check limits for text length. 

-->

Het eindpunt moet twee parameters voor spellingcorrecties om te werken:

|Param|Waarde|
|--|--|
|`spellCheck`|booleaans|
|`bing-spell-check-subscription-key`|[Bing spellingcontrole-API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) -eindpunt sleutel|

Wanneer [Bing spellingcontrole-API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) een fout detecteert, worden de oorspronkelijke utterance en de gecorrigeerde utterance met de voor spellingen van het eind punt geretourneerd.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2-antwoord op Voorspellings eindpunt](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3-Voorspellings eindpunt antwoord](#tab/V3)
 
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
De Bing spell check-API die in LUIS wordt gebruikt, biedt geen ondersteuning voor een lijst met woorden die moeten worden genegeerd tijdens de wijziging van de spelling controle. Als u een lijst met woorden of acroniemen wilt toestaan, verwerkt u de utterance in de client toepassing voordat u de utterance naar LUIS verzendt voor het voors pellen van de intentie.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Tijdzone van de vooraf gedefinieerde datetimeV2 entiteit wijzigen
Wanneer een LUIS-app gebruikmaakt van de vooraf gemaakte [datetimeV2](luis-reference-prebuilt-datetimev2.md) -entiteit, kan een datum/tijd-waarde worden geretourneerd in het Voorspellings antwoord. De tijdzone van de aanvraag wordt gebruikt om te bepalen van de juiste datum/tijd om terug te keren. Als de aanvraag afkomstig is van een bot of een andere gecentraliseerde toepassing voorafgaand aan LUIS, corrigeer dan de tijdzone die LUIS gebruikt. 

### <a name="endpoint-querystring-parameter"></a>Eindpunt querystring-parameter
De tijd zone wordt gecorrigeerd door de tijd zone van de gebruiker toe te voegen aan het [eind punt](https://go.microsoft.com/fwlink/?linkid=2092356) met behulp van de para meter `timezoneOffset`. De waarde van `timezoneOffset` moet het positieve of negatieve getal zijn, in minuten, om de tijd te wijzigen.  

|Param|Waarde|
|--|--|
|`timezoneOffset`|positief of negatief getal, in minuten|

### <a name="daylight-savings-example"></a>Voorbeeld van de zomer-en besparingen
Als u de geretourneerde vooraf gemaakte datetimeV2 nodig hebt om te passen op zomer tijd, moet u de `timezoneOffset` query string-para meter gebruiken met een +/-waarde in minuten voor de [eindpunt](https://go.microsoft.com/fwlink/?linkid=2092356) query.

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2-aanvraag voor Voorspellings eindpunt](#tab/V2)

60 minuten toevoegen: 

https://{Region}. API. cognitieve. Microsoft. com/Luis/v 2.0/apps/{appId}? q = de lampjes inschakelen? **time zone offset = 60**& verbose = {boolean} & spelling controle = {boole} & fase ring = {boolean} & Bing-spelling controle-abonnement-key = {string} & log = {Boolean}

60 minuten verwijderen: 

https://{Region}. API. cognitieve. Microsoft. com/Luis/v 2.0/apps/{appId}? q = de lampjes inschakelen? **time zone offset =-60**& uitgebreid = {boolean} & spelling controle = {boolean} & fase ring = {boolean} & Bing-spelling controle-abonnement-key = {string} & log = {Boolean}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3-Voorspellings eindpunt aanvraag](#tab/V3)

60 minuten toevoegen:

https://{Region}. API. cognitieve. micro soft. com/Luis/v 3.0-Preview/apps/{appId}/sleuven/Production/voorspelbaar? query = de lampjes inschakelen? **time zone offset = 60**& spelling controle = {boolean} & Bing-spelling-check-Subscription-key = {string} & log = {Boolean}

60 minuten verwijderen: 

https://{Region}. API. cognitieve. micro soft. com/Luis/v 3.0-Preview/apps/{appId}/sleuven/Production/voorspelbaar? query = de lampjes inschakelen? **time zone offset =-60**& spelling controle = {boolean} & Bing-spelling-check-Subscription-key = {string} & log = {Boolean}

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C#-code bepaalt de juiste waarde van timezoneOffset
De volgende C# code maakt gebruik van de methode [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) van de klasse [time zone info](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) om de juiste `timezoneOffset` te bepalen op basis van de systeem tijd:

```CSharp
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
> [Spel fouten corrigeren met deze zelf studie](luis-tutorial-bing-spellcheck.md)
