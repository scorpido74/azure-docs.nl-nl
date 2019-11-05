---
title: Herhalend app-ontwerp-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS leert het beste in een iteratieve cyclus van model wijzigingen, utterance-voor beelden, publiceren en verzamelen van gegevens uit eindpunt query's.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 12a1f2304e4255eb9abd04ab2e2d0726066dd1e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487769"
---
# <a name="authoring-cycles-and-versions"></a>Ontwerp cycli en-versies

Uw LUIS-app leert het beste in een iteratieve cyclus van:

* nieuwe versie maken
* app-schema bewerken
    * doel stellingen met voor beeld-uitingen
    * Rijg
    * database
* leerling
* test
* publish
    * testen bij prediction-eind punt voor actief leren
* gegevens verzamelen van eindpunt query's

![Ontwerpcyclus](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Een LUIS-schema bouwen

Het app-schema is bedoeld om te definiëren wat de gebruiker vraagt (de bedoeling of intentie) en welke onderdelen van de vraag Details (entiteiten) bevatten die helpen bij het bepalen van het antwoord. 

Het app-schema moet specifiek zijn voor de app-domeinen om te bepalen welke woorden en zinsdelen er relevant zijn, evenals de standaard volgorde van woorden. 

Voor beeld-uitingen vertegenwoordigen gebruikers invoer die de app naar verwachting tijdens runtime ontvangt. 

Het schema vereist intenties en _moet entiteiten hebben_ . 

### <a name="example-schema-of-intents"></a>Voorbeeld schema van intenties

Het meest voorkomende schema is een intentie schema dat is ingedeeld met intents. Dit type schema is afhankelijk van LUIS om te bepalen wat de bedoeling van een gebruiker is. 

Dit schema type kan entiteiten hebben als de LUIS de bedoeling helpt te bepalen. Een verzend entiteit (als een descriptor voor een intentie) helpt bijvoorbeeld bij het bepalen van een verLUISings doel. 

### <a name="example-schema-of-entities"></a>Voorbeeld schema van entiteiten

Een entiteits schema is gericht op de entiteiten. Dit zijn de gegevens die uit de uitingen moeten worden opgehaald. 

De bedoeling van de utterance is minder of niet belang rijk voor de client toepassing. 

Een algemene methode voor het ordenen van een entiteits schema is het toevoegen van alle voor beeld-uitingen aan de geen intentie. 

### <a name="example-of-a-mixed-schema"></a>Voor beeld van een gemengd schema

Het meest krachtige en rijpste schema is een intentie schema met een volledig scala aan entiteiten en functies. Dit schema kan worden gestart als een intentie-of entity-schema en kan worden uitgebreid om concepten van beide toe te voegen, omdat de client toepassing die gegevens nodig heeft. 

## <a name="add-example-utterances-to-intents"></a>Voor beeld van uitingen aan intenties toevoegen

LUIS heeft een aantal voor beelden van uitingen in elk **doel**nodig. Het voor beeld uitingen vereist voldoende variatie van woord keuze en woord volgorde om te kunnen bepalen met welke intentie de utterance bedoeld is. 

> [!CAUTION]
> Voeg geen voor beeld-uitingen toe. Begin met 15 tot 30 specifieke en verschillende voor beelden. 

Elk voor beeld-utterance moet **vereiste gegevens bevatten om te kunnen worden geëxtraheerd** en gemaakt met **entiteiten**. 

|Sleutel element|Doel|
|--|--|
|Intentie|**Classificeer** gebruikers uitingen in één intentie of actie. Voor beelden zijn `BookFlight` en `GetWeather`.|
|Entiteit|**Haal** gegevens op uit utterance die nodig zijn om de bedoeling te volt ooien. Voor beelden zijn onder meer datum en tijd van reis en locatie.|

U ontwerpt uw LUIS-app voor het negeren van uitingen die niet relevant zijn voor het domein van uw app door de utterance toe te wijzen aan de **geen** intentie. 

## <a name="test-and-train-your-app"></a>Uw app testen en trainen

Wanneer u 15 tot 30 verschillende voor beeld-uitingen in elke intentie hebt, met de vereiste entiteiten met het label, moet u testen en [trainen](luis-how-to-train.md). 

## <a name="publish-to-a-prediction-endpoint"></a>Publiceren naar een Voorspellings eindpunt

Zorg ervoor dat u uw app publiceert zodat deze beschikbaar is in de [Voorspellings eindpunt regio's](luis-reference-regions.md) die u nodig hebt. 

## <a name="test-your-published-app"></a>Uw gepubliceerde toepassing testen

U kunt uw gepubliceerde LUIS-app testen vanuit het HTTPS prediction-eind punt. Door te testen vanaf het Voorspellings eindpunt kan LUIS een wille keurige uitingen met lage betrouw baarheid kiezen voor [controle](luis-how-to-review-endpoint-utterances.md).  

## <a name="create-a-new-version-for-each-cycle"></a>Een nieuwe versie maken voor elke cyclus

Versies, in LUIS, zijn vergelijkbaar met versies in traditioneel Program meren. Elke versie is een moment opname in de tijd van de app. Maak een nieuwe versie voordat u wijzigingen aanbrengt in de app. Het is eenvoudiger om terug te gaan naar een oudere versie en vervolgens om te proberen de intenties en uitingen te verwijderen uit een eerdere status.

De versie-ID bestaat uit tekens, cijfers of '. ' en mag niet langer zijn dan 10 tekens.

De eerste versie (0,1) is de standaard versie die actief is. 

### <a name="begin-by-cloning-an-existing-version"></a>Begin door een bestaande versie te klonen

Een bestaande versie klonen om te gebruiken als uitgangs punt voor de nieuwe versie. Wanneer u een versie kloont, wordt de nieuwe versie de **actieve** versie. 

### <a name="publishing-slots"></a>Publicatie sleuven
U publiceert naar de stage-en productie-sleuven. Elke sleuf kan een andere versie of dezelfde versie hebben. Dit is handig als u wijzigingen wilt controleren voordat u publiceert naar productie, die beschikbaar is voor bots of andere toepassingen voor LUIS-aanroepen. 

Getrainde versies zijn niet automatisch beschikbaar op het [eind punt](luis-glossary.md#endpoint)van uw app. U moet een versie [publiceren](luis-how-to-publish-app.md) of opnieuw publiceren zodat deze beschikbaar is op het eind punt van de app. U kunt publiceren naar **fase ring** en **productie**, zodat u twee versies van de app beschikbaar hebt op het eind punt. Als u meer versies van de app op een eind punt beschikbaar wilt stellen, moet u de versie exporteren en opnieuw importeren in een nieuwe app. De nieuwe app heeft een andere app-ID.

### <a name="import-and-export-a-version"></a>Een versie importeren en exporteren
U kunt een versie importeren op het niveau van de app. Deze versie wordt de actieve versie en gebruikt de versie-ID in de eigenschap `versionId` van het app-bestand. U kunt ook op versie niveau importeren in een bestaande app. De nieuwe versie wordt de actieve versie. 

U kunt een versie exporteren op app-of versie niveau. Het enige verschil is dat de geëxporteerde versie op app-niveau de momenteel actieve versie is op versie niveau, u kunt een wille keurige versie kiezen die u wilt exporteren op de pagina **[instellingen](luis-how-to-manage-versions.md)** . 

Het geëxporteerde bestand bevat niet:

* door de computer geleerde informatie omdat de app opnieuw is getraind nadat deze is geïmporteerd
* informatie over Inzender

Als u een back-up wilt maken van uw LUIS-app-schema, exporteert u een versie vanuit de LUIS-Portal.

## <a name="manage-contributor-changes-with-versions-and-apps"></a>Wijzigingen in Inzender beheren met versies en apps

LUIS biedt het concept van inzenders van een app door Azure-machtigingen op resource niveau te bieden. Combi neer dit concept met versie beheer om gerichte samen werking mogelijk te maken. 

Gebruik de volgende technieken om Inzender wijzigingen te beheren in uw app.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Meerdere versies binnen dezelfde app beheren
Begin door te [klonen](luis-how-to-manage-versions.md#clone-a-version), vanuit een basis versie, voor elke auteur. 

Elke auteur brengt wijzigingen aan in hun eigen versie van de app. Zodra elke auteur aan het model is voldaan, exporteert u de nieuwe versies naar JSON-bestanden.  

Geëxporteerde apps, JSON-of Lu-bestanden kunnen worden vergeleken voor wijzigingen. Combi neer de bestanden om één bestand van de nieuwe versie te maken. Wijzig de eigenschap **versionId** om de nieuwe samengevoegde versie aan te duiden. Importeer die versie in de oorspronkelijke app. 

Met deze methode kunt u één actieve versie, één fase versie en één gepubliceerde versie hebben. U kunt de resultaten van de actieve versie vergelijken met een gepubliceerde versie (fase of productie) in het [interactieve test venster](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Meerdere versies beheren als apps
De basis versie [exporteren](luis-how-to-manage-versions.md#export-version) . Elke auteur importeert de versie. De persoon die de app importeert, is de eigenaar van de versie. Wanneer de app is gewijzigd, exporteert u de versie. 

Geëxporteerde apps zijn bestanden in JSON-indeling, die kunnen worden vergeleken met de basis export voor wijzigingen. Combi neer de bestanden om één JSON-bestand van de nieuwe versie te maken. Wijzig de eigenschap **versionId** in de JSON om de nieuwe samengevoegde versie aan te duiden. Importeer die versie in de oorspronkelijke app.

Meer informatie over het ontwerpen van bijdragen van deel [nemers](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-authoring-cycle"></a>Eind punt uitingen bekijken om de nieuwe ontwerp cyclus te starten

Wanneer u klaar bent met het ontwerpen van een cyclus, kunt u het opnieuw starten. Begin met het [controleren van voorspellings eindpunt uitingen](luis-how-to-review-endpoint-utterances.md) Luis gemarkeerd met lage betrouw baarheid. Controleer deze uitingen voor zowel de juiste vooraf gedicteerde intentie als juiste en volledige entiteit geëxtraheerd. Wanneer u de wijzigingen hebt gecontroleerd en geaccepteerd, moet de controle lijst leeg zijn.  

## <a name="next-steps"></a>Volgende stappen

Leer concepten over [samen werking](luis-concept-keys.md).
