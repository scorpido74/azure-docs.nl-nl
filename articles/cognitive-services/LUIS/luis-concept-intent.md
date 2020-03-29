---
title: Intenties en entiteiten - LUIS
titleSuffix: Azure Cognitive Services
description: Eén intentie vertegenwoordigt een taak of actie die de gebruiker wil uitvoeren. Het is een doel, uitgedrukt in van de uiting van een gebruiker. Definieer een set intenties die overeenkomt met acties die gebruikers in uw toepassing willen uitvoeren.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 309a2592dbac2918aeb532fbe91e33d296f4e5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220972"
---
# <a name="intents-in-your-luis-app"></a>Intents in uw LUIS-app

Een intentie vertegenwoordigt een taak of actie die de gebruiker wil uitvoeren. Het is een doel of doel uitgedrukt in de [uiting](luis-concept-utterance.md)van een gebruiker.

Definieer een set intenties die overeenkomt met acties die gebruikers in uw toepassing willen uitvoeren. Een reis-app definieert bijvoorbeeld verschillende intenties:

Intenties voor reisapps   |   Voorbeelden van utterances   | 
------|------|
 BookFlight     |   "Boek me een vlucht naar Rio volgende week" <br/> "Vlieg me naar Rio op de 24e" <br/> "Ik heb een vliegticket nodig volgende zondag naar Rio de Janeiro"    |
 Begroeting     |   "Hallo" <br/>"Hallo" <br/>"Goedemorgen"  |
 CheckWeather CheckWeather | "Hoe is het weer in Boston?" <br/> "Toon me de voorspelling voor dit weekend" |
 Geen         | "Geef me een cookie recept"<br>"Hebben de Lakers gewonnen?" |

Alle toepassingen worden geleverd met de vooraf gedefinieerde intentie, "[Geen](#none-intent)", dat is de fallback intentie. 

## <a name="prebuilt-domains-provide-intents"></a>Vooraf gebouwde domeinen bieden intenties
Naast intents die u definieert, u vooraf gebouwde intenties gebruiken van een van de [vooraf gebouwde domeinen.](luis-how-to-use-prebuilt-domains.md) 

## <a name="return-all-intents-scores"></a>Alle intenties retourneren
U wijst een utterance toe aan één intentie. Wanneer LUIS een utterance op het eindpunt ontvangt, wordt standaard de bovenste intentie voor die utterance geretourneerd. 

Als u de scores voor alle intenties voor de utterance wilt, u een vlag op de queryreeks van de voorspellings-API weergeven. 

|Api-versie voor voorspelling|Markering|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Intentie in vergelijking met entiteit
De intentie vertegenwoordigt actie die de toepassing moet uitvoeren voor de gebruiker en is gebaseerd op de volledige utterance. Een utterance kan slechts één intentie met hoge scores hebben, maar kan veel entiteiten hebben. 

<a name="how-do-intents-relate-to-entities"></a>

Maak een intentie wanneer de _intentie_ van de gebruiker een actie in uw clienttoepassing zou activeren, zoals een oproep naar de functie checkweather(). Maak vervolgens entiteiten om parameters weer te geven die nodig zijn om de actie uit te voeren. 

|Intentie   | Entiteit | Voorbeeld van een utterance   | 
|------------------|------------------------------|------------------------------|
| CheckWeather CheckWeather | { "type": "locatie", "entiteit": "seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "tomorrow","resolution":"2018-05-23" } | Hoe is het weer? `Seattle` `tomorrow` |
| CheckWeather CheckWeather | { "type": "date_range", "entiteit": "dit weekend" } | Laat me de voorspelling voor`this weekend` | 
||||

## <a name="prebuilt-domain-intents"></a>Vooraf gebouwde domeinintenten

[Vooraf gebouwde domeinen](luis-how-to-use-prebuilt-domains.md) bieden intenties met uitingen. 

## <a name="none-intent"></a>None- intent

De intentie **Geen** wordt gemaakt, maar expres leeg gelaten. De intentie **Geen** is een vereiste intentie en kan niet worden verwijderd of een andere naam krijgen. Vul het met uitingen die zich buiten uw domein bevinden.

De **intentie Geen** is de terugvalintentie, belangrijk in elke app, en moet 10% van de totale uitingen hebben. Het wordt gebruikt om LUIS-uitingen te onderwijzen die niet belangrijk zijn in het app-domein (onderwerpgebied). Als u geen uitingen toevoegt voor de intentie **Geen,** dwingt LUIS een utterance die zich buiten het domein bevindt in een van de domeinintenties. Dit zal de voorspellingscores scheeftrekken door LUIS de verkeerde bedoeling voor de uiting te onderwijzen. 

Wanneer een utterance wordt voorspeld als de intentie Geen, kan de clienttoepassing meer vragen stellen of een menu bieden om de gebruiker naar geldige keuzes te leiden. 

## <a name="negative-intentions"></a>Negatieve intenties 
Als u negatieve en positieve intenties wilt bepalen, zoals 'Ik **wil** een auto' en 'Ik wil **geen auto',** u twee intenties (één positief en één negatief) maken en voor elk van deze neen passende uitingen toevoegen. U ook één intentie maken en de twee verschillende positieve en negatieve termen markeren als een entiteit.  

## <a name="intents-and-patterns"></a>Intenties en patronen

Als u voorbeelduitingen hebt, die in een deel of geheel kunnen worden gedefinieerd als een reguliere expressie, u overwegen de [entiteit met reguliere expressie](luis-concept-entity-types.md#regular-expression-entity) te gebruiken die is gekoppeld aan een [patroon](luis-concept-patterns.md). 

Het gebruik van een entiteit met regelmatige expressie garandeert de gegevensextractie, zodat het patroon wordt geëvenaard. De patroonmatching garandeert dat een exacte intentie wordt geretourneerd. 

## <a name="intent-balance"></a>Intentiesaldo
De intenties van het app-domein moeten een balans hebben tussen uitingen in elke intentie. Heb niet één intentie met 10 uitingen en een andere intentie met 500 uitingen. Dit is niet in balans. Als u deze situatie hebt, bekijkt u de intentie met 500 uitingen om te zien of veel van de intenties kunnen worden gereorganiseerd in een [patroon.](luis-concept-patterns.md) 

De **intentie Geen** is niet opgenomen in het saldo. Die intentie moet 10% van de totale uitingen in de app bevatten.

## <a name="intent-limits"></a>Intentielimieten
Bekijk [limieten](luis-boundaries.md#model-boundaries) om te begrijpen hoeveel intents u aan een model toevoegen. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Als u meer dan het maximum aantal intenties nodig hebt 
Overweeg eerst of uw systeem te veel intenties gebruikt. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Kunnen meerdere intents worden gecombineerd in één intentie met entiteiten 
Intents die te veel op elkaar lijken, kunnen het voor LUIS moeilijker maken om onderscheid te maken tussen deze objecten. Intents moeten zo gevarieerd zijn om de belangrijkste taken vast te leggen waar de gebruiker om vraagt, maar ze hoeven niet elk pad vast te leggen dat uw code neemt. BookFlight en FlightCustomerService kunnen bijvoorbeeld afzonderlijke intenties zijn in een reis-app, maar BookInternationalFlight en BookDomesticFlight lijken te veel op elkaar. Als uw systeem ze moet onderscheiden, gebruikt u entiteiten of andere logica in plaats van intenties. 

### <a name="dispatcher-model"></a>Verzendermodel
Meer informatie over het combineren van LUIS- en QnA-maker-apps met het [verzendmodel](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Hulp aanvragen voor apps met een aanzienlijk aantal intents
Als het verminderen van het aantal intenties of het verdelen van uw intenties in meerdere apps niet voor u werkt, neemt u contact op met de ondersteuning. Neem contact op met [azure-technische ondersteuning](https://azure.microsoft.com/support/options/)als uw Azure-abonnement ondersteuningsservices bevat. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [entiteiten,](luis-concept-entity-types.md)die belangrijke woorden zijn die relevant zijn voor intents
* Meer informatie over het [toevoegen en beheren van intenties](luis-how-to-add-intents.md) in uw LUIS-app.
* Best [practices voor intent-richtlijnen bekijken](luis-concept-best-practices.md)
