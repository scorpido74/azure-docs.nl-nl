---
title: Aanbevolen procedures-LUIS
titleSuffix: Azure Cognitive Services
description: Informatie over de LUIS aanbevolen procedures voor de beste resultaten van uw LUIS-app-model.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 91ff99f674439580d369aad1490ded85d39d377c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382883"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Aanbevolen procedures voor het bouwen van een language understanding-app met Cognitive Services
Gebruik het ontwerp proces voor apps om uw LUIS-app te bouwen: 

* Taalmodel bouwen
* Een paar training voorbeeld utterances (10-15 per bedoeling) toevoegen
* Publiceren 
* Testen van eindpunt 
* Functies toevoegen

Als uw app is [gepubliceerd](luis-how-to-publish-app.md), gebruikt u de ontwerp cyclus om functies toe te voegen, te publiceren en te testen vanaf het eind punt. De volgende cyclus authoring niet beginnen met het toevoegen van meer voorbeeld uitingen. Die kunt niet meer van uw model met echte gebruiker uitingen LUIS. 

Opdat LUIS efficiënt aan de taak van de training, niet uitbreiden de uitingen totdat de huidige set met zowel voorbeeld als eindpunt uitingen vertrouwen, hoge voorspelling scores zijn geretourneerd. Verbeter scores met behulp van [actieve](luis-concept-review-endpoint-utterances.md)overzichten, [patronen](luis-concept-patterns.md)en [woordgroepen lijsten](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Voer en geen
De volgende lijst bevat aanbevolen procedures voor LUIS apps:

|Doen|Niet doen|
|--|--|
|[Verschillende intents definiëren](#do-define-distinct-intents) |[Veel voorbeeld uitingen aan intents toevoegen](#dont-add-many-example-utterances-to-intents) |
|[Een plek sweet tussen te algemeen en te specifiek voor elke doel vinden](#do-find-sweet-spot-for-intents)|[LUIS gebruiken als platform voor training](#dont-use-luis-as-a-training-platform)|
|[Bouw uw app iteratief](#do-build-the-app-iteratively)|[Toevoegen van veel voorbeeld uitingen van dezelfde indeling, andere indelingen worden genegeerd](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Woordgroep lijsten en patronen in latere iteraties toevoegen](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Combineer de definitie van de intenties en entiteiten](#dont-mix-the-definition-of-intents-and-entities)|
|[U hoeft uw uitingen te verdelen over alle intenties](#balance-your-utterances-across-all-intents) , met uitzonde ring van de geen intentie.<br>[Voorbeeld utterances toevoegen op None intentie](#do-add-example-utterances-to-none-intent)|[Woordgroep lijsten maken met alle mogelijke waarden](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Maak gebruik van de functie voor suggesties voor actief leren](#do-leverage-the-suggest-feature-for-active-learning)|[Te veel patronen toevoegen](#dont-add-many-patterns)|
|[De prestaties van uw app controleren](#do-monitor-the-performance-of-your-app)|[Trainen en publiceren met elk één voorbeeld utterance toegevoegd](#dont-train-and-publish-with-every-single-example-utterance)|
|[Versies voor elke app-iteratie gebruiken](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>Verschillende intents definiëren
Zorg ervoor dat de woordenlijst voor elk doel alleen voor dit doel en niet met een ander doel overlappende. Als u bijvoorbeeld een app wilt hebben die reis regelingen afhandelt, zoals vlieg vluchten en hotels, kunt u ervoor kiezen om deze onderwerpgebieden als afzonderlijke intenties of hetzelfde doel te hebben met entiteiten voor specifieke gegevens binnen de utterance.

Als het vocabulaire tussen twee intents hetzelfde is, de bedoeling combineren en entiteiten gebruiken. 

Houd rekening met het volgende voorbeeld-uitingen:

|Voorbeelden van utterances|
|--|
|Een vlucht reserveren|
|Boek een hotel|

'Een vlucht reserveren' en 'Book een hotel' gebruikt u de dezelfde vocabulaire van ' book een '. Deze indeling is hetzelfde als die van dezelfde intentie met de verschillende woorden van de vlucht en het hotel als geëxtraheerde entiteiten. 

Voor meer informatie:
* Begrip [Concepten over de intenties in uw LUIS-app](luis-concept-intent.md)
* Zelfstudie: [LUIS-app bouwen om de bedoelingen van de gebruiker te bepalen](luis-quickstart-intents-only.md)
* Procedure: [Intenties toevoegen om te bepalen wat de gebruikers intentie van uitingen zijn](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>Vind sweet positie voor intents
Voorspellingsgegevens van LUIS gebruiken om te bepalen als uw intenties elkaar overlappen. Overlappende intentiesen verwarren LUIS. Het resultaat is dat boven scoring-doel te sluiten bij een ander doel. LUIS gebruikt niet precies hetzelfde pad van de gegevens voor het trainen van elke keer, is overlappende opzet een kans dat het eerste of tweede in Trainingen. U wilt dat de Score van de utterance voor elke intentie verder uit elkaar komt, zodat deze flip/flop niet plaatsvindt. Goede onderscheidende factor zijn voor intents moet resulteren in het verwachte belangrijkste doel telkens. 
 
## <a name="do-build-the-app-iteratively"></a>Maak de app iteratief
Bewaar een afzonderlijke set uitingen die niet wordt gebruikt als [voor beeld uitingen](luis-concept-utterance.md) of endpoint uitingen. Houd de verbetering van de app voor de test-verzameling. Aanpassing van de test instellen om real-user-uitingen weer te geven. Gebruik deze testset om elke iteratie of versie van de app te evalueren. 

Ontwikkelaars moeten drie gegevenssets hebben. De eerste is de voorbeeld-uitingen voor het bouwen van het model. De tweede is voor het testen van het model op het eindpunt. De derde is de blind gegevens die worden gebruikt test [batch testen](luis-how-to-batch-test.md). Deze laatste set wordt niet gebruikt bij het trainen van de toepassing, noch verzonden op het eind punt.  

Voor meer informatie:
* Begrip [Ontwerp cyclus voor uw LUIS-app](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Voeg woordgroep lijsten en -patronen in de volgende herhalingen

Een best practice is deze procedures niet toe te passen voordat uw app is getest. U moet weten hoe de app zich gedraagt voordat [woordgroepen lijsten](luis-concept-feature.md) en [patronen](luis-concept-patterns.md) worden toegevoegd, omdat deze functies meer sterk worden gewogen dan bijvoorbeeld uitingen en het vertrouwen wordt schuingetrokken. 

Wanneer u begrijpt hoe uw app zich gedraagt zonder deze, voegt u elk van deze functies toe die van toepassing zijn op uw app. U hoeft deze functies niet aan elke [herhaling](luis-concept-app-iteration.md) toe te voegen of de functies te wijzigen met elke versie. 

Er zijn geen kwaad werkers toe te voegen aan het begin van uw model ontwerp, maar het is eenvoudiger om te zien hoe elke functie de resultaten wijzigt nadat het model is getest met uitingen. 

Een best practice is om te testen via het [eind punt](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) , zodat u het toegevoegde voor deel van [actieve lessen](luis-concept-review-endpoint-utterances.md)krijgt. Het [deel venster interactieve test](luis-interactive-test.md) is ook een geldige test methodologie. 
 

### <a name="phrase-lists"></a>Woordgroepenlijsten

[Lijsten met zin](luis-concept-feature.md) kunt u voor het definiëren van woordenlijsten van woorden met betrekking tot uw app-domein. Seed uw woordgroepen lijst met enkele woorden en gebruik vervolgens de functie suggereren, zodat LUIS meer woorden in de vocabulaire specifiek voor uw app kent. Een woordgroepen lijst verbetert de detectie van de intentie en de classificatie van entiteiten door het signaal te verhogen dat is gekoppeld aan woorden of zinsdelen die belang rijk zijn voor uw app. 

Elke woord niet worden toegevoegd aan het vocabulaire, omdat de woordgroepenlijst met niet een exacte overeenkomst. 

Voor meer informatie:
* Begrip [Woordgroepen lijst functies in uw LUIS-app](luis-concept-feature.md)
* Uitleg: [Woordgroepen lijsten gebruiken om het signaal van een woorden lijst te verhogen](luis-how-to-add-features.md)

### <a name="patterns"></a>Patronen

Real-user-uitingen van het eindpunt, vergelijkbaar met elkaar worden verbonden, waarschijnlijk patronen van word keuze en plaatsing. De [patroon](luis-concept-patterns.md) functie neemt dit woord keuze en plaatsing samen met de reguliere expressies voor het verbeteren van de nauwkeurigheid van de voorspelling. Een reguliere expressie in het patroon kan woorden en leestekens die u van plan bent om te negeren terwijl nog steeds die overeenkomt met het patroon. 

De [optionele syntaxis](luis-concept-patterns.md) van het patroon gebruiken voor lees tekens zodat interpunctie kan worden genegeerd. Gebruik de [lijst expliciet](luis-concept-patterns.md#explicit-lists) om het patroon te compenseren. alle syntaxis problemen. 

Voor meer informatie:
* Begrip [Nauw keurigheid van de voor spelling verbeteren](luis-concept-patterns.md)
* Uitleg: [Patronen toevoegen om de nauw keurigheid van de voor spelling te verbeteren](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>Uw uitingen in balans brengen voor alle intenties

Om ervoor te zorgen dat LUIS-voor spellingen nauw keurig zijn, moet de hoeveelheid voor beeld uitingen in elke intentie (met uitzonde ring van het geen doel) relatief gelijk zijn. 

Als u een intentie hebt met 100-voor beeld-uitingen en een intentie met 20 voor beeld-uitingen, heeft de utterance-intentie van 100 een hoger tempo van voor spelling.  

## <a name="do-add-example-utterances-to-none-intent"></a>Voeg voorbeeld uitingen op None intentie

Dit is de terugval intentie, aangeduid met alles buiten uw toepassing. Een voorbeeld utterance toevoegen aan de intentie voor elke uitingen 10 voorbeeld in de rest van uw LUIS-app geen.

Voor meer informatie:
* Begrip [Begrijpen wat goede uitingen zijn voor uw LUIS-app](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Gebruikmaken van de functie voor suggesties voor actief leren

Gebruik [actief leren](luis-how-to-review-endpoint-utterances.md)van **bekijken eindpunt uitingen** regelmatig, in plaats van meer voorbeeld uitingen aan intents toe te voegen. Omdat de app voortdurend eindpunt uitingen ontvangt, wordt deze lijst groeit en wijzigen.

Voor meer informatie:
* Begrip [Concepten voor het inschakelen van actief leren door eind punt uitingen te controleren](luis-concept-review-endpoint-utterances.md)
* Zelfstudie: [Zelfstudie: Herstel onzekere voor spellingen door eind punt uitingen te controleren](luis-tutorial-review-endpoint-utterances.md)
* Uitleg: [Endpoint uitingen in de LUIS-Portal bekijken](luis-how-to-review-endpoint-utterances.md)

## <a name="do-monitor-the-performance-of-your-app"></a>De prestaties van uw app controleren

Bewaak de nauw keurigheid van de voor spelling met behulp van een [batch-testset](luis-concept-batch-test.md) . 

## <a name="dont-add-many-example-utterances-to-intents"></a>Veel voorbeeld uitingen aan intents niet toevoegen

Nadat de app is gepubliceerd, kunt u alleen uitingen van actief leren in een iteratief proces toevoegen. Als uitingen te lijken, voegt u een patroon toe. 

## <a name="dont-use-luis-as-a-training-platform"></a>LUIS niet gebruiken als platform voor training

LUIS is specifiek voor een taalmodel-domein. Het is niet bedoeld om te werken als een algemeen platform voor het trainen van natuurlijke taal. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Voeg niet veel voorbeeld uitingen van dezelfde indeling, andere indelingen worden genegeerd

LUIS verwacht variaties in uitingen van een doel. De uitingen kunnen variëren terwijl dezelfde algemene betekenis. Variaties kunnen opnemen utterance lengte, word keuze en word plaatsing. 

|Gebruik niet dezelfde indeling|Gebruik verschillende opmaak|
|--|--|
|Een ticket naar Seattle kopen<br>Een ticket naar Parijs kopen<br>Een ticket naar Orlando kopen|1 ticket naar Seattle kopen<br>Reserveren van twee seats op de rode ogen naar Parijs volgende maandag<br>Ik wil graag 3 tickets te Orlando spring pauze boek|

De tweede kolom maakt gebruik van verschillende bewerkingen (kopen, reserveren, book), verschillende aantallen (1, twee, 3), en verschillende structuren van woorden, maar alle ook hetzelfde doel van het aanschaffen van vliegtickets voor reizen hebben. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>De definitie van de intenties en entiteiten niet combineren

Maak een doel voor alle acties die uw bot duurt. Entiteiten gebruiken als parameters die deze actie mogelijk maken. 

Voor een chatbot die vluchten wordt het adresboek, maakt u een **BookFlight** intentie. Maak geen een doel voor elke luchtvaartmaatschappij of elke bestemming. Gebruik van deze onderdelen van de gegevens als [entiteiten](luis-concept-entity-types.md) en deze te markeren in de voorbeeld-uitingen. 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Maak geen zin lijsten met alle mogelijke waarden

Vindt u enkele voorbeelden in de [lijsten zin](luis-concept-feature.md) , maar niet elk woord. LUIS generaliseert en context rekening wordt gehouden. 

## <a name="dont-add-many-patterns"></a>Veel patronen niet toevoegen

Voeg niet te veel [patronen](luis-concept-patterns.md). LUIS is bedoeld voor meer informatie over snel met minder voorbeelden. Het systeem niet onnodig worden overbelast.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Geen trainen en publiceren met elk één voorbeeld utterance

10 of 15 utterances toevoegen voordat u training en publiceren. Hierdoor kunt u de impact op de nauwkeurigheid zien. Een enkele utterance toe te voegen, kan niet een zichtbaar invloed hebben op de score. 

## <a name="do-use-versions-for-each-app-iteration"></a>Versies voor elke app-iteratie gebruiken

Elke ontwerp cyclus moet zich in een nieuwe [versie](luis-concept-version.md)bevinden, die is gekloond van een bestaande versie. LUIS heeft geen limiet voor versies. Een versie naam wordt gebruikt als onderdeel van de API-route zodat het belang rijk is om in een URL toegestane tekens te kiezen en binnen het aantal tien tekens voor een versie te houden. Ontwikkel een strategie voor de versie naam om uw versies te organiseren. 

Voor meer informatie:
* Begrip [Meer informatie over hoe en wanneer u een LUIS-versie gebruikt](luis-concept-version.md)
* Uitleg: [Versies gebruiken om te bewerken en te testen zonder dat dit van invloed is op staging-of productie-apps](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [uw app plannen](luis-how-plan-your-app.md) in uw LUIS-app.
