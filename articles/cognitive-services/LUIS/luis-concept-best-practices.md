---
title: Aanbevolen procedures voor het bouwen van uw LUIS-app
titleSuffix: Azure Cognitive Services
description: Ontdek de aanbevolen procedures om de beste resultaten uit het model van uw LUIS-app te halen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b4be79338db71ad83204fae971da0b77885a8070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280929"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Aanbevolen procedures voor het bouwen van een LUIS-app (Language Understanding)
Gebruik het ontwerpproces voor apps om uw LUIS-app te bouwen: 

* Taalmodellen (intents en entiteiten) bouwen
* Een paar trainingsvoorbeelduitingen toevoegen (15-30 per intentie)
* Publiceren naar eindpunt
* Test vanaf eindpunt 

Zodra uw app is [gepubliceerd,](luis-how-to-publish-app.md)gebruikt u de ontwikkelingslevenscyclus om functies toe te voegen, te publiceren en te testen vanaf eindpunt. Begin de volgende ontwerpcyclus niet door meer voorbeelduitingen toe te voegen, want dat laat LUIS uw model niet leren met uitingen van echte gebruikers. 

Vouw de uitingen niet uit totdat de huidige set van zowel voorbeeld- als eindpuntuitingen zelfverzekerde, hoge voorspellingsscores retourneert. Verbeter scores met behulp van [actief leren](luis-concept-review-endpoint-utterances.md). 




## <a name="do-and-dont"></a>Do and Don't
De volgende lijst bevat aanbevolen procedures voor LUIS-apps:

|Wel doen|Niet doen|
|--|--|
|[Duidelijke intenties definiëren](#do-define-distinct-intents)<br>[Beschrijvingen toevoegen aan intents](#do-add-descriptors-to-intents) |[Veel voorbeelduitingen toevoegen aan intents](#dont-add-many-example-utterances-to-intents)<br>[Gebruik weinig of eenvoudige entiteiten](#dont-use-few-or-simple-entities) |
|[Zoek een sweet spot tussen te generiek en te specifiek voor elke intentie](#do-find-sweet-spot-for-intents)|[LUIS gebruiken als trainingsplatform](#dont-use-luis-as-a-training-platform)|
|[Bouw uw app iteratief met versies](#do-build-your-app-iteratively-with-versions)<br>[Entiteiten bouwen voor modelontleding](#do-build-for-model-decomposition)|[Voeg veel voorbeelduitingen van dezelfde indeling toe en negeer andere indelingen](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Patronen toevoegen in latere iteraties](#do-add-patterns-in-later-iterations)|[De definitie van intenties en entiteiten mengen](#dont-mix-the-definition-of-intents-and-entities)|
|[Breng uw uitingen in evenwicht over alle intenties,](#balance-your-utterances-across-all-intents) behalve de intentie Geen.<br>[Voorbeelduitingen toevoegen aan geen intentie](#do-add-example-utterances-to-none-intent)|[Descriptoren maken met alle mogelijke waarden](#dont-create-descriptors-with-all-the-possible-values)|
|[Maak gebruik van de suggestiefunctie voor actief leren](#do-leverage-the-suggest-feature-for-active-learning)|[Te veel patronen toevoegen](#dont-add-many-patterns)|
|[De prestaties van uw app controleren met batchtests](#do-monitor-the-performance-of-your-app)|[Trainen en publiceren met elk voorbeeld uiting toegevoegd](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definieer verschillende intenties
Zorg ervoor dat de woordenschat voor elke intentie is alleen voor die intentie en niet overlappenmet een andere bedoeling. Als u bijvoorbeeld een app wilt hebben die reisarrangementen zoals vluchten van luchtvaartmaatschappijen en hotels verwerkt, u ervoor kiezen deze onderwerpgebieden als afzonderlijke intenties of dezelfde intentie te hebben met entiteiten voor specifieke gegevens in de utterance.

Als de woordenschat tussen twee intenties hetzelfde is, combineert u de intentie en gebruikt u entiteiten. 

Houd rekening met de volgende voorbeelduitingen:

|Voorbeelden van utterances|
|--|
|Boek een vlucht|
|Boek een hotel|

`Book a flight`en `Book a hotel` gebruik dezelfde woordenschat `book a `van . Deze indeling is hetzelfde, dus het moet dezelfde `flight` `hotel` bedoeling met de verschillende woorden van en als geëxtraheerde entiteiten. 

## <a name="do-add-descriptors-to-intents"></a>Descriptoren toevoegen aan intents

Beschrijvingen helpen functies te beschrijven met een intentie. Een beschrijving kan een woordenlijst zijn met woorden die belangrijk zijn voor die intentie of een entiteit die belangrijk is voor die intentie. 

## <a name="do-find-sweet-spot-for-intents"></a>Vind sweet spot voor intents
Gebruik voorspellingsgegevens van LUIS om te bepalen of uw intenties elkaar overlappen. Overlappende intenties verwarren LUIS. Het resultaat is dat de intentie voor het hoogste scoren te dicht bij een andere intentie staat. Omdat LUIS niet elke keer exact hetzelfde pad door de gegevens gebruikt voor training, heeft een overlappende intentie een kans om eerste of tweede in training te zijn. U wilt dat de score van de utterance voor elke intentie verder uit elkaar, zodat deze flip / flop niet gebeurt. Een goed onderscheid voor intents moet resulteren in de verwachte top intentie elke keer. 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Bouw uw app iteratief met versies

Elke ontwerpcyclus moet zich binnen een nieuwe [versie](luis-concept-version.md)begeven, gekloond uit een bestaande versie. 

## <a name="do-build-for-model-decomposition"></a>Bouw voor modelontbinding

Modelontbinding heeft een typisch proces van:

* **Intent** maken op basis van de gebruikersintenties van de client-app
* voeg 15-30 voorbeelduitingen toe op basis van de invoer van echte gebruikers
* label gegevensconcept op het hoogste niveau in voorbeeldutterance
* dataconcept in subcomponenten opsplitsen
* descriptoren (functies) toevoegen aan subcomponenten
* beschrijvingen (functies) toevoegen aan intent 

Zodra u de intentie hebt gemaakt en voorbeelduitingen hebt toegevoegd, wordt in het volgende voorbeeld entiteitsontleding beschreven. 

Begin met het identificeren van complete gegevensconcepten die u in een utterance wilt extraheren. Dit is uw machine-geleerde entiteit. Ontleed vervolgens de zin in de delen ervan. Dit omvat het identificeren van subcomponenten (als entiteiten), samen met beschrijvingen en beperkingen. 

Als u bijvoorbeeld een adres wilt extraheren, kan de `Address`bovenste door de machine geleerde entiteit worden aangeroepen . Tijdens het maken van het adres u enkele van de subcomponenten identificeren, zoals adres, woonplaats, staat en postcode. 

Blijf deze elementen ontbinden door de postcode te **beperken** tot een reguliere expressie. Ontleden het adres in delen van een straatnummer (met behulp van een vooraf gebouwd nummer), een straatnaam en een straattype. Het straattype kan worden beschreven met een **beschrijvingslijst** zoals laan, cirkel, weg en rijstrook.

De V3 authoring API zorgt voor modelontbinding. 

## <a name="do-add-patterns-in-later-iterations"></a>Patronen toevoegen in latere iteraties

U moet begrijpen hoe de app zich gedraagt voordat u [patronen](luis-concept-patterns.md) toevoegt, omdat patronen zwaarder worden gewogen dan voorbeelduitingen en het vertrouwen scheeftrekken. 

Zodra u begrijpt hoe uw app zich gedraagt, voegt u patronen toe zoals deze van toepassing zijn op uw app. U hoeft ze niet bij elke [iteratie](luis-concept-app-iteration.md)toe te voegen. 

Het kan geen kwaad om ze toe te voegen aan het begin van uw modelontwerp, maar het is gemakkelijker om te zien hoe elk patroon het model wijzigt nadat het model is getest met uitingen. 
 
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

## <a name="do-balance-your-utterances-across-all-intents"></a>Uw uitingen in evenwicht brengen in alle intenties

Om LUIS-voorspellingen nauwkeurig te laten zijn, moet het aantal voorbeelduitingen in elke intentie (met uitzondering van de intentie Geen) relatief gelijk zijn. 

Als u een intentie hebt met 100 voorbeelduitingen en een intentie met 20 voorbeelduitingen, heeft de intentie met 100 uitingen een hogere voorspellingssnelheid.  

## <a name="do-add-example-utterances-to-none-intent"></a>Voorbeelduitingen toevoegen aan geen intentie

Deze intentie is de terugvalintentie, die alles buiten uw toepassing aangeeft. Voeg één voorbeeldutterance toe aan de intentie Geen voor elke 10 voorbeelduitingen in de rest van uw LUIS-app.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Maak gebruik van de suggest-functie voor actief leren

Gebruik regelmatig **'Resteendpoint-uitingen** voor actief [leren'](luis-how-to-review-endpoint-utterances.md)controleren in plaats van meer voorbeelduitingen toe te voegen aan intents. Omdat de app voortdurend eindpuntuitingen ontvangt, wordt deze lijst steeds groter en verandert deze.

## <a name="do-monitor-the-performance-of-your-app"></a>Houd de prestaties van uw app in de gaten

Controleer de nauwkeurigheid van de voorspelling met behulp van [een batchtestset.](luis-concept-batch-test.md) 

Houd een afzonderlijke set uitingen bij die niet worden gebruikt als [voorbeelduitingen](luis-concept-utterance.md) of eindpuntuitingen. Blijf de app voor je testset verbeteren. Pas de testset aan om echte gebruikersuitingen weer te geven. Gebruik deze testset om elke iteratie of versie van de app te evalueren. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Voeg niet veel voorbeelduitingen toe aan intents

Nadat de app is gepubliceerd, voegt u alleen uitingen toe van actief leren in het ontwikkelingslevenscyclusproces. Als uitingen te veel op elkaar lijken, voegt u een patroon toe. 

## <a name="dont-use-few-or-simple-entities"></a>Gebruik geen enkele of eenvoudige entiteiten

Entiteiten zijn gebouwd voor gegevensextractie en -voorspelling. Het is belangrijk dat elke intentie machine-geleerde entiteiten heeft die de gegevens in de intentie beschrijven. Dit helpt LUIS de intentie te voorspellen, zelfs als uw clienttoepassing de geëxtraheerde entiteit niet hoeft te gebruiken. 

## <a name="dont-use-luis-as-a-training-platform"></a>Gebruik LUIS niet als trainingsplatform

LUIS is specifiek voor het domein van een taalmodel. Het is niet bedoeld om te werken als een algemene natuurlijke taal training platform. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Voeg niet veel voorbeelduitingen van dezelfde indeling toe, waarbij andere indelingen worden genegeerd

LUIS verwacht variaties in de uitingen van een intentie. De uitingen kunnen variëren terwijl ze dezelfde algemene betekenis hebben. Variaties kunnen utterancelengte, woordkeuze en woordplaatsing omvatten. 

|Gebruik niet dezelfde indeling|Gebruik verschillende indeling|
|--|--|
|Koop een ticket naar Seattle<br>Koop een ticket naar Parijs<br>Koop een ticket naar Orlando|Koop 1 ticket naar Seattle<br>Reserveer twee zetels op de rode ogen naar Parijs volgende week maandag<br>Ik wil graag 3 tickets naar Orlando boeken voor de voorjaarsvakantie|

De tweede kolom maakt gebruik van verschillende werkwoorden (kopen, reserveren, boek), verschillende hoeveelheden (1, twee, 3), en verschillende regelingen van woorden, maar hebben allemaal dezelfde intentie van de aankoop van vliegtickets voor reizen. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Meng de definitie van intents en entiteiten niet

Maak een intentie voor elke actie die je bot zal ondernemen. Gebruik entiteiten als parameters die die actie mogelijk maken. 

Voor een bot die vluchten van luchtvaartmaatschappijen boekt, maakt u een **BookFlight-intentie.** Maak geen intentie voor elke luchtvaartmaatschappij of elke bestemming. Gebruik deze gegevens als [entiteiten](luis-concept-entity-types.md) en markeer ze in de voorbeelduitingen. 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Maak geen beschrijvingen met alle mogelijke waarden

Geef een paar voorbeelden in de lijst met [descriptorzinnen,](luis-concept-feature.md) maar niet elk woord. LUIS generaliseert en houdt rekening met de context. 

## <a name="dont-add-many-patterns"></a>Voeg niet veel patronen toe

Voeg niet te veel [patronen](luis-concept-patterns.md)toe. LUIS is bedoeld om snel te leren met minder voorbeelden. Overbelast het systeem niet onnodig.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Niet trainen en publiceren met elk voorbeeld uiting

Voeg 10 of 15 uitingen toe voordat u deze traint en publiceert. Zo u de impact op de nauwkeurigheid van de voorspelling zien. Het toevoegen van één utterance heeft mogelijk geen zichtbare invloed op de score. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [plannen van uw app](luis-how-plan-your-app.md) in uw LUIS-app.
