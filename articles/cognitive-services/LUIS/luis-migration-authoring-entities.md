---
title: Migreren naar V3-door machines geleerde entiteit
titleSuffix: Azure Cognitive Services
description: Het V3-ontwerp biedt een nieuw entiteitstype, de door de machine geleerde entiteit, samen met de mogelijkheid om relaties toe te voegen aan de door de machine geleerde entiteit en andere entiteiten of functies van de toepassing.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75563832"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migreren naar V3-ontwerpentiteit

Het V3-ontwerp biedt een nieuw entiteitstype, de door de machine geleerde entiteit, samen met de mogelijkheid om relaties toe te voegen aan de door de machine geleerde entiteit en andere entiteiten of functies van de toepassing.

## <a name="entities-are-decomposable-in-v3"></a>Entiteiten zijn ontleedbaar in V3

Entiteiten die zijn gemaakt met de V3-ontwerp-API's, met behulp van de [API's](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) of met de [preview-portal,](https://preview.luis.ai/)stellen u in staat om een gelaagd entiteitsmodel te bouwen met een ouder en kinderen. De ouder staat bekend als de **machine-geleerde entiteit** en de kinderen staan bekend als **subcomponenten** van de machine geleerde entiteit.

Elke subcomponent is ook een machine-geleerde entiteit, maar met de toegevoegde configuratie-opties van beperkingen en beschrijvingen.

* **Beperkingen** zijn exacte regels voor tekstafstemming die garanderen dat een entiteit wordt geëxtraheerd wanneer deze overeenkomt met een regel. De regel wordt gedefinieerd door een exacte entiteit die overeenkomt met tekst , momenteel: een [vooraf gebouwde entiteit](luis-reference-prebuilt-entities.md), een entiteit met reguliere [expressie](reference-entity-regular-expression.md)of [lijstentiteit](reference-entity-list.md).
* **Descriptoren** zijn [functies](luis-concept-feature.md), zoals woordgroeplijsten of entiteiten, die worden gebruikt om de entiteit sterk aan te geven.

Het V3-ontwerp biedt een nieuw entiteitstype, de door de machine geleerde entiteit, samen met de mogelijkheid om relaties toe te voegen aan de door de machine geleerde entiteit en andere entiteiten of functies van de toepassing.

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Hoe verhouden deze nieuwe relaties zich tot V2-authoring

V2-authoring voorzag hiërarchische en samengestelde entiteiten samen met rollen en functies om dezelfde taak te volbrengen. Omdat de entiteiten, functies en rollen niet expliciet met elkaar waren gerelateerd, was het moeilijk te begrijpen hoe LUIS de relaties impliceerde tijdens de voorspelling.

Met V3 is de relatie expliciet en ontworpen door de app-auteurs. Hiermee u als auteur van de app:

* Bekijk visueel hoe LUIS deze relaties voorspelt, in de voorbeelduitingen
* Testen op deze relaties met het [interactieve testvenster](luis-interactive-test.md) of op het eindpunt
* Gebruik deze relaties in de clienttoepassing, via een goed gestructureerd, benoemd, genest [.json-object](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Planning

Wanneer u migreert, moet u rekening houden met het volgende in uw migratieplan:

* Maak een back-up van uw LUIS-app en voer de migratie uit in een aparte app. Als u tegelijkertijd een V2- en V3-app beschikbaar hebt, u de vereiste wijzigingen en de impact op de voorspellingsresultaten valideren.
* Huidige successtatistieken voor voorspellingen vastleggen
* Huidige dashboardgegevens vastleggen als momentopname van de app-status
* Bestaande intents, entiteiten, woordgroeplijsten, patronen en batchtests controleren
* De volgende elementen kunnen **zonder wijziging**worden gemigreerd:
    * Intents
    * Entiteiten
        * Een entiteit in de vorm van een reguliere expressie
        * Lijstentiteit
    * Functies
        * Lijst met zinnen
* De volgende elementen moeten worden gemigreerd **met wijzigingen:**
    * Entiteiten
        * Hiërarchische entiteit
        * Samengestelde entiteit
    * Rollen - rollen kunnen alleen worden toegepast op een machine-geleerde (bovenliggende) entiteit. Rollen kunnen niet worden toegepast op subcomponenten
    * Batchtests en -patronen die gebruikmaken van de hiërarchische en samengestelde entiteiten

Wanneer u uw migratieplan ontwerpt, laat u tijd over om de uiteindelijke door machines geleerde entiteiten te bekijken, nadat alle hiërarchische en samengestelde entiteiten zijn gemigreerd. Terwijl een rechte migratie zal werken, nadat u de wijziging hebt aangebracht en uw batchtestresultaten hebt bekeken, en voorspelling JSON, kan de meer uniforme JSON u ertoe brengen wijzigingen aan te brengen, zodat de uiteindelijke informatie die aan de client-side app wordt geleverd, anders is georganiseerd. Dit is vergelijkbaar met coderefactoring en moet worden behandeld met hetzelfde beoordelingsproces dat uw organisatie heeft.

Als u geen batchtests hebt voor uw V2-model en de batchtests naar het V3-model migreert als onderdeel van de migratie, u niet valideren hoe de migratie de resultaten van de eindpuntvoorspelling zal beïnvloeden.

## <a name="migrating-from-v2-entities"></a>Migreren van V2-entiteiten

Wanneer u begint te gaan naar het V3-ontwerpmodel, moet u overwegen hoe u naar de door de machine geleerde entiteit en de subcomponenten ervan gaan, inclusief de beperkingen en beschrijvingen.

In de volgende tabel wordt notities vastgelegd welke entiteiten moeten migreren van een V2 naar een V3-entiteitsontwerp.

|V2-auteurvan entiteitstype|V3-auteurentitytype|Voorbeeld|
|--|--|--|
|Samengestelde entiteit|Door de machine geleerde entiteit|[Meer informatie](#migrate-v2-composite-entity)|
|Hiërarchische entiteit|De rol van de machine geleerde entiteit|[Meer informatie](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>V2-composiet entiteit migreren

Elk kind van de V2-composiet moet worden vertegenwoordigd met een subcomponent van de V3-machine-geleerde entiteit. Als het samengestelde kind een vooraf gebouwde, reguliere expressie of een lijstentiteit is, moet dit worden toegepast als **een beperking** voor de subcomponent die het kind vertegenwoordigt.

Overwegingen bij het migreren van een samengestelde entiteit naar een door machines geleerde entiteit:
* Onderliggende entiteiten kunnen niet worden gebruikt in patronen
* Onderliggende entiteiten worden niet langer gedeeld
* Onderliggende entiteiten moeten worden gelabeld als ze vroeger niet-machinaal geleerd

### <a name="existing-descriptors"></a>Bestaande beschrijvingen

Elke woordenlijst die wordt gebruikt om woorden in de samengestelde entiteit te stimuleren, moet worden toegepast als beschrijving op de door de machine geleerde (bovenliggende) entiteit, de entiteit subcomponent (onderliggend) of de intentie (als de woordgroeplijst slechts op één intentie van toepassing is). Plan om de beschrijving toe te voegen aan de entiteit die het meest moet stimuleren. Voeg de beschrijving niet generiek toe aan de door de machine geleerde (bovenliggende) entiteit, als deze de voorspelling van een subcomponent (onderliggend) het sterkst zal verbeteren.

### <a name="new-descriptors"></a>Nieuwe beschrijvingen

Voeg in V3-auteuring een planningsstap toe om entiteiten te evalueren als mogelijke beschrijvingen voor alle entiteiten en intenties.

### <a name="example-entity"></a>Voorbeeldentiteit

Deze entiteit is slechts een voorbeeld. Voor uw eigen entiteitsmigratie zijn mogelijk andere overwegingen vereist.

Overweeg een V2-composiet voor `order` het wijzigen van een pizza die wordt gebruikt:
* vooraf opgebouwde datetimeV2 voor levertijd
* woordenlijst om bepaalde woorden zoals pizza, taart, korst en topping te stimuleren
* lijstentiteit om toppings zoals champignons, olijven, pepperoni te detecteren.

Een voorbeeld van uiting voor deze entiteit is:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

In de volgende tabel wordt de migratie aangetoond:

|V2-modellen|V3-modellen|
|--|--|
|Bovenliggende entiteit - Entiteit met de naam Component`Order`|Ouder - Door machines geleerde entiteit met de naam`Order`|
|Kind - Vooraf gebouwde datetimeV2|* Migreer vooraf gebouwde entiteit naar een nieuwe app.<br>* Beperking toevoegen aan bovenliggende voor vooraf gebouwde datumV2.|
|Kind - lijstentiteit voor toppings|* Migreer lijstentiteit naar nieuwe app.<br>* Voeg vervolgens een beperking toe aan de bovenliggende instantie voor de lijstentiteit.|


## <a name="migrate-v2-hierarchical-entity"></a>V2-hiërarchische entiteit migreren

In V2-auteurmaken werd een hiërarchische entiteit verstrekt voordat rollen in LUIS bestonden. Beide dienden hetzelfde doel van het extraheren van entiteiten op basis van contextgebruik. Als u hiërarchische entiteiten hebt, u ze zien als eenvoudige entiteiten met rollen.

In V3 authoring:
* Een rol kan worden toegepast op de machine-geleerde (bovenliggende) entiteit.
* Een rol kan niet worden toegepast op subcomponenten.

Deze entiteit is slechts een voorbeeld. Voor uw eigen entiteitsmigratie zijn mogelijk andere overwegingen vereist.

Overweeg een V2-hiërarchische entiteit `order`voor het wijzigen van een pizza:
* waarbij elk kind een originele topping of de laatste topping bepaalt

Een voorbeeld van uiting voor deze entiteit is:

`Change the topping from mushrooms to olives`

In de volgende tabel wordt de migratie aangetoond:

|V2-modellen|V3-modellen|
|--|--|
|Bovenliggende entiteit - Entiteit met de naam Component`Order`|Ouder - Door machines geleerde entiteit met de naam`Order`|
|Onderliggend - Hiërarchische entiteit met originele en laatste pizzatopping|* Voeg `Order` rol toe aan voor elke topping.|

## <a name="next-steps"></a>Volgende stappen

* [Bronnen voor ontwikkelaars](developer-reference-resource.md)
