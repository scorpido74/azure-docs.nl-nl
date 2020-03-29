---
title: Ontwerp met modellen - LUIS
titleSuffix: Azure Cognitive Services
description: Taalbegrip biedt verschillende soorten modellen. Sommige modellen kunnen op meerdere manieren worden gebruikt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d721ceb25b3ce2408563a0bed16457d05affe7b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219993"
---
# <a name="design-with-intent-and-entity-models"></a>Ontwerpen met intentie- en entiteitsmodellen 

Taalbegrip biedt verschillende soorten modellen. Sommige modellen kunnen op meerdere manieren worden gebruikt. 

## <a name="v3-authoring-uses-machine-teaching"></a>V3 Authoring maakt gebruik van machine teaching

LUIS stelt mensen in staat om gemakkelijk concepten te leren aan een machine. De machine kan vervolgens modellen bouwen (functionele benaderingen van concepten zoals classificaties en extractors) die kunnen worden gebruikt om intelligente toepassingen van stroom te voorzien. Hoewel LUIS wordt aangedreven door machine learning, is het niet nodig om machine learning te gebruiken. In plaats daarvan communiceren machinedocenten concepten naar LUIS door positieve en negatieve voorbeelden van het concept te tonen en uit te leggen hoe een concept moet worden gemodelleerd met behulp van andere gerelateerde concepten. Docenten kunnen luis's model ook interactief verbeteren door de voorspellingsfouten te identificeren en vast te stellen. 

## <a name="v3-authoring-model-decomposition"></a>V3 Authoring model ontbinding

LUIS ondersteunt _modelontleding_ met de V3 authoring API's, waardoor het model in kleinere delen wordt opgesplitst. Hierdoor u uw modellen bouwen met vertrouwen in hoe de verschillende onderdelen zijn gebouwd en voorspeld.

Modelontleding heeft de volgende onderdelen:

* [intenties](#intents-classify-utterances)
    * [beschrijvingen](#descriptors-are-features) die worden geleverd door functies
* [machine-geleerde entiteiten](#machine-learned-entities)
    * [subcomponenten](#entity-subcomponents-help-extract-data) (ook machine-aangeleerde entiteiten)
        * [beschrijvingen](#descriptors-are-features) die worden geleverd door functies 
        * [beperkingen](#constraints-are-text-rules) die worden geboden door niet-machine-geleerde entiteiten zoals reguliere expressies en lijsten

## <a name="v2-authoring-models"></a>V2-ontwerpmodellen

LUIS ondersteunt samengestelde entiteiten met de V2-auteurs-API's. Dit biedt een vergelijkbaar model ontbinding, maar is niet hetzelfde als V3 model ontbinding. De aanbevolen modelarchitectuur is om over te stappen op modelontbinding in de V3 authoring API's. 

## <a name="intents-classify-utterances"></a>Intents classificeren uitingen

Een intentie classificeert voorbeelduitingen om LUIS over de intentie te onderwijzen. Voorbeelduitingen binnen een intentie worden gebruikt als positieve voorbeelden van de utterance. Deze dezelfde uitingen worden gebruikt als negatieve voorbeelden in alle andere intents.

Overweeg een app die de intentie van een gebruiker moet bepalen om een boek te bestellen en een app die het verzendadres voor de klant nodig heeft. Deze app heeft twee `OrderBook` `ShippingLocation`intenties: en .

De volgende utterance is een `OrderBook` positief **voorbeeld** voor de `None` intentie en een negatief **voorbeeld** voor de `ShippingLocation` intenties: 

`Buy the top-rated book on bot architecture.`

Het resultaat van goed ontworpen intents, met hun voorbeelduitingen, is een voorspelling van hoge intenties. 

## <a name="entities-extract-data"></a>Entiteiten extraheren gegevens

Een entiteit vertegenwoordigt een eenheid gegevens die u uit de utterance wilt halen. 

### <a name="machine-learned-entities"></a>Door machines geleerde entiteiten

Een door machines aangeleerde entiteit is een entiteit op het hoogste niveau die subcomponenten bevat, die ook door de machine geleerde entiteiten zijn. 

**Gebruik een door de machine geleerde entiteit:**

* wanneer de subcomponenten nodig zijn door de clienttoepassing
* om het machine learning-algoritme te helpen entiteiten te ontleden

Elke subcomponent kan beschikken over:

* Subonderdelen
* beperkingen (entiteit met reguliere expressie of lijstentiteit)
* beschrijvingen (functies zoals een woordenlijst) 

Een voorbeeld van een door de machine geleerde entiteit is een order voor een vliegticket. Conceptueel is dit een enkele transactie met veel kleinere eenheden van gegevens, zoals datum, tijd, hoeveelheid stoelen, type stoel, zoals eerste klas of coach, herkomst locatie, bestemming locatie, en maaltijd keuze.


### <a name="entity-subcomponents-help-extract-data"></a>Subcomponenten van entiteithelpen gegevens te extraheren

Een subcomponent is een door de machine geleerde onderliggende entiteit binnen een door machines geleerde bovenliggende entiteit. 

**Gebruik de subcomponent om**:

* de delen van de door de machine geleerde entiteit (bovenliggende entiteit) te ontleden.

Het volgende vertegenwoordigt een door de machine geleerde entiteit met al deze afzonderlijke gegevens:

* TravelOrder (machine-geleerde entiteit)
    * DateTime (vooraf ingebouwde datetimeV2)
    * Locatie (machine-geleerde entiteit)
        * Oorsprong (rol gevonden door `from`context zoals)
        * Bestemming (rol gevonden via `to`context zoals)
    * Zitplaatsen (machine-geleerde entiteit)
        * Aantal (vooraf opgebouwd aantal)
        * Kwaliteit (machine-geleerde entiteit met beschrijving van de lijst van zinnen)
    * Maaltijden (machine-geleerde entiteit met beperking van lijstentiteit als voedselkeuzen)

Sommige van deze gegevens, zoals de herkomstlocatie en de bestemmingslocatie, moeten worden `from` geleerd `to`uit de context van de uiting, misschien met dergelijke bewoordingen als en . Andere delen van gegevens kunnen worden geëxtraheerd met exacte tekenreeksovereenkomsten`Vegan`( `Seattle` `Cairo`) of vooraf gebouwde entiteiten (geografieV2 van en ). 

U ontwerpt hoe de gegevens worden afgestemd en geëxtraheerd met welke modellen u kiest en hoe u ze configureert.

### <a name="constraints-are-text-rules"></a>Beperkingen zijn tekstregels

Een beperking is een regel voor het afstemmen van teksten, die wordt verstrekt door een entiteit die niet door de machine is aangeleerd, zoals de entiteit met reguliere expressie of een lijstentiteit. De beperking wordt toegepast op voorspellingstijd om de voorspelling te beperken en entiteitsresolutie te verstrekken die nodig is voor de clienttoepassing. U definieert deze regels tijdens het maken van de subcomponent. 

**Gebruik een beperking:**
* wanneer u de exacte tekst weet te extraheren.

Beperkingen zijn onder andere:

* [entiteiten met regelmatige expressie](reference-entity-regular-expression.md)
* [lijstentiteiten](reference-entity-list.md) 
* [vooraf gebouwde](luis-reference-prebuilt-entities.md) entiteiten

Als u doorgaan met het voorbeeld van het vliegticket, kunnen de luchthavencodes in een entiteit Lijst staan voor exacte tekstovereenkomsten. 

Voor een luchthavenlijst is de lijst vermelding `Seattle` voor Seattle de naam van de stad, en de synoniemen voor Seattle bevatten de luchthaven code voor Seattle, samen met de omliggende steden en steden:

|`Seattle`Entiteitssynoniemen van entiteiten weergeven|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Als u slechts 3 lettercodes voor luchthavencodes wilt herkennen, gebruikt u een reguliere expressie als beperking. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Intents versus entiteiten

Een intentie is het gewenste resultaat van de _hele_ utterance, terwijl entiteiten stukjes gegevens zijn die uit de utterance worden gehaald. Meestal zijn intents gekoppeld aan acties die de clienttoepassing moet uitvoeren en entiteiten zijn informatie die nodig is om deze actie uit te voeren. Vanuit het oogpunt van programmeren zou een intentie een methodeaanroep activeren en de entiteiten worden gebruikt als parameters voor die methodeaanroep.

Deze utterance _moet_ een intentie hebben en _kan_ entiteiten hebben:

`Buy an airline ticket from Seattle to Cairo`

Deze uiting heeft één enkele intentie:

* Het kopen van een vliegticket

Deze uiting _kan_ meerdere entiteiten hebben:

* Locaties van Seattle (herkomst) en Caïro (bestemming)
* De hoeveelheid van één ticket

## <a name="descriptors-are-features"></a>Descriptoren zijn functies

Een beschrijving is een functie die op een model wordt toegepast tijdens de training, inclusief woordgroeplijsten en entiteiten. 

**Gebruik een beschrijving wanneer u wilt:**

* de betekenis van woorden en zinnen die door de descriptor worden geïdentificeerd, te vergroten
* hebben LUIS raden nieuwe tekst of zinnen aan te bevelen voor de beschrijving
* een fout op de trainingsgegevens oplossen

## <a name="next-steps"></a>Volgende stappen

* Inzicht in [intenties](luis-concept-intent.md) en [entiteiten](luis-concept-entity-types.md). 