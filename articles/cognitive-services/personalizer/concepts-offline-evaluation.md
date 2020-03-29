---
title: Gebruik de methode Offline Evaluatie - Personalizer
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u offline evaluatie gebruiken om de effectiviteit van uw app te meten en uw leerlus te analyseren.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: f8ceef5e80bf15f0ba52a9c289e617018febfb5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623592"
---
# <a name="offline-evaluation"></a>Offline-evaluatie

Offline evaluatie is een methode waarmee u de effectiviteit van de Personalizer Service testen en beoordelen zonder uw code te wijzigen of de gebruikerservaring te beïnvloeden. Offline evaluatie maakt gebruik van gegevens uit het verleden, verzonden van uw toepassing naar de Rank en Reward API's, om te vergelijken hoe verschillende rangen hebben gepresteerd.

Offline evaluatie wordt uitgevoerd op een datumbereik. Het bereik kan net zo laat eindigen als de huidige tijd. Het begin van het bereik kan niet groter zijn dan het aantal dagen dat is opgegeven voor het bewaren van [gegevens.](how-to-settings.md)

Offline evaluatie kan u helpen de volgende vragen te beantwoorden:

* Hoe effectief zijn Personalizer-rangen voor succesvolle personalisatie?
    * Wat zijn de gemiddelde beloningen die worden behaald door het personalizer online machine learning-beleid?
    * Hoe verhoudt Personalizer zich tot de effectiviteit van wat de toepassing standaard zou hebben gedaan?
    * Wat zou de vergelijkende effectiviteit van een willekeurige keuze voor personalisatie zijn geweest?
    * Wat zou de vergelijkende effectiviteit zijn geweest van verschillende handmatig gespecificeerde leerbeleid?
* Welke kenmerken van de context dragen min of meer bij aan een succesvolle personalisatie?
* Welke kenmerken van de acties dragen min of meer bij aan een succesvolle personalisatie?

Daarnaast kan Offline Evaluatie worden gebruikt om meer geoptimaliseerd leerbeleid te ontdekken dat Personalizer kan gebruiken om de resultaten in de toekomst te verbeteren.

Offline evaluaties bieden geen richtlijnen over het percentage gebeurtenissen dat moet worden gebruikt voor verkenning.

## <a name="prerequisites-for-offline-evaluation"></a>Voorwaarden voor offline evaluatie

De volgende overwegingen zijn belangrijke overwegingen voor de representatieve offline evaluatie:

* Heb genoeg gegevens. Het aanbevolen minimum is ten minste 50.000 evenementen.
* Verzamel gegevens uit perioden met representatief gebruikersgedrag en verkeer.

## <a name="discovering-the-optimized-learning-policy"></a>Ontdek het geoptimaliseerde leerbeleid

Personalizer kan het offline evaluatieproces gebruiken om automatisch een beter leerbeleid te ontdekken.

Na het uitvoeren van de offline evaluatie, u de vergelijkende effectiviteit van Personalizer zien met dat nieuwe beleid in vergelijking met het huidige online beleid. U dat leerbeleid vervolgens toepassen om het onmiddellijk effectief te maken in Personalizer, door het te downloaden en te uploaden in het deelvenster Modellen en beleid. U het ook downloaden voor toekomstige analyse of gebruik.

Huidig beleid dat in de evaluatie is opgenomen:

| Leerinstellingen | Doel|
|--|--|
|**Online beleid**| Het huidige leerbeleid dat wordt gebruikt in Personalizer |
|**Basislijn**|De standaardinstelling van de toepassing (zoals bepaald door de eerste actie die in Rang-aanroepen is verzonden)|
|**Willekeurig beleid**|Een denkbeeldig rank-gedrag dat altijd willekeurige keuze van Acties van de meegeleverde acties retourneert.|
|**Aangepast beleid**|Aanvullend leerbeleid geüpload bij het starten van de evaluatie.|
|**Geoptimaliseerd beleid**|Als de evaluatie is gestart met de optie om een geoptimaliseerd beleid te ontdekken, wordt deze ook vergeleken en u deze downloaden of het online leerbeleid maken, ter vervanging van het huidige beleid.|

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Inzicht in de relevantie van offline evaluatieresultaten

Wanneer u een offline evaluatie uitvoert, is het erg belangrijk om _de vertrouwensgrenzen_ van de resultaten te analyseren. Als ze breed zijn, betekent dit dat uw toepassing niet genoeg gegevens heeft ontvangen om de beloningsschattingen nauwkeurig of belangrijk te maken. Naarmate het systeem meer gegevens verzamelt en u offline evaluaties uitvoert over langere perioden, worden de betrouwbaarheidsintervallen smaller.

## <a name="how-offline-evaluations-are-done"></a>Hoe offline evaluaties worden uitgevoerd

Offline evaluaties worden gedaan met behulp van een methode genaamd **Counterfactual Evaluation**.

Personalizer is gebouwd op de veronderstelling dat het gedrag van gebruikers (en dus beloningen) onmogelijk zijn om achteraf te voorspellen (Personalizer kan niet weten wat er zou zijn gebeurd als de gebruiker iets anders had getoond dan wat ze zagen), en alleen om van te leren gemeten beloningen.

Dit is het conceptuele proces dat wordt gebruikt voor evaluaties:

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call

        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.

    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

De offline evaluatie maakt alleen gebruik van waargenomen gebruikersgedrag. Met dit proces worden grote hoeveelheden gegevens verwijderd, vooral als uw toepassing Rank-oproepen doet met grote aantallen acties.


## <a name="evaluation-of-features"></a>Evaluatie van de kenmerken

Offline evaluaties kunnen informatie geven over hoeveel van de specifieke functies voor acties of context wegen voor hogere beloningen. De informatie wordt berekend aan de hand van de evaluatie aan de hand van de opgegeven periode en gegevens, en kan variëren met de tijd.

We raden u aan om te kijken naar functie-evaluaties en te vragen:

* Welke andere, extra, functies kunnen uw toepassing of systeem bieden langs de lijnen van degenen die effectiever zijn?
* Welke functies kunnen worden verwijderd vanwege de lage effectiviteit? Lage effectiviteitsfuncties voegen _ruis_ toe aan de machine learning.
* Zijn er functies die per ongeluk zijn opgenomen? Voorbeelden hiervan zijn: gebruikersidentificeerbare informatie, dubbele ID's, enz.
* Zijn er ongewenste functies die niet moeten worden gebruikt om te personaliseren als gevolg van regelgeving of verantwoord gebruik overwegingen? Zijn er functies die proxy kunnen (dat wil zeggen, nauw spiegelen of correleren met) ongewenste functies?


## <a name="next-steps"></a>Volgende stappen

[Personaliser](how-to-settings.md)
[Run Offline evaluaties](how-to-offline-evaluation.md) configureren begrijpen hoe [personalizer werkt](how-personalizer-works.md)
