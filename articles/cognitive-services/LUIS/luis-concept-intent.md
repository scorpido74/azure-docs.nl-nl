---
title: Intents en entiteiten-LUIS
titleSuffix: Azure Cognitive Services
description: Eén intentie vertegenwoordigt een taak of actie die de gebruiker wil uitvoeren. Het is een doel of het doel, uitgedrukt in van een gebruiker utterance. Een set intents die overeenkomt met voor acties die gebruikers te nemen in uw toepassing wilt definiëren.
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280888"
---
# <a name="intents-in-your-luis-app"></a>Intenties in uw LUIS-app

Een doel vertegenwoordigt een taak of actie de gebruiker wil om uit te voeren. Het is een doel of het doel, uitgedrukt in van een gebruiker [utterance](luis-concept-utterance.md).

Een set intents die overeenkomt met voor acties die gebruikers te nemen in uw toepassing wilt definiëren. Bijvoorbeeld, definieert een reis-app verschillende intents:

App-intents reizen   |   Voorbeelden van utterances   | 
------|------|
 BookFlight     |   "Book me een vlucht Rio volgende week" <br/> "Vliegt me naar Rio op de 24th" <br/> "Ik moet een ticket vlak volgende zondag naar Rio de Janeiro"    |
 Begroeting     |   'Hallo' <br/>"Hallo" <br/>"Goedemorgen"  |
 CheckWeather | "Wat is het weer, zoals in Boston?" <br/> 'Toon de prognose voor dit weekend' |
 None         | 'Ophalen me een cookie recept'<br>"De Lakers te winnen?" |

Alle toepassingen worden geleverd met de vooraf gedefinieerde intentie, '[geen](#none-intent)', de terugval opzet. 

## <a name="prebuilt-domains-provide-intents"></a>Vooraf gemaakte domeinen bieden intents
Naast de intenties die u definieert, kunt u vooraf ontwikkelde intenties gebruiken uit een van de [vooraf ontwikkelde domeinen](luis-how-to-use-prebuilt-domains.md). 

## <a name="return-all-intents-scores"></a>Retourneren van alle intents scores
U toewijzen een utterance aan een één doel. Wanneer LUIS een utterance op het eind punt ontvangt, wordt standaard het hoogste doel voor die utterance geretourneerd. 

Als u de scores voor alle intenties voor de utterance wilt, kunt u een vlag geven op de query reeks van de prediction-API. 

|API-versie voor voor spelling|Vlag|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Kunt u lezen wat ten opzichte van entiteit
De intentie vertegenwoordigt een actie die de toepassing moet uitvoeren voor de gebruiker en is gebaseerd op de hele utterance. Een utterance kan slechts één boven scoren doel hebben, maar het kan veel entiteiten hebben. 

<a name="how-do-intents-relate-to-entities"></a>

Maak een intentie wanneer de _bedoeling_ van de gebruiker een actie in uw client toepassing zou activeren, zoals een aanroep van de functie checkweather (). Maak vervolgens entiteiten om de vereiste para meters voor het uitvoeren van de actie weer te geven. 

|Intentie   | Entiteit | Voorbeeld van een utterance   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {"type": "locatie", "entiteit": "seattle"}<br>{"type": "builtin.datetimeV2.date","entity": 'morgen', 'Oplossing': "2018-05-23"} | Wat het weer, zoals `Seattle` `tomorrow`? |
| CheckWeather | {"type": "date_range', 'entiteit':"dit weekend"} | De weersvoorspelling voor weergeven `this weekend` | 
||||

## <a name="prebuilt-domain-intents"></a>Vooraf gedefinieerde domein intents

[Vooraf ontwikkelde domeinen](luis-how-to-use-prebuilt-domains.md) bieden intenties met uitingen. 

## <a name="none-intent"></a>None- intent

De **geen** bedoeling is gemaakt, maar opzet leeg wordt gelaten. De **geen** kunt u lezen wat is een vereiste bedoeling en kan niet worden verwijderd of hernoemd. Vul het met uitingen die zich buiten uw domein.

De **geen** intentie is de terugval intentie, die belang rijk is voor elke app, en moet 10% van de totale uitingen hebben. Het wordt gebruikt om te leren LUIS uitingen die in het app-domein (onderwerpsgebied) niet belangrijk zijn. Als u niet alle uitingen van toevoegt de **geen** intentie, LUIS dwingt een utterance die zich buiten het domein in een van de domein-intents. Hiermee wordt de scores voorspelling scheeftrekken door onderwijzen LUIS het verkeerde type voor de utterance. 

Wanneer een utterance wordt voor speld als geen intentie, kan de client toepassing meer vragen stellen of een menu opgeven om de gebruiker naar geldige keuzes te sturen. 

## <a name="negative-intentions"></a>Negatieve bedoelingen 
Als u wilt bepalen negatief als positief bedoelingen, zoals ' ik **wilt** een auto "en" ik **niet** wilt van een auto ", kunt u twee intents (één positieve en negatieve één) maken en juiste utterances voor toevoegen elke. Of u kunt maken van een enkele kunt u lezen wat en markeren van de twee verschillende positieve en negatieve voorwaarden als een entiteit.  

## <a name="intents-and-patterns"></a>Doel stellingen en patronen

Als u bijvoorbeeld uitingen hebt, die in een deel of geheel kunnen worden gedefinieerd als een reguliere expressie, kunt u overwegen de [reguliere expressie-entiteit](luis-concept-entity-types.md#regular-expression-entity) te gebruiken die is gekoppeld aan een [patroon](luis-concept-patterns.md). 

Het gebruik van een reguliere expressie-entiteit waarborgt de extractie van gegevens zodat het patroon wordt vergeleken. Het patroon dat overeenkomt met een exacte intentie, wordt geretourneerd. 

## <a name="intent-balance"></a>Intentie saldo
De app-intents domein moeten een balans tussen uitingen voor elk doel hebben. Beschikt niet over een doel met 10 uitingen en een ander doel met 500 uitingen. Dit is niet met gelijke taakverdeling. Als u deze situatie hebt, raadpleegt u de bedoeling van 500 uitingen om te zien als veel van de intenties kunnen opnieuw worden gerangschikt in een [patroon](luis-concept-patterns.md). 

De **geen** doel is niet opgenomen in het saldo. Dit doel moet 10% van de totale uitingen in de app bevatten.

## <a name="intent-limits"></a>Intentie limieten
Beoordeling [limieten](luis-boundaries.md#model-boundaries) om te begrijpen hoeveel intents kunt u toevoegen aan een model. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Als u meer dan het maximum aantal intents nodig hebt 
Ten eerste kunt u overwegen of uw systeem is te veel intents. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Meerdere intents kunnen worden gecombineerd in één doel met entiteiten 
Intents die te veel zijn kunnen maken voor LUIS onderscheid maken tussen deze moeilijker. Intents moet voldoende zijn om vast te leggen van de belangrijkste taken die de gebruiker wordt gevraagd, maar ze niet nodig hebben om vast te leggen van elk pad uw code wordt gevarieerd. Bijvoorbeeld, BookFlight en FlightCustomerService mogelijk afzonderlijke intents in een reis-app, maar BookInternationalFlight en BookDomesticFlight te vergelijkbaar zijn. Als uw systeem nodig heeft om deze te onderscheiden, gebruikt u entiteiten of andere logische gebruikt in plaats van een intents. 

### <a name="dispatcher-model"></a>Model van de functie voor berichtverzending
Meer informatie over het combineren van LUIS en QnA maker-apps met de [verzending model](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Help-informatie voor apps met een groot aantal intenties Request
Neem contact op met ondersteuning als het aantal intents te verminderen of uw intenties verdelen in meerdere apps niet voor u werkt. Als uw Azure-abonnement voor ondersteuningsservices bevat, neem dan contact op met [technische ondersteuning van Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [entiteiten](luis-concept-entity-types.md), die zijn belangrijk woorden die relevant zijn voor intents
* Meer informatie over het [toevoegen en beheren van intents](luis-how-to-add-intents.md) in uw LUIS-app.
* Controleren van de intentie [aanbevolen procedures](luis-concept-best-practices.md)
