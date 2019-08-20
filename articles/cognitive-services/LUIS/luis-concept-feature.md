---
title: Functies-LUIS
titleSuffix: Azure Cognitive Services
description: Functies toevoegen aan een taalmodel voor tips over het herkennen van de invoer die u wilt een label of classificeren.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 829054064a21b2fb8da2bd975501bfcd0840e7eb
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624571"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Woordgroepen lijst functies in uw LUIS-app

In machine learning, een *functie* is een onderscheidende eigenschap of kenmerk van de gegevens die uw systeem toetsenbordinvoer. 

Functies toevoegen aan een taalmodel voor tips over het herkennen van de invoer die u wilt een label of classificeren. Functies helpen LUIS zowel intenties en entiteiten herkennen, maar functies zijn niet intents of entiteiten zelf. Functies mogelijk in plaats daarvan vindt u voorbeelden van de bijbehorende voorwaarden.  

## <a name="what-is-a-phrase-list-feature"></a>Wat is een functie van de lijst woordgroep?
Een woordgroepen lijst is een lijst met woorden of woord groepen die belang rijk zijn voor uw app, meer dan andere woorden in uitingen. Een woordgroepen lijst wordt toegevoegd aan de vocabulaire van het app-domein als een extra signaal voor LUIS over die woorden. LUIS leert over een van beide wordt automatisch toegepast op de andere. Deze lijst is geen entiteit met een gesloten [lijst](luis-concept-entity-types.md#types-of-entities) met exacte tekst overeenkomsten.

Woordgroepen lijsten bieden geen ondersteuning voor het opruimen van items, zodat u utterance-voor beelden kunt toevoegen die gebruikmaken van een verscheidenheid aan termen van grote woorden en zinsdelen.

## <a name="phrase-lists-help-all-models"></a>Woordgroepen lijsten helpen alle modellen

Woordgroepen lijsten zijn niet gekoppeld aan een specifieke intentie of entiteit, maar worden toegevoegd als een belang rijke versterking van alle doel stellingen en entiteiten. Het doel is om de detectie van de intentie en de entiteits classificatie te verbeteren.

## <a name="how-to-use-phrase-lists"></a>Het gebruik van een lijst met woorden

[Maak een woordgroepen](luis-how-to-add-features.md) lijst als uw app woorden of zinsdelen bevat die belang rijk zijn voor de app, zoals:

* branche voorwaarden
* slang
* afkortingen
* bedrijfsspecifieke taal
* taal die afkomstig is uit een andere taal, maar vaak wordt gebruikt in uw app
* tref woorden en zinsdelen in uw voor beeld uitingen

Wanneer u enkele woorden of zinsdelen hebt ingevoerd, gebruikt u de functie **Aanbevolen** om verwante waarden te vinden. Controleer de gerelateerde waarden voordat u deze toevoegt aan de lijst waarden van de woordgroepen.

|Lijsttype|Doel|
|--|--|
|Uitwisselbaar|Synoniemen of woorden die, wanneer ze worden gewijzigd in een ander woord in de lijst, dezelfde intentie en entiteits extractie hebben.|
|Niet-verwisselbaar|App-vocabulaire, specifiek voor uw app, meer dan over de meeste andere woorden in die taal.|

### <a name="interchangeable-lists"></a>Verwissel bare lijsten

Een lijst met verwisselbaar woordgroepen is voor waarden die synoniemen zijn. Bijvoorbeeld, als u wilt dat alle water lichamen worden gevonden en u bijvoorbeeld uitingen hebt zoals: 

* Welke steden bevinden zich dicht bij de fantastische meren? 
* Wat is de weg van meer dan Lake Havasu?
* Waar wordt de Nile gestart en beëindigd? 

Elk utterance moet worden vastgesteld voor zowel de intentie als de entiteiten ongeacht het water lichaam: 

* Welke steden bevinden zich dicht bij [bodyOfWater]?
* Wat loopt de weg langs [bodyOfWater]?
* Waar wordt de [bodyOfWater] gestart en eindigen? 

Omdat de woorden of zinsdelen voor het water lichaam synoniemen zijn en kunnen worden gebruikt door elkaar in de uitingen, gebruikt u de instelling die kan worden **gewijzigd** in de lijst met zinsdelen. 

### <a name="non-interchangeable-lists"></a>Niet-Verwissel bare lijsten

Een niet-verwisselbaar woordgroepen lijst is een signaal dat detectie naar LUIS verhoogt. De lijst met zinsdelen geeft woorden of zinsdelen aan die belang rijker zijn dan andere woorden. Dit helpt bij het bepalen van de intentie en de entiteits detectie. Stel dat u een onderwerps domein hebt, zoals een globaal traject (dat wil zeggen over cult uren, maar nog steeds in één taal). Er zijn woorden en zinsdelen die belang rijk zijn voor de app, maar die niet synoniem zijn. 

Voor een ander voor beeld gebruikt u een niet-verwisselbaar woordgroepen lijst voor zeldzame, bedrijfs eigen en vreemde woorden. LUIS is mogelijk niet worden herkend zeldzame en eigen woorden, evenals de woorden in vreemde talen (buiten de cultuur van de app). De niet-verwisselbaar instelling geeft aan dat de set zeldzaam woorden vormt een klasse die LUIS moet leren herkennen, maar ze niet synoniemen zijn of uitwisselbaar met elkaar.

Voeg niet alle mogelijke woorden of woord groepen toe aan een woordgroepen lijst, voeg enkele woorden of zinsdelen per keer toe en voer ze vervolgens opnieuw uit. 

Wanneer de lijst met zinsdelen langer duurt, zijn er mogelijk enkele formulieren (synoniemen). Verbreek deze naar een andere woordgroepen lijst die kan worden gewijzigd. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Woordgroepen lijsten helpen bij het identificeren van eenvoudige, verwissel bare entiteiten
Verwisselbaar woordgroep lijsten zijn een goede manier om af te stemmen van de prestaties van uw LUIS-app. Als uw app problemen bij het voorspellen van uitingen met de juiste intent of entiteiten herkennen heeft, moet u bedenken dat de uitingen ongebruikelijke woorden of woorden die mogelijk niet-eenduidige in betekenis bevatten. Deze woorden zijn goede kandidaten om op te nemen in een woordgroepenlijst met.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Woordgroep geeft een lijst van de help intents identificeren door beter inzicht in context
Een woordgroepenlijst is niet een instructie aan LUIS uit te voeren strikte die overeenkomen met of alle voorwaarden in de woordgroepenlijst met altijd label precies hetzelfde. Het is gewoon een hint. Bijvoorbeeld, u kunt een woordgroepenlijst die aangeeft dat "Patti" en "Selma" namen zijn hebben, maar LUIS kunt contextuele informatie nog steeds gebruiken voor het herkennen van dat ze iets anders in betekenen 'Maak een reservering voor 2 op de Patti Diner voor diner' en 'vinden me te stimuleren aanwijzingen voor het Selma, Georgië'. 

Toevoegen van een woordgroepenlijst vormt een alternatief voor het toevoegen van meer voorbeeld uitingen aan een doel. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Wanneer u lijsten woordgroep ten opzichte van de lijst met entiteiten
Hoewel zowel een woordgroepen lijst als [lijst-entiteiten](reference-entity-list.md) van invloed kunnen zijn op uitingen in alle intenties, gebeurt dit op een andere manier. Een woordgroepenlijst met gebruiken om te bepalen van intentie voorspelling score. Een lijst met entiteit gebruiken voor het extraheren van entiteiten af voor een overeenkomst exact overeenkomende tekst van invloed zijn op. 

### <a name="use-a-phrase-list"></a>Gebruik een zin-lijst
LUIS kunt nog steeds met een woordgroepenlijst rekening gehouden met context en generaliseren voor het identificeren van items die vergelijkbaar zijn met, maar niet een exacte overeenkomst als items in een lijst. Als u uw LUIS-app moet kunnen generaliseren en nieuwe items in een categorie te identificeren, gebruikt u een woordgroepenlijst met. 

Als u nieuwe exemplaren van een entiteit wilt kunnen herkennen, zoals een Meeting scheduler die de namen van nieuwe contact personen moet herkennen of een inventarisatie-app die nieuwe producten moet herkennen, moet u een ander type computer-geleerde entiteit, zoals een eenvoudige entiteit, gebruiken. Maak vervolgens een woordgroepenlijst met woorden en zinnen waarmee LUIS andere woorden die vergelijkbaar is met de entiteit zoeken. Deze lijst begeleidt LUIS zodat voorbeelden van de entiteit worden herkend door extra betekenis toe te voegen aan de waarde van deze woorden. 

Een lijst met woorden zijn domeinspecifieke-woordenlijst die u helpen bij het verbeteren van de kwaliteit van het inzicht van zowel intenties en entiteiten. Een veelvoorkomend gebruik van een woordgroepenlijst is de juiste zelfstandige naamwoorden, zoals plaatsnamen. Naam van een plaats kan meerdere woorden, inclusief afbreekstreepjes of apostroffen zijn.
 
### <a name="dont-use-a-phrase-list"></a>Gebruik niet een woordgroepenlijst met 
Een lijst met entiteiten definieert expliciet elke waarde in een entiteit kan duren, en identificeert alleen waarden die precies overeenkomen. Een lijst met entiteit mogelijk geschikt is voor een app waarin alle exemplaren van een entiteit bekend zijn en niet vaak te wijzigen. Voorbeelden zijn food items in een restaurantmenu die veelvuldig wordt gewijzigd. Als u een overeenkomst exact overeenkomende tekst van een entiteit nodig hebt, gebruik niet een woordgroepenlijst met. 

## <a name="best-practices"></a>Aanbevolen procedures
Informatie over [aanbevolen procedures](luis-concept-best-practices.md).

## <a name="next-steps"></a>Volgende stappen

Zie [onderdelen toevoegen](luis-how-to-add-features.md) voor meer informatie over hoe u functies toevoegt aan uw LUIS-app.
