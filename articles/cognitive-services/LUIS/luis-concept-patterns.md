---
title: Help voor voor spelling van patronen-LUIS
titleSuffix: Azure Cognitive Services
description: Met een patroon kunt u een grotere nauwkeurigheid verkrijgen voor een intentie zonder te voorzien in veel meer uitingen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 32b9b42ee0962353405616e501de95b19e40ea1a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280856"
---
# <a name="patterns-improve-prediction-accuracy"></a>Patronen verbeteren nauwkeurigheid
Patronen zijn ontworpen voor betere nauwkeurigheid wanneer verschillende uitingen vergelijkbaar zijn.  Met een patroon kunt u een grotere nauwkeurigheid verkrijgen voor een intentie zonder te voorzien in veel meer uitingen. 

## <a name="patterns-solve-low-intent-confidence"></a>Patronen oplossen lage intentie vertrouwen
Houd rekening met een Human Resources-app die in de organisatie-grafiek ten opzichte van een werknemer-rapporten. Gezien de naam en de relatie van een werknemer, retourneert LUIS de werknemers die betrokken zijn. Houd rekening met een werknemer, Tom, met een manager naam Els en een team van onderliggende niveaus met de naam: Michael Rebecca en Carl.

![Afbeelding van organigram](./media/luis-concept-patterns/org-chart.png)

|Utterances|Bedoeling voorspeld|Intentie score|
|--|--|--|
|Wie is de Tom ondergeschikte?|GetOrgChart|.30|
|Wie is de onderliggend niveau van Tom?|GetOrgChart|.30|

Als een app heeft tussen 10 en 20 uitingen met verschillende lengtes van zin, verschillende woordvolgorde en zelfs verschillende woorden (synoniemen van 'onderliggende', 'beheren', "rapport"), opleveren LUIS met een lage betrouwbaarheidsscore. Maak een patroon om LUIS inzicht te krijgen in het belang van de woord volgorde. 

Patronen oplossen van de volgende situaties: 

* De intentie Score is laag
* De juiste intentie is niet de hoogste score, maar te dicht bij de bovenste Score. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Patronen zijn geen garantie van doel
Patronen gebruiken een combinatie van technologieën voor voorspelling. Instellen van een doel voor de utterance van een sjabloon in een patroon is geen garantie van de intentie voorspelling maar het is een sterk signaal. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>De door de machine gedetecteerde entiteits detectie worden niet door patronen verbeterd

Een patroon is hoofd zakelijk bedoeld om de voor spelling van intenties en rollen te helpen. Het _patroon. elke_ entiteit wordt gebruikt om vrije-vorm entiteiten te extra heren. Hoewel patronen gebruikmaken van entiteiten, kan een patroon niet helpen bij het detecteren van een door de machine geleerde entiteit.  

Het is niet raadzaam om verbeterde entiteits voorspellingen te zien als u meerdere uitingen samenvouwt tot één patroon. Voor eenvoudige entiteiten die moeten worden gestart, moet u uitingen toevoegen of de lijst entiteiten gebruiken. anders wordt uw patroon niet geactiveerd.

## <a name="patterns-use-entity-roles"></a>Patronen entiteit rollen gebruiken
Als twee of meer entiteiten in een patroon contextuele gerelateerde zijn, gebruiken patronen entiteits [rollen](luis-concept-roles.md) om contextuele informatie over entiteiten te extra heren.  

## <a name="prediction-scores-with-and-without-patterns"></a>Voorspelling scores met en zonder patronen
Onvoldoende uitingen voorbeeld gezien, zou LUIS kunnen worden vergroot het vertrouwen van voorspelling geen patronen weergegeven. De betrouwbaarheidsscore verhogen patronen zonder deze op te geven zoveel uitingen.  

## <a name="pattern-matching"></a>Jokertekens
Een patroon wordt op basis van de entiteiten in het patroon eerst detecteren en valideren van de rest van de woorden en de woordvolgorde van het patroon vergeleken. Entiteiten zijn vereist in het patroon voor een patroon voor de overeenkomst. Het patroon wordt toegepast op het niveau van token niet het teken-niveau. 

## <a name="pattern-only-apps"></a>Alleen patroon-apps
U kunt een app bouwen met intenties die geen voor beeld uitingen hebben, zolang er voor elke intentie een patroon is. Het patroon mag voor een alleen-patroon app geen door machines geleerde entiteiten bevatten, omdat hiervoor bijvoorbeeld uitingen nodig zijn. 

## <a name="best-practices"></a>Aanbevolen procedures
Lees de [Aanbevolen procedures](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Patroonsyntaxis

Meer informatie over de syntaxis van patronen in de syntaxis van de [patroon verwijzing](reference-pattern-syntax.md). 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over patronen:

* [Patronen toevoegen](luis-how-to-model-intent-pattern.md)
* [Een patroon toevoegen. elke entiteit](luis-how-to-add-entities.md##add-a-patternany-entity)
* [Syntaxis van patronen](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Meer informatie over het implementeren van patronen in deze zelf studie](luis-tutorial-pattern.md)
