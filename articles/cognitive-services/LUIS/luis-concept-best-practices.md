---
title: Aanbevolen procedures-LUIS
titleSuffix: Azure Cognitive Services
description: Meer informatie over de aanbevolen procedures voor LUIS voor het verkrijgen van de beste resultaten van het model van uw LUIS-app.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 64d67edaf5affbc908fba7b6c261096589bc84d0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487618"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Aanbevolen procedures voor het bouwen van een taal die is gebaseerd op een app met Cognitive Services
Gebruik het ontwerp proces voor apps om uw LUIS-app te bouwen: 

* Bouw taal modellen (intents en entiteiten)
* Voeg een paar trainings voorbeeld uitingen (15-30 per intentie) toe
* Publiceren naar eind punt
* Testen vanaf eind punt 

Nadat de app is [gepubliceerd](luis-how-to-publish-app.md), gebruikt u de ontwikkelings levenscyclus om functies toe te voegen, te publiceren en te testen vanaf het eind punt. Begin niet de volgende ontwerp cyclus door meer voorbeeld uitingen toe te voegen, omdat LUIS uw model niet kan leren kennen met de gebruikers uitingen van de echte wereld. 

Vouw de uitingen niet uit totdat de huidige set van zowel het voor beeld als het eind punt uitingen vertrouwt, hoge Voorspellings scores. Verbeter scores met behulp van [actief leren](luis-concept-review-endpoint-utterances.md). 




## <a name="do-and-dont"></a>Do en niet
De volgende lijst bevat aanbevolen procedures voor LUIS-apps:

|Wel doen|Niet doen|
|--|--|
|[Afzonderlijke intenties definiëren](#do-define-distinct-intents)<br>[Descriptors aan intenties toevoegen](#do-add-descriptors-to-intents) |[Een groot aantal voor beelden uitingen toevoegen aan intenties](#dont-add-many-example-utterances-to-intents)<br>[Gebruik enkele of eenvoudige entiteiten](#dont-use-few-or-simple-entities) |
|[Vind een zoete vlek tussen te algemeen en te specifiek voor elke intentie](#do-find-sweet-spot-for-intents)|[LUIS als trainings platform gebruiken](#dont-use-luis-as-a-training-platform)|
|[Uw app iteratief bouwen met versies](#do-build-your-app-iteratively-with-versions)<br>[Entiteiten bouwen voor model ontleding](#do-build-for-model-decomposition)|[Een groot aantal voor beeld-uitingen met dezelfde indeling toevoegen en andere indelingen negeren](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Patronen in latere iteraties toevoegen](#do-add-patterns-in-later-iterations)|[De definitie van intenties en entiteiten combi neren](#dont-mix-the-definition-of-intents-and-entities)|
|[U hoeft uw uitingen te verdelen over alle intenties](#balance-your-utterances-across-all-intents) , met uitzonde ring van de geen intentie.<br>[Voor beeld uitingen toevoegen aan geen intentie](#do-add-example-utterances-to-none-intent)|[Descriptors maken met alle mogelijke waarden](#dont-create-descriptors-with-all-the-possible-values)|
|[Gebruik de functie Voorst Ellen voor actief leren](#do-leverage-the-suggest-feature-for-active-learning)|[Te veel patronen toevoegen](#dont-add-many-patterns)|
|[De prestaties van uw app bewaken met batch tests](#do-monitor-the-performance-of-your-app)|[Train en publiceer met elk enkel voor beeld utterance toegevoegd](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Afzonderlijke intenties definiëren
Zorg ervoor dat de woorden lijst voor elke intentie alleen voor dat doel is en niet overlapt met een ander doel. Als u bijvoorbeeld een app wilt hebben die reis regelingen afhandelt, zoals vlieg vluchten en hotels, kunt u ervoor kiezen om deze onderwerpgebieden als afzonderlijke intenties of hetzelfde doel te hebben met entiteiten voor specifieke gegevens binnen de utterance.

Als de vocabulaire tussen twee intenties hetzelfde is, combi neer u de intentie en gebruikt u entiteiten. 

Bekijk het volgende voor beeld uitingen:

|Voorbeelden van utterances|
|--|
|Een vlucht boeken|
|Een hotel boeken|

`Book a flight` en `Book a hotel` dezelfde woorden lijst van `book a `gebruiken. Deze indeling is hetzelfde, zodat deze hetzelfde doel moet zijn met de verschillende woorden van `flight` en `hotel` als geëxtraheerde entiteiten. 

## <a name="do-add-descriptors-to-intents"></a>Voeg descriptors toe aan de intenties

Descriptoren helpen bij het beschrijven van functies voor een intentie. Een descriptor kan een woordgroepen lijst zijn met woorden die belang rijk zijn voor dat doel of een entiteit die belang rijk is voor dat doel. 

## <a name="do-find-sweet-spot-for-intents"></a>Vind een zoete plaats voor intenties
Gebruik Voorspellings gegevens uit LUIS om te bepalen of uw intentie elkaar overlappen. Overlappende intentiesen verwarren LUIS. Het resultaat is dat de bovenste Score intentie te dicht bij een andere intentie is. Omdat LUIS niet exact hetzelfde pad door de gegevens voor de training gebruikt, heeft een overlappende intentie een kans op het eerste of tweede in de training. U wilt dat de Score van de utterance voor elke intentie verder uit elkaar komt, zodat deze flip/flop niet plaatsvindt. Een goed onderscheiding voor intenties moet elke keer op de verwachte beste intentie worden uitgevoerd. 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Bouw uw app iteratief op met versies

Elke ontwerp cyclus moet zich in een nieuwe [versie](luis-concept-version.md)bevinden, die is gekloond van een bestaande versie. 

## <a name="do-build-for-model-decomposition"></a>Bouwen voor model ontleding

Model ontleding heeft een typisch proces van:

* **opzet** maken op basis van de bedoelingen van de gebruiker van de client-app
* 15-30-voor beeld uitingen toevoegen op basis van de werkelijke gebruikers invoer
* Label gegevens concept op het hoogste niveau in voor beeld utterance
* gegevens concept in subonderdelen opsplitsen
* descriptors (onderdelen) toevoegen aan subonderdelen
* descriptors (onderdelen) aan doel toevoegen 

Wanneer u de intentie hebt gemaakt en voor beeld-uitingen hebt toegevoegd, wordt in het volgende voor beeld de entiteit ontleding beschreven. 

Begin met het identificeren van de volledige gegevens concepten die u wilt uitpakken in een utterance. Dit is uw door de computer geleerde entiteit. Splits de woord groep vervolgens in de bijbehorende onderdelen. Dit omvat het identificeren van subonderdelen (als entiteiten), samen met descriptors en beperkingen. 

Als u bijvoorbeeld een adres wilt extra heren, kan de op de computer geleerde entiteit `Address`worden genoemd. Identificeer tijdens het maken van het adres een aantal subonderdelen, zoals het adres, de plaats, de provincie en de post code. 

Ga door met het ontsamen stellen van deze elementen door de post code te **beperken** tot een reguliere expressie. Het adres afbreken in delen van een straat nummer (met een vooraf samengesteld nummer), een straat naam en een straat type. Het type straat kan worden beschreven met een lijst met **descriptors** , zoals de naam, cirkel, weg en Lane.

Met de V3 authoring API kunt u model ontleden. 

## <a name="do-add-patterns-in-later-iterations"></a>Voeg patronen toe in latere iteraties

U moet weten hoe de app zich gedraagt voordat [patronen](luis-concept-patterns.md) worden toegevoegd, omdat patronen meer sterk worden gewogen dan bijvoorbeeld uitingen en het vertrouwen wordt schuingetrokken. 

Wanneer u begrijpt hoe uw app zich gedraagt, voegt u patronen toe die van toepassing zijn op uw app. U hoeft deze niet bij elke [herhaling](luis-concept-app-iteration.md)toe te voegen. 

Er is geen kwaadief toevoegen aan het begin van uw model ontwerp, maar het is eenvoudiger om te zien hoe elk patroon het model wijzigt nadat het model is getest met uitingen. 
 
<!--

### Phrase lists

[Phrase lists](luis-concept-feature.md) allow you to define dictionaries of words related to your app domain. Seed your phrase list with a few words then use the suggest feature so LUIS knows about more words in the vocabulary specific to your app. A Phrase List improves intent detection and entity classification by boosting the signal associated with words or phrases that are significant to your app. 

Don't add every word to the vocabulary since the phrase list isn't an exact match. 

For more information:
* Concept: [Phrase list features in your LUIS app](luis-concept-feature.md)
* How-to: [Use phrase lists to boost signal of word list](luis-how-to-add-features.md)



### Patterns

Real user utterances from the endpoint, very similar to each other, may reveal patterns of word choice and placement. The [pattern](luis-concept-patterns.md) feature takes this word choice and placement along with regular expressions to improve your prediction accuracy. A regular expression in the pattern allows for words and punctuation you intend to ignore while still matching the pattern. 

Use pattern's [optional syntax](luis-concept-patterns.md) for punctuation so punctuation can be ignored. Use the [explicit list](luis-concept-patterns.md#explicit-lists) to compensate for pattern.any syntax issues. 

For more information:
* Concept: [Patterns improve prediction accuracy](luis-concept-patterns.md)
* How-to: [How to add Patterns to improve prediction accuracy](luis-how-to-model-intent-pattern.md)
-->

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Laat uw uitingen in balans voor alle intenties

Om ervoor te zorgen dat LUIS-voor spellingen nauw keurig zijn, moet de hoeveelheid voor beeld uitingen in elke intentie (met uitzonde ring van het geen doel) relatief gelijk zijn. 

Als u een intentie hebt met 100-voor beeld-uitingen en een intentie met 20 voor beeld-uitingen, heeft de utterance-intentie van 100 een hoger tempo van voor spelling.  

## <a name="do-add-example-utterances-to-none-intent"></a>Voeg voor beeld uitingen toe aan geen intentie

Dit is de terugval intentie, wat inhoudt dat u buiten uw toepassing kunt vinden. Voeg één voor beeld utterance toe aan de geen intentie voor elk 10 voor beeld uitingen in de rest van uw LUIS-app.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Gebruik de functie Voorst Ellen voor actief leren

Gebruik het **controle punt uitingen** van het [actieve leer proces](luis-how-to-review-endpoint-utterances.md)regel matig, in plaats van meer voor beeld-uitingen aan intenties toe te voegen. Omdat de app voortdurend eind punt uitingen ontvangt, wordt deze lijst steeds groter en gewijzigd.

## <a name="do-monitor-the-performance-of-your-app"></a>Bewaak de prestaties van uw app.

Bewaak de nauw keurigheid van de voor spelling met behulp van een [batch-testset](luis-concept-batch-test.md) . 

Bewaar een afzonderlijke set uitingen die niet worden gebruikt als [voor beeld uitingen](luis-concept-utterance.md) of endpoint uitingen. Blijf de app verbeteren voor uw testset. Pas de testset aan om echte gebruikers uitingen weer te geven. Gebruik deze testset om elke iteratie of versie van de app te evalueren. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Voeg geen veel voor beeld-uitingen toe aan intents

Nadat de app is gepubliceerd, voegt u alleen uitingen toe van actief leren in het ontwikkelings levenscyclus proces. Als uitingen te vergelijkbaar zijn, voegt u een patroon toe. 

## <a name="dont-use-few-or-simple-entities"></a>Gebruik geen enkele of eenvoudige entiteiten

Entiteiten zijn gebouwd voor het uitpakken en voors pellen van gegevens. Het is belang rijk dat elke intentie door machines geleerde entiteiten heeft die de gegevens in het doel beschrijven. Dit helpt LUIS de intentie te voors pellen, zelfs als uw client toepassing de geëxtraheerde entiteit niet hoeft te gebruiken. 

## <a name="dont-use-luis-as-a-training-platform"></a>Gebruik LUIS niet als trainings platform

LUIS is specifiek voor het domein van een taal model. Het is niet bedoeld om te werken als een algemeen platform voor het trainen van natuurlijke taal. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Voeg geen veel voor beeld-uitingen met dezelfde indeling toe, waarbij andere indelingen worden genegeerd

LUIS verwacht variaties in een uitingen van een intentie. De uitingen kan variëren en heeft dezelfde algemene betekenis. Variaties kunnen de utterance lengte, woorden keuze en woord plaatsing bevatten. 

|Gebruik niet dezelfde indeling|Gebruik verschillende notaties|
|--|--|
|Een ticket kopen bij Seattle<br>Een ticket kopen bij Parijs<br>Een ticket kopen bij Orlando|1 ticket kopen bij Seattle<br>Reserveer twee stoelen op het rode oog tot Parijs volgende maandag<br>Ik wil 3 tickets boeken naar Orlando voor een lente-afbreek punt|

In de tweede kolom worden verschillende werk woorden (kopen, reserve ringen, boeken), verschillende hoeveel heden (1, 2, 3) en verschillende regelingen van woorden gebruikt, maar allemaal hetzelfde als het kopen van vlieg tickets voor reizen. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>De definitie van intents en entiteiten niet combi neren

Maak een intentie voor elke actie die uw bot gaat ondernemen. Gebruik entiteiten als para meters die deze actie mogelijk maken. 

Voor een bot die vlieg vluchten gaat boeken, maakt u een **BookFlight** intentie. Maak geen intentie voor elke luchtvaart maatschappij of voor elke bestemming. Gebruik deze gegevens als [entiteiten](luis-concept-entity-types.md) en markeer deze in het voor beeld uitingen. 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Geen descriptors met alle mogelijke waarden maken

Geef een aantal voor beelden in de [woordgroepen lijst](luis-concept-feature.md) met descriptors, maar niet elk woord. LUIS generaliseert en houdt rekening met de context. 

## <a name="dont-add-many-patterns"></a>Geen veel patronen toevoegen

Voeg niet te veel [patronen](luis-concept-patterns.md)toe. LUIS is bedoeld om snel te leren werken met minder voor beelden. U hoeft het systeem niet onnodig te overbelasten.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Train en publiceer niet met elk enkel voor beeld utterance

10 of 15 uitingen toevoegen vóór training en publicatie. Zo kunt u de gevolgen voor de nauw keurigheid van de voor spellingen weer geven. Het toevoegen van één utterance heeft mogelijk geen zicht bare invloed op de score. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [plannen van uw app](luis-how-plan-your-app.md) in uw Luis-app.
