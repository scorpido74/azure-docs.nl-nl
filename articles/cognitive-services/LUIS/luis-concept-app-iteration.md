---
title: Iteratief app-ontwerp - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS leert het beste in een iteratieve cyclus van modelwijzigingen, utterancevoorbeelden, publicatie en het verzamelen van gegevens uit eindpuntquery's.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c1c1b2df301634a435b610c395a1a58aa5573da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74422604"
---
# <a name="iterative-app-design-for-luis"></a>Iteratief app-ontwerp voor LUIS

Een LUIS-app (Language Understanding) leert en presteert het meest efficiënt met iteratie. Hier is een typische iteratiecyclus:

* Nieuwe versie maken
* Bewerk het LUIS-app-schema. Dit omvat:
    * Intents with example utterances
    * Entiteiten
    * Functies
* Trainen, testen en publiceren
    * Test op het voorspellingseindpunt voor actief leren
* Gegevens uit eindpuntquery's verzamelen

![Ontwerpcyclus](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Een LUIS-schema maken

Het schema van een app definieert wat de gebruiker vraagt (de _intentie_ of _intentie)_ en welke delen van de intentie details bevatten _(entiteiten_genoemd) die worden gebruikt om het antwoord te bepalen. 

Het app-schema moet specifiek zijn voor de app-domeinen om relevante woorden en zinnen te bepalen en om de typische woordvolgorde te bepalen. 

Voorbeelduitingen vertegenwoordigen gebruikersinvoer, zoals herkende spraak of tekst, die de app verwacht tijdens runtime. 

Het schema vereist intenties en moet entiteiten _hebben._ 

### <a name="example-schema-of-intents"></a>Voorbeeldschema met intents

Het meest voorkomende schema is een intentieschema dat is georganiseerd met intents. Dit type schema gebruikt LUIS om de intentie van een gebruiker te bepalen. 

Het type intent-schema kan entiteiten bevatten als luis hiermee de intentie van de gebruiker kan bepalen. Een verzendentiteit (als descriptor naar een intentie) helpt LUIS bijvoorbeeld bij het bepalen van een verzendintentie. 

### <a name="example-schema-of-entities"></a>Voorbeeldschema van entiteiten

Een entiteitsschema richt zich op entiteiten, dat zijn de gegevens die worden geëxtraheerd uit gebruikersuitingen. Als een gebruiker bijvoorbeeld zou zeggen: "Ik wil drie pizza's bestellen." Er zijn twee entiteiten die zouden worden geëxtraheerd: _drie_ en _pizza's_. Deze worden gebruikt om te helpen voldoen aan de intentie, die was om een bestelling te maken. 

Voor een entiteitsschema is de intentie van de utterance minder belangrijk voor de clienttoepassing. 

Een veelgebruikte methode voor het ordenen van een entiteitsschema is om alle voorbeelduitingen toe te voegen aan de intentie **Geen.** 

### <a name="example-of-a-mixed-schema"></a>Voorbeeld van een gemengd schema

Het krachtigste en meest volwassen schema is een intentieschema met een volledig scala aan entiteiten en functies. Dit schema kan beginnen als een intentie- of entiteitsschema en worden uitgebreid tot concepten van beide, omdat de clienttoepassing die informatie nodig heeft. 

## <a name="add-example-utterances-to-intents"></a>Voorbeelduitingen toevoegen aan intents

LUIS heeft een paar voorbeelduitingen in elke **intentie**nodig. De voorbeelduitingen hebben voldoende variatie van woordkeuze en woordvolgorde nodig om te kunnen bepalen voor welke intentie de utterance is bedoeld. 

> [!CAUTION]
> Voeg geen voorbeelduitingen in bulk toe. Begin met 15 tot 30 specifieke en wisselende voorbeelden. 

Elke voorbeeldutterance moet alle vereiste gegevens bevatten om ontworpen en gelabeld met **entiteiten** **te extraheren.** 

|Sleutelelement|Doel|
|--|--|
|Intentie|**Classificeer** uitingen van gebruikers in één intentie of actie. Voorbeelden hiervan `BookFlight` `GetWeather`zijn en .|
|Entiteit|**Gegevens** extraheren uit uitingen die nodig zijn om de intentie te voltooien. Voorbeelden hiervan zijn datum en reistijd en locatie.|

Een LUIS-app kan worden ontworpen om uitingen te negeren die niet relevant zijn voor het domein van een app door de utterance toe te wijsen aan de **intentie Geen.**

## <a name="test-and-train-your-app"></a>Uw app testen en trainen

Nadat u 15 tot 30 verschillende voorbeelduitingen in elke intentie hebt, met de vereiste entiteiten die zijn gelabeld, moet u uw LUIS-app testen en [trainen.](luis-how-to-train.md) 

## <a name="publish-to-a-prediction-endpoint"></a>Publiceren naar een voorspellingseindpunt

De LUIS-app moet worden gepubliceerd, zodat deze voor u beschikbaar is in de [eindpuntregio's van de lijstvoorspelling.](luis-reference-regions.md)

## <a name="test-your-published-app"></a>Uw gepubliceerde toepassing testen

U uw gepubliceerde LUIS-app testen vanaf het HTTPS-voorspellingseindpunt. Testen vanaf het voorspellingseindpunt stelt LUIS in staat om uitingen met weinig vertrouwen te kiezen voor [beoordeling.](luis-how-to-review-endpoint-utterances.md)  

## <a name="create-a-new-version-for-each-cycle"></a>Een nieuwe versie maken voor elke cyclus

Elke versie is een momentopname in de tijd van de LUIS-app. Voordat u wijzigingen aanbrengt in de app, maakt u een nieuwe versie. Het is gemakkelijker om terug te gaan naar een oudere versie dan om te proberen intents en uitingen naar een vorige status te verwijderen.

De versie-ID bestaat uit tekens, cijfers of '.' en mag niet langer zijn dan 10 tekens.

De eerste versie (0.1) is de standaard actieve versie. 

### <a name="begin-by-cloning-an-existing-version"></a>Begin met het klonen van een bestaande versie

Kloon een bestaande versie om te gebruiken als uitgangspunt voor elke nieuwe versie. Nadat u een versie hebt gekloond, wordt de nieuwe versie de **actieve** versie. 

### <a name="publishing-slots"></a>Publicatieslots

U publiceren naar de fase en/of productieslots. Elke sleuf kan een andere versie of dezelfde versie hebben. Dit is handig voor het verifiëren van wijzigingen voordat u publiceert naar de productie, die beschikbaar is voor bots of andere LUIS-bellende apps. 

Getrainde versies zijn niet automatisch beschikbaar op het [eindpunt](luis-glossary.md#endpoint)van uw LUIS-app. U moet een versie [publiceren](luis-how-to-publish-app.md) of opnieuw publiceren om deze beschikbaar te maken op het eindpunt van uw LUIS-app. U publiceren naar **Staging** en **Productie,** zodat u twee versies van de app beschikbaar op het eindpunt. Als er meer versies van de app beschikbaar moeten zijn op een eindpunt, moet u de versie exporteren en opnieuw importeren naar een nieuwe app. De nieuwe app heeft een andere app-id.

### <a name="import-and-export-a-version"></a>Een versie importeren en exporteren

Een versie kan worden geïmporteerd op app-niveau. Die versie wordt de actieve versie en `versionId` gebruikt de versie-id in de eigenschap van het app-bestand. U ook importeren in een bestaande app, op versieniveau. De nieuwe versie wordt de actieve versie. 

Een versie kan ook worden geëxporteerd op app- of versieniveau. Het enige verschil is dat de geëxporteerde versie op app-niveau de momenteel actieve versie is, terwijl u op versieniveau elke versie kiezen die u wilt exporteren op de pagina **[Instellingen.](luis-how-to-manage-versions.md)** 

Het geëxporteerde **bestand bevat niet:**

* Machine-geleerde informatie, omdat de app wordt omgeschoold nadat deze is geïmporteerd
* Informatie bij contribuant

Als u een back-up wilt maken van uw LUIS-app-schema, exporteert u een versie vanuit de [LUIS-portal.](https://www.luis.ai/applications)

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Wijzigingen in bijdragen beheren met versies en bijdragers

LUIS gebruikt het concept van bijdragers aan een app door machtigingen op Azure-resourceniveau aan te bieden. Combineer dit concept met versiebeheer om gerichte samenwerking te bieden. 

Gebruik de volgende technieken om wijzigingen in de bijdrager in uw app te beheren.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Meerdere versies in dezelfde app beheren

Begin met [klonen](luis-how-to-manage-versions.md#clone-a-version) vanuit een basisversie voor elke auteur. 

Elke auteur brengt wijzigingen aan in zijn eigen versie van de app. Wanneer de auteur tevreden is met het model, exporteert u de nieuwe versies naar JSON-bestanden.  

Geëxporteerde apps, .json- of .lu-bestanden kunnen worden vergeleken voor wijzigingen. Combineer de bestanden om één bestand van de nieuwe versie te maken. Wijzig `versionId` de eigenschap om de nieuwe samengevoegde versie aan te geven. Importeer die versie in de oorspronkelijke app. 

Met deze methode u één actieve versie, één faseversie en één gepubliceerde versie hebben. U de resultaten van de actieve versie vergelijken met een gepubliceerde versie (fase of productie) in het [interactieve testvenster.](luis-interactive-test.md)

### <a name="manage-multiple-versions-as-apps"></a>Meerdere versies beheren als apps

[Exporteer](luis-how-to-manage-versions.md#export-version) de basisversie. Elke auteur importeert de versie. De persoon die de app importeert, is de eigenaar van de versie. Wanneer ze klaar zijn met het wijzigen van de app, exporteert u de versie. 

Geëxporteerde apps zijn JSON-geformatteerde bestanden, die kunnen worden vergeleken met de basisexport voor wijzigingen. Combineer de bestanden om één JSON-bestand van de nieuwe versie te maken. Wijzig de eigenschap **versionId** in de JSON om de nieuwe samengevoegde versie aan te duiden. Importeer die versie in de oorspronkelijke app.

Meer informatie over het schrijven van bijdragen van [bijdragers](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Eindpuntuitingen controleren om de nieuwe iteratieve cyclus te beginnen

Wanneer u klaar bent met een iteratiecyclus, u het proces herhalen. Begin met [het controleren van voorspellingseindpuntuitingen](luis-how-to-review-endpoint-utterances.md) LUIS gemarkeerd met weinig vertrouwen. Controleer deze uitingen op zowel de juiste voorspelde intentie als de juiste en volledige entiteit die is geëxtraheerd. Nadat u wijzigingen hebt beoordeeld en geaccepteerd, moet de beoordelingslijst leeg zijn.  

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [samenwerking](luis-concept-keys.md).
