---
title: Ontwerpen met modellen-LUIS
titleSuffix: Azure Cognitive Services
description: Language Standing biedt verschillende types modellen. Sommige modellen kunnen op meer dan één manier worden gebruikt.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393808"
---
# <a name="design-with-intent-and-entity-models"></a>Ontwerpen met intentie-en entiteits modellen 

Language Standing biedt verschillende types modellen. Sommige modellen kunnen op meer dan één manier worden gebruikt. 

## <a name="v3-authoring-uses-machine-teaching"></a>V3-ontwerpen maakt gebruik van machine onderwijs

LUIS biedt mensen de mogelijkheid om eenvoudig concepten te leren aan een machine. De machine kan vervolgens modellen bouwen (functionele benaderingen van concepten zoals classificaties en uittreksels) die kunnen worden gebruikt om intelligente toepassingen uit te scha kelen. Hoewel LUIS wordt aangestuurd door machine learning, is het niet nodig om de machine learning te gebruiken. In plaats daarvan communiceren machine docenten concepten aan LUIS door positieve en negatieve voor beelden van het concept weer te geven en te verklaren hoe een concept moet worden gemodelleerd met behulp van andere gerelateerde concepten. Docenten kunnen het model van de LUIS ook interactief verbeteren door de Voorspellings fouten te identificeren en te verhelpen. 

## <a name="v3-authoring-model-decomposition"></a>Uitsplitsing van het v3-ontwerp model

LUIS ondersteunt _model ontleding_ met behulp van de V3-ontwerp-api's, waardoor het model in kleinere delen opsplitst. Zo kunt u uw modellen met vertrouwen bouwen in de manier waarop de verschillende onderdelen zijn geconstrueerd en gedicteerd.

Model ontleding heeft de volgende onderdelen:

* [intents](#intents-classify-utterances)
    * [descriptoren](#descriptors-are-features) van functies
* [door machines geleerde entiteiten](#machine-learned-entities)
    * [subonderdelen](#entity-subcomponents-help-extract-data) (ook door machines geleerde entiteiten)
        * [descriptoren](#descriptors-are-features) van functies 
        * [beperkingen](#constraints-are-text-rules) die worden gegeven door entiteiten die niet door machines zijn geleerd, zoals reguliere expressies en lijsten

## <a name="v2-authoring-models"></a>V2-modellen ontwerpen

LUIS ondersteunt samengestelde entiteiten met de v2-ontwerp-Api's. Dit biedt een vergelijk bare model samenstelling, maar is niet hetzelfde als de ontleding van V3-modellen. De aanbevolen model architectuur is om over te stappen op model ontleding in de V3-ontwerp-Api's. 

## <a name="intents-classify-utterances"></a>Intenties classificeren uitingen

Een intentie classificeert bijvoorbeeld uitingen om LUIS te leren over het doel. Een voor beeld van een uitingen binnen een intentie wordt als positieve voor beeld van de utterance gebruikt. Deze zelfde uitingen worden als negatieve voor beelden in alle andere intenties gebruikt.

Overweeg een app die de bedoeling van een gebruiker moet bepalen om een boek te best Ellen en een app die het verzend adres voor de klant nodig heeft. Deze app heeft twee intenties: `OrderBook` en `ShippingLocation`.

De volgende utterance is een **positief voor beeld** voor de `OrderBook` intentie en een **negatief voor beeld** voor de `ShippingLocation` en `None` intentie: 

`Buy the top-rated book on bot architecture.`

Het resultaat van goed ontworpen intenties, met hun voor beeld uitingen, is een hoge intentie voor spelling. 

## <a name="entities-extract-data"></a>Gegevens extra heren door entiteiten

Een entiteit vertegenwoordigt een hoeveelheid gegevens die u wilt ophalen uit de utterance. 

### <a name="machine-learned-entities"></a>Door machines geleerde entiteiten

Een door een machine geleerde entiteit is een entiteit op het hoogste niveau met subonderdelen, die ook door machines geleerde entiteiten zijn. 

**Een door de machine geleerde entiteit gebruiken**:

* Wanneer de subonderdelen vereist zijn voor de client toepassing
* de machine learning Algorithm-algoritme verbreken entiteiten

Elk subonderdeel kan het volgende hebben:

* subonderdelen
* beperkingen (reguliere expressie-entiteit of lijst entiteit)
* descriptors (functies zoals een woordgroepen lijst) 

Een voor beeld van een door de machine geleerde entiteit is een bestelling voor een vlak ticket. Conceptueel gezien is dit een enkele trans actie met veel kleinere gegevens eenheden, zoals datum, tijd, aantal stoelen, het type Seat, zoals de eerste klasse of de fase, de oorsprongs locatie, de doel locatie en de keuze van de maaltijd.


### <a name="entity-subcomponents-help-extract-data"></a>Subonderdelen van de entiteit helpen gegevens te extra heren

Een subonderdeel is een door de machine geleerd onderliggende entiteit binnen een door de machine geleerde bovenliggende entiteit. 

**Gebruik het subonderdeel voor het**volgende:

* de onderdelen van de door de machine geleerde entiteit (bovenliggende entiteit) afbreken.

Hieronder staat een door de machine geleerde entiteit met al deze afzonderlijke gegevens items:

* TravelOrder (door machine geleerde entiteit)
    * DateTime (prebuild datetimeV2)
    * Locatie (door machine geleerde entiteit)
        * Oorsprong (rol gevonden via context zoals `from`)
        * Doel (rol gevonden via context zoals `to`)
    * Seat (door machine geleerde entiteit)
        * Aantal (vooraf gebouwd nummer)
        * Kwaliteit (door machine geleerde entiteit met descriptor van woordgroepen lijst)
    * Maal tijden (door de machine geleerde entiteit met een beperking van de lijst entiteit als voedings opties)

Sommige van deze gegevens, zoals de oorsprongs locatie en doel locatie, moeten worden geleerd uit de context van de utterance, mogelijk met een dergelijke vermelding als `from` en `to`. Andere gegevens delen kunnen worden geëxtraheerd met exact overeenkomende teken reeksen (`Vegan`) of vooraf gemaakte entiteiten (geographyV2 van `Seattle` en `Cairo`). 

U ontwerpt hoe de gegevens worden vergeleken en geëxtraheerd door de modellen die u kiest en hoe u ze configureert.

### <a name="constraints-are-text-rules"></a>Beperkingen zijn tekst regels

Een beperking is een tekst vergelijkings regel, die wordt aangeboden door een entiteit die niet door een machine is geleerd, zoals de reguliere expressie-entiteit of een lijst entiteit. De beperking wordt toegepast op de Voorspellings tijd om de voor spelling te beperken en de entiteit omzetting te bieden die nodig is voor de client toepassing. U definieert deze regels tijdens het ontwerpen van het subonderdeel. 

**Een beperking gebruiken**:
* Wanneer u precies weet welke tekst u wilt ophalen.

Beperkingen zijn onder andere:

* entiteiten van [reguliere expressie](reference-entity-regular-expression.md)
* entiteiten [weer geven](reference-entity-list.md) 
* [vooraf gemaakte](luis-reference-prebuilt-entities.md) entiteiten

Als u doorgaat met het voor beeld van het vliegtuig ticket, kunnen de luchthaven codes in een lijst entiteit voor exacte tekst overeenkomsten staan. 

Voor een lijst met lucht havens is de vermelding in de lijst voor Seattle de plaatsnaam, `Seattle` en de synoniemen voor Seattle bevatten de luchthaven code voor Seattle samen met Omringende steden en steden:

|synoniemen van `Seattle` lijst entiteit|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Als u alleen 3 letter codes wilt herkennen voor luchthaven codes, gebruikt u een reguliere expressie als de beperking. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Intenties versus entiteiten

Een intentie is het gewenste resultaat van het _hele_ utterance terwijl entiteiten gegevens uit de utterance zijn geëxtraheerd. Doorgaans zijn intenties gekoppeld aan acties die de client toepassing moet uitvoeren en entiteiten zijn informatie die nodig is om deze actie uit te voeren. Vanuit een programmeer perspectief wordt een methode aanroep geactiveerd en worden de entiteiten gebruikt als para meters voor die methode aanroep.

Deze utterance _moet_ een intentie hebben en _kan_ entiteiten hebben:

`Buy an airline ticket from Seattle to Cairo`

Dit utterance heeft één intentie:

* Een vlieg ticket kopen

Deze utterance _kan_ verschillende entiteiten hebben:

* Locaties van Seattle (oorsprong) en Cairo (bestemming)
* De hoeveelheid van een enkel ticket

## <a name="descriptors-are-features"></a>Descriptors zijn functies

Een descriptor is een functie die wordt toegepast op een model tijdens de trainings tijd, inclusief woordgroepen lijsten en entiteiten. 

**Gebruik een descriptor als u het volgende wilt**doen:

* het belang van woorden en zinsdelen die worden geïdentificeerd door de descriptor verhogen
* laat LUIS nieuwe tekst of zinsdelen aanbevelen voor de descriptor
* een fout op de trainings gegevens oplossen

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [intenties](luis-concept-intent.md) en [entiteiten](luis-concept-entity-types.md). 