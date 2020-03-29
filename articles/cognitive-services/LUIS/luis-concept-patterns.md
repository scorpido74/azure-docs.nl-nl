---
title: Patronen helpen bij het voorspellen - LUIS
titleSuffix: Azure Cognitive Services
description: Met een patroon u meer nauwkeurigheid voor een intentie krijgen zonder veel meer uitingen te verstrekken.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 6c1b548de25369c162b4a08dfa20fce62c17f99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75890291"
---
# <a name="patterns-improve-prediction-accuracy"></a>Patronen verbeteren de nauwkeurigheid van de voorspelling
Patronen zijn ontworpen om de nauwkeurigheid te verbeteren wanneer verschillende uitingen zeer vergelijkbaar zijn.  Met een patroon u meer nauwkeurigheid voor een intentie krijgen zonder veel meer uitingen te verstrekken. 

## <a name="patterns-solve-low-intent-confidence"></a>Patronen lossen vertrouwen met lage intentie op
Overweeg een Human Resources-app die rapporteert op het organigram met betrekking tot een werknemer. Gezien de naam en relatie van een werknemer, stuurt LUIS de betrokken werknemers terug. Overweeg een werknemer, Tom, met een manager naam Alice, en een team van ondergeschikten genaamd: Michael, Rebecca, en Carl.

![Afbeelding van het organigram](./media/luis-concept-patterns/org-chart.png)

|Utterances|Intentie voorspeld|Intentiescore|
|--|--|--|
|Wie is Tom's ondergeschikte?|GetOrgChart|.30|
|Wie is de ondergeschikte van Tom?|GetOrgChart|.30|

Als een app tussen de 10 en 20 uitingen heeft met verschillende lengtes van de zin, verschillende woordvolgorde en zelfs verschillende woorden (synoniemen van 'ondergeschikt', 'beheren', 'rapporteren'), kan LUIS een lage betrouwbaarheidsscore retourneren. Maak een patroon om LUIS inzicht te geven in het belang van de woordvolgorde. 

Patronen lossen de volgende situaties op: 

* De intentiescore is laag
* De juiste intentie is niet de topscore, maar te dicht bij de topscore. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Patronen zijn geen garantie voor intentie
Patronen maken gebruik van een mix van voorspellingstechnologieën. Het instellen van een intentie voor een sjabloonutterance in een patroon is geen garantie voor de intentievoorspelling, maar het is een sterk signaal. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Patronen verbeteren de detectie van door de machine geleerde entiteit niet

Een patroon is in de eerste plaats bedoeld om de voorspelling van intenties en rollen te helpen. Het _patroon.elke_ entiteit wordt gebruikt om entiteiten uit vrije vorm te extraheren. Hoewel patronen entiteiten gebruiken, helpt een patroon niet bij het detecteren van een door de machine geleerde entiteit.  

Verwacht geen verbeterde entiteitsvoorspelling als u meerdere uitingen samenvouwt in één patroon. Als u eenvoudige entiteiten wilt afvuren, moet u uitingen toevoegen of lijstentiteiten gebruiken, anders wordt uw patroon niet afvuurt.

## <a name="patterns-use-entity-roles"></a>Patronen gebruiken entiteitsrollen
Als twee of meer entiteiten in een patroon contextueel gerelateerd zijn, gebruiken patronen [entiteitsrollen](luis-concept-roles.md) om contextuele informatie over entiteiten te extraheren.  

## <a name="prediction-scores-with-and-without-patterns"></a>Voorspellingscores met en zonder patronen
Gezien genoeg voorbeelduitingen, zou LUIS in staat zijn om voorspelling vertrouwen te verhogen zonder patronen. Patronen verhogen de betrouwbaarheidsscore zonder dat u zoveel uitingen hoeft te leveren.  

## <a name="pattern-matching"></a>Patroonherkenning
Een patroon wordt gekoppeld aan het detecteren van de entiteiten in het patroon eerst, vervolgens valideren van de rest van de woorden en woordvolgorde van het patroon. Entiteiten zijn vereist in het patroon voor een patroon te evenaren. Het patroon wordt toegepast op tokenniveau, niet op het tekenniveau. 

## <a name="pattern-only-apps"></a>Apps met alleen patronen
U een app bouwen met intents die geen voorbeelduitingen hebben, zolang er een patroon is voor elke intentie. Voor een app met alleen patronen mag het patroon geen door de machine geleerde entiteiten bevatten, omdat hiervoor voorbeelduitingen nodig zijn. 

## <a name="best-practices"></a>Aanbevolen procedures
Leer [aanbevolen procedures](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Patroonsyntaxis

Leer de syntaxis van het patroon uit de verwijzing naar de [syntaxis van het patroon](reference-pattern-syntax.md). 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over patronen:

* [Patronen toevoegen](luis-how-to-model-intent-pattern.md)
* [Hoe patroon.een entiteit toe te voegen](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Syntaxis van patronen](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Meer informatie over het implementeren van patronen in deze zelfstudie](luis-tutorial-pattern.md)
