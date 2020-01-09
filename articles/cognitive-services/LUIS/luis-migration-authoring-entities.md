---
title: Migreren naar op v3 machine geleerde entiteit
titleSuffix: Azure Cognitive Services
description: De V3-ontwerp functie biedt een nieuw entiteits type, de door de machine geleerde entiteit, en de mogelijkheid om relaties toe te voegen aan de door de machine geleerde entiteit en andere entiteiten of functies van de toepassing.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563832"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migreren naar v3-ontwerp entiteit

De V3-ontwerp functie biedt een nieuw entiteits type, de door de machine geleerde entiteit, en de mogelijkheid om relaties toe te voegen aan de door de machine geleerde entiteit en andere entiteiten of functies van de toepassing.

## <a name="entities-are-decomposable-in-v3"></a>Entiteiten kunnen worden ontstelbaar in v3

Entiteiten die zijn gemaakt met de V3-ontwerp-Api's, met behulp van de [api's](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) of met de [Preview-Portal](https://preview.luis.ai/), bieden u de mogelijkheid om een gelaagd entiteits model te bouwen met een bovenliggend item en onderliggende items. Het bovenliggende item is bekend als de door de **machine geleerde entiteit** en de onderliggende items worden aangeduid als **subonderdelen** van de door de machine geleerde entiteit.

Elk subonderdeel is ook een door een machine geleerde entiteit, maar met de toegevoegde configuratie opties van beperkingen en descriptors.

* **Beperkingen** zijn exacte tekst vergelijkings regels die garanderen dat een entiteit wordt geëxtraheerd wanneer deze overeenkomt met een regel. De regel wordt gedefinieerd door een exacte tekst overeenkomende entiteit, momenteel: een [vooraf samengestelde entiteit](luis-reference-prebuilt-entities.md), een [reguliere expressie-entiteit](reference-entity-regular-expression.md)of een [lijst entiteit](reference-entity-list.md).
* **Descriptors** zijn [functies](luis-concept-feature.md), zoals woordgroepen lijsten of entiteiten, die worden gebruikt om de entiteit sterk aan te geven.

De V3-ontwerp functie biedt een nieuw entiteits type, de door de machine geleerde entiteit, en de mogelijkheid om relaties toe te voegen aan de door de machine geleerde entiteit en andere entiteiten of functies van de toepassing.

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Hoe kunnen deze nieuwe relaties worden vergeleken met v2-ontwerpen

V2-ontwerping heeft hiërarchische en samengestelde entiteiten, samen met rollen en functies, voorzien om deze zelfde taak uit te voeren. Omdat de entiteiten, functies en rollen niet expliciet zijn gerelateerd aan elkaar, is het moeilijk om te begrijpen hoe LUIS de relaties tijdens de voor spelling heeft geïmpliceerd.

Met v3 is de relatie expliciet en ontworpen door de auteur van de app. Zo kunt u, als auteur van de app,:

* Bekijk hoe LUIS deze relaties voordicteert, in het voor beeld uitingen
* Testen op deze relaties met het [interactieve test venster](luis-interactive-test.md) of op het eind punt
* Gebruik deze relaties in de client toepassing via een goed gestructureerd, benoemd, genest [. json-object](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Planning

Wanneer u migreert, moet u rekening houden met het volgende in uw migratie plan:

* Maak een back-up van uw LUIS-app en voer de migratie uit op een afzonderlijke app. Wanneer u een v2-en V3-app tegelijkertijd beschikbaar hebt, kunt u de vereiste wijzigingen en de invloed op de Voorspellings resultaten valideren.
* Huidige metrische voor spelling slagen vastleggen
* Huidige dashboard gegevens vastleggen als moment opname van de app-status
* Bestaande intenties, entiteiten, woordgroepen lijsten, patronen en batch tests controleren
* De volgende elementen kunnen **zonder wijziging**worden gemigreerd:
    * Intents
    * Entiteiten
        * Een entiteit in de vorm van een reguliere expressie
        * Lijstentiteit
    * Functies
        * Woordgroepen lijst
* De volgende elementen moeten worden gemigreerd **met wijzigingen**:
    * Entiteiten
        * Hiërarchische entiteit
        * Samengestelde entiteit
    * Rollen: rollen kunnen alleen worden toegepast op een door een machine geleerde (bovenliggende) entiteit. Rollen kunnen niet worden toegepast op subonderdelen
    * Batch tests en patronen die gebruikmaken van de hiërarchische en samengestelde entiteiten

Wanneer u uw migratie plan ontwerpt, laat u de laatste door de machine geleerde entiteiten na dat alle hiërarchische en samengestelde entiteiten zijn gemigreerd. Terwijl u een rechte migratie kunt uitvoeren, kunt u, nadat u de wijziging hebt aangebracht en de resultaten van de batch test en voor spellingen hebt gecontroleerd, de meer Unified JSON ertoe leiden dat u wijzigingen aanbrengt zodat de laatste gegevens die aan de client zijde worden geleverd, anders zijn ingedeeld. Dit is vergelijkbaar met het herstructureren van code en moet worden behandeld met hetzelfde controle proces als uw organisatie.

Als u geen batch tests hebt voor uw v2-model en u de batch tests naar het v3-model wilt migreren als onderdeel van de migratie, kunt u niet valideren hoe de migratie van invloed is op de Voorspellings resultaten van het eind punt.

## <a name="migrating-from-v2-entities"></a>Migreren van v2-entiteiten

Wanneer u begint met het maken van een v3-ontwerp model, moet u overwegen hoe u kunt overstappen op de door de machine geleerde entiteit en de bijbehorende subonderdelen, inclusief de beperkingen en descriptors.

In de volgende tabel ziet u welke entiteiten moeten worden gemigreerd van een v2 naar een v3-entiteits ontwerp.

|V2-entiteits type voor ontwerpen|Type v3-ontwerp entiteit|Voorbeeld|
|--|--|--|
|Samengestelde entiteit|Door machine geleerde entiteit|[Meer informatie](#migrate-v2-composite-entity)|
|Hiërarchische entiteit|Rol van door de computer geleerde entiteit|[Meer informatie](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>V2 samengestelde entiteit migreren

Elk onderliggend element van de v2-samengestelde waarde moet worden weer gegeven met een subonderdeel van de op v3 machine geleerde entiteit. Als het samengestelde onderliggende element een vooraf samengestelde, reguliere expressie of een lijst entiteit is, moet dit worden toegepast als een **beperking** voor het subonderdeel dat het onderliggende item vertegenwoordigt.

Overwegingen bij het plannen van het migreren van een samengestelde entiteit naar een door de machine geleerde entiteit:
* Onderliggende entiteiten kunnen niet worden gebruikt in patronen
* Onderliggende entiteiten worden niet langer gedeeld
* Onderliggende entiteiten moeten worden gelabeld als ze worden gebruikt als niet-machine-geleerd

### <a name="existing-descriptors"></a>Bestaande descriptoren

Een woordgroepen lijst voor het boosten van woorden in de samengestelde entiteit moet worden toegepast als een descriptor voor de door de machine geleerde (bovenliggende) entiteit, de entiteit subonderdeel (onderliggende) of het doel (als de lijst met zinsdelen alleen van toepassing is op één intentie). Plan de descriptor toe te voegen aan de entiteit die het belangrijkst moet worden verhoogd. Voeg de descriptor niet algemeen toe aan de entiteit die is geleerd van de computer (bovenliggend), als de voor spelling van een subonderdeel (onderliggend element) het meest beduidend wordt verhoogd.

### <a name="new-descriptors"></a>Nieuwe descriptoren

Voeg in v3 ontwerpen een plannings stap toe om entiteiten te evalueren als mogelijke descriptors voor alle entiteiten en intenties.

### <a name="example-entity"></a>Voorbeeld entiteit

Deze entiteit is alleen een voor beeld. De migratie van uw eigen entiteit vereist mogelijk andere overwegingen.

Denk na over een combi natie van v2 voor het wijzigen van een pizza `order` die gebruikmaakt van:
* vooraf gebouwde datetimeV2 voor leverings tijd
* woordgroepen lijst om bepaalde woorden, zoals pizza, cirkel, crust en topping, te stimuleren
* lijst entiteit voor het detecteren van toppings zoals paddestoelen, olijven, pepperoni.

Een voor beeld van een utterance voor deze entiteit is:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

In de volgende tabel ziet u de migratie:

|V2-modellen|V3-modellen|
|--|--|
|Bovenliggende-onderdeel entiteit met de naam `Order`|De entiteit met de naam van de bovenliggende en door de machine geleerde `Order`|
|Kinder datetimeV2|* Vooraf gebouwde entiteit migreren naar een nieuwe app.<br>* Voeg een beperking toe op het bovenliggende item voor prebuiled datetimeV2.|
|Onderliggende lijst entiteit voor toppings|* Migreer de lijst entiteit naar een nieuwe app.<br>* Voeg vervolgens een beperking toe voor het bovenliggende item voor de lijst entiteit.|


## <a name="migrate-v2-hierarchical-entity"></a>Hiërarchische v2-entiteit migreren

Bij het ontwerpen van v2 werd een hiërarchische entiteit gemaakt vóór de functies bestaande in LUIS. Beide hebben hetzelfde doel om entiteiten te extra heren op basis van context gebruik. Als u hiërarchische entiteiten hebt, kunt u deze als eenvoudige entiteiten met rollen beschouwen.

In v3 ontwerpen:
* Een rol kan worden toegepast op de entiteit die is geleerd van de machine.
* Een rol kan niet worden toegepast op subonderdelen.

Deze entiteit is alleen een voor beeld. De migratie van uw eigen entiteit vereist mogelijk andere overwegingen.

Overweeg een hiërarchische v2-entiteit voor het wijzigen van een pizza `order`:
* waarbij elk onderliggend element een oorspronkelijke topping of de uiteindelijke topping bepaalt

Een voor beeld van een utterance voor deze entiteit is:

`Change the topping from mushrooms to olives`

In de volgende tabel ziet u de migratie:

|V2-modellen|V3-modellen|
|--|--|
|Bovenliggende-onderdeel entiteit met de naam `Order`|De entiteit met de naam van de bovenliggende en door de machine geleerde `Order`|
|Onderliggende hiërarchische entiteit met oorspronkelijke en laatste Pizza-Topping|* Voeg een rol toe aan `Order` voor elke topping.|

## <a name="next-steps"></a>Volgende stappen

* [Bronnen voor ontwikkel aars](developer-reference-resource.md)
