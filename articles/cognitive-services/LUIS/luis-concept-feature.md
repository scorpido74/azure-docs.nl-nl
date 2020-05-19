---
title: Functies-LUIS
description: Voeg functies toe aan een taal model om tips te bieden over het herkennen van de invoer die u wilt labelen of classificeren.
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 906876e39eb7ff31c2e6b954d1514d8afc50bf3a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591893"
---
# <a name="machine-learning-ml-features"></a>Functies voor machine learning (ML)

In machine learning is een **functie**   een onderscheiding van eigenschappen of kenmerken van gegevens die uw systeem observeert.

Machine learning-functies geven LUIS belang rijke aanwijzingen voor waar u kunt zoeken naar dingen die een concept zullen onderscheiden. Ze zijn hints die LUIS kunnen gebruiken, maar geen vaste regels.  Deze hints worden gebruikt in combi natie met de labels om de gegevens te vinden.

 LUIS ondersteunt beide woordgroepen lijsten en het gebruik van andere entiteiten als functies:
* Functie woordgroepen lijst
* Model (intentie of entiteit) als onderdeel

Functies moeten worden beschouwd als een vereist deel van het schema ontwerp.

## <a name="a-phrase-list-for-a-particular-concept"></a>Een woordgroepen lijst voor een bepaald concept

Een woordgroepen lijst is een lijst met woorden of zinsdelen waarmee een bepaald concept wordt ingekapseld.

Wanneer u een woordgroepen lijst toevoegt, kunt u de functie als volgt instellen:
* **[Global](#global-features)**. Een globale functie is van toepassing op de hele app.

### <a name="when-to-use-a-phrase-list"></a>Wanneer u een woordgroepen lijst gebruikt

Wanneer u uw LUIS-app nodig hebt om nieuwe items voor het concept te generaliseren en te identificeren, gebruikt u een woordgroepen lijst. Woordgroepen lijsten zijn net als een Documentspecifieke woorden lijst waarmee u de kwaliteit van het memorandum van intenties en entiteiten kunt verbeteren.

### <a name="how-to-use-a-phrase-list"></a>Een woordgroepen lijst gebruiken

Met een woordgroepen lijst, beschouwt LUIS context en generaliseert om items te identificeren die vergelijkbaar zijn met, maar niet exact overeenkomen met de tekst.

Stappen voor het gebruik van een woordgroepen lijst:
* Beginnen met een door de machine geleerde entiteit
    * Voorbeelden van utterances toevoegen
    * Label met een door de machine geleerde entiteit
* Een woordgroepen lijst toevoegen
    * Woorden met een vergelijk bare betekenis toevoegen: Voeg **niet** alle mogelijke woorden of woord groepen toe. Voeg in plaats daarvan enkele woorden of woord groepen tegelijk toe, vervolgens opnieuw te trainen en te publiceren.
    * Voorgestelde woorden controleren en toevoegen

### <a name="a-typical-scenario-for-a-phrase-list"></a>Een typisch scenario voor een woordgroepen lijst

Een typisch scenario voor een woordgroepen lijst is het verhogen van woorden die betrekking hebben op een specifiek idee.

Een voor beeld van woorden waarvoor een woordgroepen lijst nodig is om hun significantie te stimuleren, zijn medische voor waarden. De voor waarden kunnen specifieke fysische, chemische, therapeutische of abstracte betekenis hebben. LUIS weet niet welke voor waarden belang rijk zijn voor uw onderwerps domein zonder een woordgroepen lijst.

Als u de medische voor waarden wilt extra heren:
* Maak eerst een voor beeld van uitingen en label medische voor waarden binnen deze uitingen.
* Maak vervolgens een woordgroepen lijst met voor beelden van de voor waarden binnen het onderwerps domein. Deze woordgroepen lijst moet de werkelijke term bevatten die u hebt gelabeld en andere termen die hetzelfde concept beschrijven.
* Voeg de woordgroepen lijst toe aan de entiteit of subentiteit die het concept extraheert dat in de woordgroepen lijst wordt gebruikt. Het meest voorkomende scenario is een onderdeel (onderliggend) van een door de machine geleerde entiteit. Als de lijst met zinsdelen moet worden toegepast op alle intenties of entiteiten, markeert u de woordgroepen lijst als een globale woordgroepen lijst. De `enabledForAllModels` vlag bepaalt dit model bereik in de API.

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>Een model als functie helpt een ander model

U kunt een model (intentie of entiteit) toevoegen als onderdeel van een ander model (intentie of entiteit). Door een bestaande intentie of entiteit als functie toe te voegen, voegt u een duidelijk gedefinieerd concept toe met gelabelde voor beelden.

Wanneer u een model toevoegt als onderdeel, kunt u de functie als volgt instellen:
* **[Vereist](#required-features)**. Er moet een vereiste functie worden gevonden om het model te retour neren van het Voorspellings eindpunt.
* **[Global](#global-features)**. Een globale functie is van toepassing op de hele app.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Wanneer een entiteit moet worden gebruikt als een functie voor een intentie

Voeg een entiteit als een functie toe aan een intentie wanneer de detectie van die entiteit significant is voor het doel.

Als de bedoeling bijvoorbeeld is voor het reserveren van een vlucht, `BookFlight` en de entiteit ticket gegevens bevat (zoals het aantal seats, Origin en Destination), moet de entiteit ticket gegevens een significant gewicht toevoegen aan de voor spelling van de `BookFlight` bedoeling.

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Wanneer een entiteit moet worden gebruikt als een functie voor een andere entiteit

Een entiteit (A) moet worden toegevoegd als een functie aan een andere entiteit (B) wanneer de detectie van die entiteit (A) belang rijk is voor de voor spelling van een entiteit (B).

Als bijvoorbeeld de entiteit n Shipping Address een straat subentiteit bevat, wordt door het zoeken naar de subentiteit straat een aanzienlijk gewicht toegevoegd aan de voor spelling voor de entiteit van het verzend adres.

* Verzend adres (geleerde computer entiteit)
    * Straat nummer (subentiteit)
    * Adres (subentiteit)
    * Plaats (subentiteit)
    * Staat of provincie (subentiteit)
    * Land (subentiteit)
    * Post code (subentiteit)

## <a name="required-features"></a>Vereiste onderdelen

Er moet een vereiste functie worden gevonden om het model te retour neren van het Voorspellings eindpunt. Gebruik een vereiste functie wanneer u weet dat uw inkomende gegevens moeten overeenkomen met de functie.

**Een vereiste functie maakt gebruik van een niet-computer geleerde entiteit**:
* Een entiteit in de vorm van een reguliere expressie
* Lijstentiteit
* Vooraf gebouwde entiteit

Wat zijn goede functies om te markeren als vereist? Als u zeker weet dat uw model wordt gevonden in de gegevens, stelt u de functie in zoals vereist. Een vereiste functie retourneert niets, als deze niet wordt gevonden.

U kunt door gaan met het voor beeld van het verzend adres:
* Verzend adres (geleerde computer entiteit)
    * Straat nummer (subentiteit)
    * Adres (subentiteit)
    * Straat naam (subentiteit)
    * Plaats (subentiteit)
    * Staat of provincie (subentiteit)
    * Land (subentiteit)
    * Post code (subentiteit)

### <a name="required-feature-using-prebuilt-entities"></a>Vereiste functie voor het gebruik van vooraf gemaakte entiteiten

De plaats, de staat en het land zijn doorgaans een gesloten set lijsten, wat betekent dat ze niet veel in de loop van de tijd veranderen. Deze entiteiten kunnen de relevante aanbevolen functies hebben en deze functies kunnen worden gemarkeerd als vereist. Dit betekent dat het hele verzend adres niet wordt geretourneerd. de entiteiten met de vereiste onderdelen zijn niet gevonden.

Wat gebeurt er als de stad, staat of land zich in de utterance bevindt, maar een locatie of slang die LUIS niet verwacht? Als u een aantal post verwerking wilt bieden bij het oplossen van de entiteit, vanwege een score met een lage betrouw baarheid van LUIS, moet u de functie niet als vereist markeren.

Een ander voor beeld van een vereiste functie voor het verzend adres is het maken van het straat nummer een verplicht [vooraf gebouwd](luis-reference-prebuilt-entities.md) nummer. Hiermee kan een gebruiker "1 micro soft Way" of "One micro soft Way" invoeren. Beide worden omgezet naar een aantal ' 1 ' voor de subentiteit straat nummer.

### <a name="required-feature-using-list-entities"></a>Vereiste functie voor het gebruik van lijst entiteiten

Een [lijst entiteit](reference-entity-list.md) wordt gebruikt als een lijst met canonieke namen en de bijbehorende synoniemen. Als een vereiste functie als de utterance geen canonieke naam of synoniem bevat, wordt de entiteit niet geretourneerd als onderdeel van het Voorspellings eindpunt.

Als u doorgaat met het verzend adres, wordt uw bedrijf alleen naar een beperkt aantal landen verzonden. U kunt een lijst entiteit maken die bestaat uit verschillende manieren waarop uw klant kan verwijzen naar het land. Als LUIS geen exacte overeenkomst in de tekst van de utterance vindt, wordt de entiteit (die de vereiste functie van de lijst entiteit bevat) niet geretourneerd in de voor spelling.

|Canonieke naam|Synoniemen|
|--|--|
|Verenigde Staten|U.S.<br>U. S. A<br>VS<br>VS<br>0|

De client toepassing, zoals een chat-bot, kan een vervolg vraag stellen, zodat de klant begrijpt dat de selectie van het land beperkt is en _vereist_is.

### <a name="required-feature-using-regular-expression-entities"></a>Vereiste functie voor het gebruik van reguliere expressie-entiteiten

Een [reguliere expressie-entiteit](reference-entity-regular-expression.md) die wordt gebruikt als een vereiste functie biedt uitgebreide tekst-overeenkomende mogelijkheden.

Als u doorgaat met het verzend adres, kunt u een reguliere expressie maken waarmee de syntaxis regels van de post codes van het land worden vastgelegd.

## <a name="global-features"></a>Algemene functies

Hoewel het meest wordt gebruikt om een functie toe te passen op een specifiek model, kunt u de functie configureren als een **globale functie** om deze toe te passen op uw hele toepassing.

Het meest voorkomende gebruik voor een globale functie is het toevoegen van een extra woorden lijst, zoals woorden uit een andere taal, aan de app. Als uw klanten een primaire taal gebruiken, maar u verwacht een andere taal in dezelfde utterance te kunnen gebruiken, kunt u een functie toevoegen die woorden uit de secundaire taal bevat.

Omdat de gebruiker de tweede taal voor een wille keurige intentie of entiteit had verwacht, moet deze worden toegevoegd aan een woordgroepen lijst met de woordgroepen lijst geconfigureerd als globale functie.

## <a name="best-practices"></a>Aanbevolen procedures
Lees de [Aanbevolen procedures](luis-concept-best-practices.md).

## <a name="next-steps"></a>Volgende stappen

* Uw app-modellen [uitbreiden](schema-change-prediction-runtime.md) bij de Voorspellings runtime
* Zie [onderdelen toevoegen](luis-how-to-add-features.md) voor meer informatie over het toevoegen van functies aan uw Luis-app.
