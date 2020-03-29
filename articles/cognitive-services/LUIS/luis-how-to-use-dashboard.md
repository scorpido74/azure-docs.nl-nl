---
title: Dashboard - Taalbegrip - LUIS
titleSuffix: Azure Cognitive Services
description: Herstel intents en entiteiten met het dashboard van uw getrainde app. Het dashboard geeft algemene app-informatie weer, met hoogtepunten van intenties die moeten worden opgelost.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d9ae126753f55349f9bf3eefd20bc4d222866af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73888202"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Het dashboard gebruiken om uw app te verbeteren

Problemen met de intenties van uw getrainde app zoeken en oplossen wanneer u voorbeelduitingen gebruikt. Het dashboard geeft algemene app-informatie weer, met hoogtepunten van intenties die moeten worden opgelost. 

Review Dashboard analyse is een iteratief proces, herhalen als u uw model verandert en verbetert.

Op deze pagina wordt geen relevante analyse uitgevoerd voor apps die geen voorbeelduitingen in de intents hebben, bekend als _apps met alleen patronen._ 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Welke problemen kunnen worden opgelost vanuit het dashboard?

De drie problemen die in het dashboard worden aangepakt zijn:

|Probleem|Grafiekkleur|Uitleg|
|--|--|--|
|Gegevensonbalans|-|Dit gebeurt wanneer de hoeveelheid voorbeelduitingen aanzienlijk varieert. Alle intenties moeten _ongeveer_ hetzelfde aantal voorbeelduitingen hebben - behalve de intentie Geen. Het mag slechts 10%-15% van de totale hoeveelheid uitingen in de app hebben.<br><br> Als de gegevens onevenwichtig zijn, maar de nauwkeurigheid van de intentie boven bepaalde drempelwaarden ligt, wordt deze onbalans niet gerapporteerd als een probleem.<br><br>**Begin met dit probleem - het kan de oorzaak van de andere problemen.**|
|Onduidelijke voorspellingen|Orange|Dit gebeurt wanneer de bovenste intentie en de scores van de volgende intentie dicht genoeg zijn om de volgende training te spiegelen, als gevolg van [negatieve sampling](luis-how-to-train.md#train-with-all-data) of meer voorbeelduitingen die aan de intentie zijn toegevoegd. |
|Onjuiste voorspellingen|Rood|Dit gebeurt wanneer een voorbeeldutterance niet wordt voorspeld voor de gelabelde intentie (de intentie waarin deze zich bevindt).|

De juiste voorspellingen worden weergegeven met de kleur blauw.

Het dashboard toont deze problemen en vertelt u welke intents worden beïnvloed en suggereert wat u moet doen om de app te verbeteren. 

## <a name="before-app-is-trained"></a>Voordat de app wordt getraind 

Voordat u de app traint, bevat het dashboard geen suggesties voor oplossingen. Train uw app om deze suggesties te zien.  

## <a name="check-your-publishing-status"></a>Uw publicatiestatus controleren

De **publicatiestatuskaart** bevat informatie over de laatste publicatie van de actieve versie. 

Controleer of de actieve versie de versie is die u wilt oplossen. 

![Dashboard toont de externe services van de app, gepubliceerde regio's en geaggregeerde eindpunttreffers.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Dit toont ook externe services, gepubliceerde regio's en geaggregeerde eindpunttreffers. 

## <a name="review-training-evaluation"></a>Evaluatie van de opleiding herzien

De **evaluatiekaart Training** bevat het geaggregeerde overzicht van de algehele nauwkeurigheid van uw app per gebied. De score geeft intentiekwaliteit aan. 

![De evaluatiekaart Training bevat het eerste gebied met informatie over de algehele nauwkeurigheid van uw app.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

De grafiek geeft de correct voorspelde intenties en de probleemgebieden met verschillende kleuren aan. Naarmate u de app verbetert met de suggesties, neemt deze score toe. 

De voorgestelde oplossingen worden gescheiden door probleemtype en zijn de belangrijkste voor uw app. Als u problemen per intentie liever bekijkt en oplost, gebruikt u de kaart **[Intents with Errors](#intents-with-errors)** onder aan de pagina. 

Elk probleemgebied heeft intenties die moeten worden opgelost. Wanneer u de intentienaam selecteert, wordt de pagina **Intent** geopend met een filter dat op de uitingen wordt toegepast. Met dit filter u zich concentreren op de uitingen die het probleem veroorzaken.

### <a name="compare-changes-across-versions"></a>Wijzigingen tussen versies vergelijken

Maak een nieuwe versie voordat u wijzigingen aanbrengt in de app. Breng in de nieuwe versie de voorgestelde wijzigingen aan in de voorbeelduitingen van de intentie en train vervolgens opnieuw. Gebruik op de **evaluatiekaart Training** van de dashboardpagina de **wijziging weergeven van getrainde versie** om de wijzigingen te vergelijken. 

![Wijzigingen tussen versies vergelijken](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Versie oplossen door voorbeelduitingen toe te voegen of te bewerken en omte scholen

De primaire methode voor het herstellen van uw app is het toevoegen of bewerken van voorbeelduitingen en het omscholen. De nieuwe of gewijzigde uitingen moeten richtlijnen volgen voor [uiteenlopende uitingen.](luis-concept-utterance.md)

Het toevoegen van voorbeelduitingen moet worden gedaan door iemand die:

* heeft een hoge mate van begrip van wat uitingen zijn in de verschillende intents.
* weet hoe uitingen in de ene intentie kunnen worden verward met een andere intentie.
* is in staat om te beslissen of twee intenties, die vaak met elkaar worden verward, moeten worden samengevouwen in een enkele intentie. Als dit het geval is, moeten de verschillende gegevens worden uitgetrokken met entiteiten.

### <a name="patterns-and-phrase-lists"></a>Patronen en woordgroepen

De analysepagina geeft niet aan wanneer [patronen](luis-concept-patterns.md) of woordgroepen moeten worden [gebruikt.](luis-concept-feature.md) Als u ze toevoegt, kan het helpen met onjuiste of onduidelijke voorspellingen, maar helpt het niet bij de onbalans van gegevens. 

### <a name="review-data-imbalance"></a>Gegevensonbalans controleren

Begin met dit probleem - het kan de oorzaak van de andere problemen.

De lijst met intenties voor **gegevensonbalans** toont intents die meer uitingen nodig hebben om de gegevensonbalans te corrigeren. 

**Ga als volgt te werk om dit probleem op te lossen:**

* Voeg meer uitingen toe aan de intentie en train vervolgens opnieuw. 

Voeg geen uitingen toe aan de intentie Geen, tenzij dat wordt voorgesteld op het dashboard.

> [!Tip]
> Gebruik het derde gedeelte op de pagina, **Uitingen per intentie** met de instelling **Utterances (getal),** als een snelle visuele handleiding van welke intents meer uitingen nodig hebben.  
    ![Gebruik 'Uitingen (getal)' om intents te vinden met gegevensonbalans.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Onjuiste voorspellingen bekijken

De **lijst met onjuiste** voorspellingsintenties toont intenties met uitingen, die worden gebruikt als voorbeelden voor een specifieke intentie, maar worden voorspeld voor verschillende intents. 

**Ga als volgt te werk om dit probleem op te lossen:**

* Bewerk uitingen om specifieker te zijn voor de intentie en train opnieuw.
* Combineer intenties als uitingen te nauw zijn uitgelijnd en train opnieuw.

### <a name="review-unclear-predictions"></a>Bekijk onduidelijke voorspellingen

De **onduidelijke** voorspellingsintentielijst toont intenties met uitingen met voorspellingsscores die niet ver genoeg verwijderd zijn van hun naaste rivaal, dat de bovenste intentie voor de utterance kan veranderen op de volgende training, als gevolg van [negatieve bemonstering.](luis-how-to-train.md#train-with-all-data)

**Om dit probleem op te lossen**;

* Bewerk uitingen om specifieker te zijn voor de intentie en train opnieuw.
* Combineer intenties als uitingen te nauw zijn uitgelijnd en train opnieuw.

## <a name="utterances-per-intent"></a>Uitingen per intentie

Deze kaart toont de algehele app-status in de bedoelingen. Terwijl u intents en retrain vastzet, blijft u deze kaart bekijken voor problemen.

De volgende grafiek toont een goed uitgebalanceerde app met bijna geen problemen op te lossen.

![De volgende grafiek toont een goed uitgebalanceerde app met bijna geen problemen op te lossen.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

De volgende grafiek toont een slecht uitgebalanceerde app met veel problemen op te lossen.

![De volgende grafiek toont een goed uitgebalanceerde app met bijna geen problemen op te lossen.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Plaats de plaats in de balk van elke intentie om informatie over de intentie te krijgen. 

![De volgende grafiek toont een goed uitgebalanceerde app met bijna geen problemen op te lossen.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Gebruik de functie **Sorteren op** om de intenties te rangschikken op uitgiftetype, zodat u zich concentreren op de meest problematische intenties met dat probleem. 

## <a name="intents-with-errors"></a>Intents with errors

Met deze kaart u problemen met een specifieke intentie bekijken. De standaardweergave van deze kaart is de meest problematische intenties, zodat u weet waar u uw inspanningen moet richten.

![Met de intents with errors-kaart u problemen bekijken voor een specifieke intentie. De kaart wordt standaard gefilterd op de meest problematische intenties, zodat u weet waar u uw inspanningen moet richten.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

De bovenste donutgrafiek toont de problemen met de intentie voor de drie probleemtypen. Als er problemen zijn in de drie probleemtypen, heeft elk type zijn eigen grafiek hieronder, samen met eventuele rivaliserende intenties. 

### <a name="filter-intents-by-issue-and-percentage"></a>Intenties filteren op uitgifte en percentage

Met dit gedeelte van de kaart u voorbeelduitingen vinden die buiten uw foutdrempel vallen. Idealiter wilt u dat de juiste voorspellingen significant zijn. Dat percentage is zakelijk en klantgericht. 

Bepaal de drempelpercentages waarmee u zich comfortabel voelt voor uw bedrijf. 

Met het filter u intents vinden met een specifiek probleem:

|Filteren|Voorgesteld percentage|Doel|
|--|--|--|
|Meest problematische intenties|-|**Begin hier** - Het herstellen van de uitingen in deze intentie zal de app meer verbeteren dan andere oplossingen.|
|Juiste voorspellingen hieronder|60%|Dit is het percentage uitingen in de geselecteerde intentie die juist zijn, maar een betrouwbaarheidsscore onder de drempelwaarde hebben. |
|Onduidelijke voorspellingen hierboven|15%|Dit is het percentage uitingen in de geselecteerde intentie die worden verward met de dichtstbijzijnde rivaliserende intentie.|
|Bovenstaande onjuiste voorspellingen|15%|Dit is het percentage uitingen in de geselecteerde intentie die onjuist zijn voorspeld. |

### <a name="correct-prediction-threshold"></a>Juiste voorspellingsdrempel

Wat is een zelfverzekerde voorspelling vertrouwen score voor u? Aan het begin van de app-ontwikkeling kan 60% uw doelwit zijn. Gebruik de **onderstaande juiste voorspellingen** met het percentage van 60% om uitingen in de geselecteerde intentie te vinden die moeten worden opgelost.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Onduidelijke of onjuiste voorspellingsdrempel

Met deze twee filters u uitingen in de geselecteerde intentie buiten uw drempelwaarde vinden. U deze twee percentages zien als foutpercentages. Als u zich comfortabel voelt met een foutpercentage van 10-15% voor voorspellingen, stelt u de filterdrempel in op 15% om alle uitingen boven deze waarde te vinden. 

## <a name="next-steps"></a>Volgende stappen

* [Uw Azure-resources beheren](luis-how-to-azure-subscription.md)
