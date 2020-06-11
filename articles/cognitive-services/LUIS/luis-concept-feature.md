---
title: Functies-LUIS
description: Voeg functies toe aan een taal model om tips te bieden over het herkennen van de invoer die u wilt labelen of classificeren.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 823c51f0b58481e30ff54814dde03285ad094b9e
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677588"
---
# <a name="machine-learning-ml-features"></a>Functies voor machine learning (ML)

In machine learning is een **functie**   een onderscheiding van eigenschappen of kenmerken van gegevens die uw systeem observeert en doorloopt.

Machine learning-functies geven LUIS belang rijke aanwijzingen voor waar u kunt zoeken naar dingen die een concept zullen onderscheiden. Ze zijn hints die LUIS kunnen gebruiken, maar geen vaste regels.  Deze hints worden gebruikt in combi natie met de labels om de gegevens te vinden.

## <a name="what-is-a-feature"></a>Wat is een functie?

Een functie is een onderscheidende eigenschappen die kunnen worden beschreven als een functie: f (x) = y. De functie wordt gebruikt om te weten waar in het voor beeld utterance voor de onderscheidende kenmerken moet worden gezocht. Wat vindt u bij het maken van uw schema, wat weet u over het voor beeld utterance dat de eigenschappen aangeeft? Uw antwoord is uw beste gids voor het maken van functies.

## <a name="types-of-features"></a>Typen functies

 LUIS ondersteunt beide woordgroepen lijsten en modellen als functies:
* Functie woordgroepen lijst
* Model (intentie of entiteit) als onderdeel

Functies moeten worden beschouwd als een vereist deel van het schema ontwerp.

## <a name="how-you-find-features-in-your-example-utterances"></a>Hoe u functies in uw voor beeld vindt uitingen

Omdat LUIS een op talen gebaseerde toepassing is, zijn de functies gebaseerd op tekst. Kies tekst die aangeeft welke eigenschappen u wilt onderscheiden. Voor LUIS is de op tekst gebaseerde kleinste eenheid het token. Voor de Engelse taal is een token een aaneengesloten reeks, zonder spaties of lees tekens, van letters en cijfers. Een spatie is geen token.

Omdat spaties en interpunctie geen tokens zijn, richt u zich op de tekst aanwijzingen die u als functies kunt gebruiken. Vergeet niet om ook variaties van het woord op te brengen:
* meervouds formulieren
* werkwoordsvormen
* veelgebruikt
* spelling en spel fout

Moet de tekst, als een onderscheiding eigenschappen, het volgende hebben:
* Overeenkomen met een exact woord of woord groep: overweeg een reguliere expressie-entiteit toe te voegen, of een lijst entiteit als een functie voor de entiteit of het doel
* Een bekende concept, zoals datums, tijden of namen van personen, met een vooraf samengestelde entiteit gebruiken als onderdeel van de entiteit of het doel
* Nieuwe voor beelden meer informatie over de tijd: gebruik een woordgroepen lijst met enkele voor beelden van het concept als een functie voor de entiteit of het doel

## <a name="combine-features"></a>Functies combi neren

Omdat er verschillende opties zijn in de manier waarop een Trait wordt beschreven, kunt u meer dan één functie gebruiken waarmee u die eigenschappen of het concept kunt beschrijven. Een veelvoorkomende koppeling is het gebruik van een woordgroepen lijst functie en een van de entiteits typen die doorgaans worden gebruikt als onderdelen: prebuilde entiteit, reguliere expressie-entiteit of lijst entiteit.

### <a name="ticket-booking-entity-example"></a>Voor beeld van ticket reserverings entiteit

Een voor beeld: een app voor het reserveren van een vlucht met een vlucht reservering en een ticket boekings entiteit.

De ticket reserverings entiteit is een door een machine geleerde entiteit voor de vlucht bestemming. U kunt de locatie extra heren door twee functies te gebruiken:
* Woordgroepen lijst met relevante woorden `plane` , zoals,, `flight` `reservation` ,`ticket`
* Vooraf gebouwde `geographyV2` entiteit als onderdeel van de entiteit

### <a name="pizza-entity-example"></a>Voor beeld van pizza-entiteit

In een ander voor beeld kunt u een app overwegen om een pizza te best Ellen met een pizza-order intentie maken en een pizza-entiteit.

De entiteit pizza is een door de machine geleerde entiteit voor de details van de pizza. Voor het uitpakken van de details gebruikt u twee functies om het volgende te helpen:
* Woordgroepen lijst met relevante woorden `cheese` , zoals,, `crust` `pepperoni` ,`pineapple`
* Vooraf gebouwde `number` entiteit als onderdeel van de entiteit

## <a name="a-phrase-list-for-a-particular-concept"></a>Een woordgroepen lijst voor een bepaald concept

Een woordgroepen lijst is een lijst met woorden of zinsdelen waarmee een bepaald concept wordt ingekapseld en wordt toegepast als een hoofdletter gevoelige overeenkomst op het token niveau.

Wanneer u een woordgroepen lijst toevoegt, kunt u de functie als volgt instellen:
* **[Global](#global-features)**. Een globale functie is van toepassing op de hele app.

### <a name="when-to-use-a-phrase-list"></a>Wanneer u een woordgroepen lijst gebruikt

Wanneer u uw LUIS-app nodig hebt om nieuwe items voor het concept te generaliseren en te identificeren, gebruikt u een woordgroepen lijst. Woordgroepen lijsten zijn net als een Documentspecifieke woorden lijst waarmee u de kwaliteit van het memorandum van intenties en entiteiten kunt verbeteren.

### <a name="how-to-use-a-phrase-list"></a>Een woordgroepen lijst gebruiken

Met een woordgroepen lijst, beschouwt LUIS context en generaliseert om items te identificeren die vergelijkbaar zijn met, maar niet exact overeenkomen met de tekst.

Stappen voor het gebruik van een woordgroepen lijst:
* Beginnen met een machine learning-entiteit
    * Voorbeelden van utterances toevoegen
    * Label met een machine learning-entiteit
* Een woordgroepen lijst toevoegen
    * Woorden met een vergelijk bare betekenis toevoegen: Voeg **niet** alle mogelijke woorden of woord groepen toe. Voeg in plaats daarvan enkele woorden of woord groepen tegelijk toe, vervolgens opnieuw te trainen en te publiceren.
    * Voorgestelde woorden controleren en toevoegen

### <a name="a-typical-scenario-for-a-phrase-list"></a>Een typisch scenario voor een woordgroepen lijst

Een typisch scenario voor een woordgroepen lijst is het verhogen van woorden die betrekking hebben op een specifiek idee.

Een voor beeld van woorden waarvoor een woordgroepen lijst nodig is om hun significantie te stimuleren, zijn medische voor waarden. De voor waarden kunnen specifieke fysische, chemische, therapeutische of abstracte betekenis hebben. LUIS weet niet welke voor waarden belang rijk zijn voor uw onderwerps domein zonder een woordgroepen lijst.

Als u de medische voor waarden wilt extra heren:
* Maak eerst een voor beeld van uitingen en label medische voor waarden binnen deze uitingen.
* Maak vervolgens een woordgroepen lijst met voor beelden van de voor waarden binnen het onderwerps domein. Deze woordgroepen lijst moet de werkelijke term bevatten die u hebt gelabeld en andere termen die hetzelfde concept beschrijven.
* Voeg de woordgroepen lijst toe aan de entiteit of subentiteit die het concept extraheert dat in de woordgroepen lijst wordt gebruikt. Het meest voorkomende scenario is een onderdeel (onderliggend) van een machine learning-entiteit. Als de lijst met zinsdelen moet worden toegepast op alle intenties of entiteiten, markeert u de woordgroepen lijst als een globale woordgroepen lijst. De `enabledForAllModels` vlag bepaalt dit model bereik in de API.

### <a name="token-matches-for-a-phrase-list"></a>Overeenkomende tokens voor een woordgroepen lijst

Een woordgroepen lijst is van toepassing op token niveau, ongeacht het geval. In het volgende diagram ziet u hoe een woordgroepen lijst met het woord `Ann` wordt toegepast op variaties van dezelfde tekens in die volg orde.


| Token variatie van`Ann` | De woordgroepen lijst komt overeen wanneer het token wordt gevonden |
|--------------------------|---------------------------------------|
| Anne<br>Anne<br>           | Ja, token is`Ann`                  |
| Anne                    | Ja, token is`Ann`                  |
| Anne                     | Geen-token is`Anne`                  |


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
    * Land/regio (subentiteit)
    * Post code (subentiteit)

## <a name="nested-subentities-with-features"></a>Geneste subentiteiten met functies

Een door een machine geleerde subentiteit geeft aan dat er een concept voor de bovenliggende entiteit aanwezig is, of dat bovenliggende item een andere subentiteit of de bovenste entiteit is. De waarde van de subentiteit fungeert als een functie voor het bovenliggende item.

Een subentiteit kan zowel een woordgroepen lijst als een functie als een model (andere entiteit) als een functie hebben.

Als de subentiteit een woordgroepen lijst heeft, wordt hiermee de vocabulaire van het concept verhoogd, maar wordt geen informatie toegevoegd aan het JSON-antwoord van de voor spelling.

Wanneer de subentiteit een functie van een andere entiteit heeft, bevat het JSON-antwoord de geëxtraheerde gegevens van die andere entiteit.

## <a name="required-features"></a>Vereiste onderdelen

Er moet een vereiste functie worden gevonden om het model te retour neren van het Voorspellings eindpunt. Gebruik een vereiste functie wanneer u weet dat uw inkomende gegevens moeten overeenkomen met de functie.

Als de tekst van de utterance niet overeenkomt met de vereiste functie, wordt deze niet geëxtraheerd.

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
    * Land/regio (subentiteit)
    * Post code (subentiteit)

### <a name="required-feature-using-prebuilt-entities"></a>Vereiste functie voor het gebruik van vooraf gemaakte entiteiten

De plaats, de staat en het land/de regio zijn doorgaans een gesloten set lijsten, wat betekent dat ze niet veel in de loop van de tijd veranderen. Deze entiteiten kunnen de relevante aanbevolen functies hebben en deze functies kunnen worden gemarkeerd als vereist. Dit betekent dat het hele verzend adres niet wordt geretourneerd. de entiteiten met de vereiste onderdelen zijn niet gevonden.

Wat gebeurt er als de stad, staat of land/regio zich in de utterance bevindt, maar een locatie of slang die LUIS niet verwacht? Als u een aantal post verwerking wilt bieden bij het oplossen van de entiteit, vanwege een score met een lage betrouw baarheid van LUIS, moet u de functie niet als vereist markeren.

Een ander voor beeld van een vereiste functie voor het verzend adres is het maken van het straat nummer een verplicht [vooraf gebouwd](luis-reference-prebuilt-entities.md) nummer. Hiermee kan een gebruiker "1 micro soft Way" of "One micro soft Way" invoeren. Beide worden omgezet naar een aantal ' 1 ' voor de subentiteit straat nummer.

### <a name="required-feature-using-list-entities"></a>Vereiste functie voor het gebruik van lijst entiteiten

Een [lijst entiteit](reference-entity-list.md) wordt gebruikt als een lijst met canonieke namen en de bijbehorende synoniemen. Als een vereiste functie als de utterance geen canonieke naam of synoniem bevat, wordt de entiteit niet geretourneerd als onderdeel van het Voorspellings eindpunt.

Als u doorgaat met het voor beeld van het verzend adres, wordt uw bedrijf alleen naar een beperkt aantal landen/regio's verzonden. U kunt een lijst entiteit maken die bestaat uit verschillende manieren waarop uw klant kan verwijzen naar het land. Als LUIS geen exacte overeenkomst in de tekst van de utterance vindt, wordt de entiteit (die de vereiste functie van de lijst entiteit bevat) niet geretourneerd in de voor spelling.

|Canonieke naam|Synoniemen|
|--|--|
|Verenigde Staten|U.S.<br>U. S. A<br>VS<br>VS<br>0|

De client toepassing, zoals een chat-bot, kan een vervolg vraag stellen, zodat de klant begrijpt dat de selectie van het land/de regio beperkt is en _vereist_is.

### <a name="required-feature-using-regular-expression-entities"></a>Vereiste functie voor het gebruik van reguliere expressie-entiteiten

Een [reguliere expressie-entiteit](reference-entity-regular-expression.md) die wordt gebruikt als een vereiste functie biedt uitgebreide tekst-overeenkomende mogelijkheden.

Als u doorgaat met het verzend adres, kunt u een reguliere expressie maken waarmee de syntaxis regels van de post codes van land/regio worden vastgelegd.

## <a name="global-features"></a>Algemene functies

Hoewel het meest wordt gebruikt om een functie toe te passen op een specifiek model, kunt u de functie configureren als een **globale functie** om deze toe te passen op uw hele toepassing.

Het meest voorkomende gebruik voor een globale functie is het toevoegen van een extra woorden lijst, zoals woorden uit een andere taal, aan de app. Als uw klanten een primaire taal gebruiken, maar u verwacht een andere taal in dezelfde utterance te kunnen gebruiken, kunt u een functie toevoegen die woorden uit de secundaire taal bevat.

Omdat de gebruiker de tweede taal voor een wille keurige intentie of entiteit had verwacht, moet deze worden toegevoegd aan een woordgroepen lijst met de woordgroepen lijst geconfigureerd als globale functie.

## <a name="best-practices"></a>Aanbevolen procedures
Lees de [Aanbevolen procedures](luis-concept-best-practices.md).

## <a name="next-steps"></a>Volgende stappen

* Uw app-modellen [uitbreiden](schema-change-prediction-runtime.md) bij de Voorspellings runtime
* Zie [onderdelen toevoegen](luis-how-to-add-features.md) voor meer informatie over het toevoegen van functies aan uw Luis-app.
