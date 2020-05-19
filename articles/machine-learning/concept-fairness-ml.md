---
title: Verdeling in machine learning modellen beoordelen en beperken
titleSuffix: Azure Machine Learning
description: Meer informatie over verdeling in machine learning-modellen en hoe het python-pakket van Fairlearn u kan helpen om eerlijke modellen te bouwen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/02/2020
ms.openlocfilehash: c21ec0329a7b5716a00262b7422296df3afe208b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598603"
---
# <a name="fairness-in-machine-learning-models"></a>Verdeling in machine learning modellen

Meer informatie over verdeling in machine learning en hoe het open-source python-pakket van Fairlearn u kan helpen bij het bouwen van modellen die billijker zijn.

## <a name="what-is-fairness-in-machine-learning-systems"></a>Wat is verdeling in machine learning systemen?

Kunst matige intelligentie en machine learning systemen kunnen oneerlijke gedrag tonen. Een manier om oneerlijk gedrag te definiëren, is het nadeel van het probleem of gevolgen voor mensen. Er zijn veel soorten schade die AI-systemen kunnen aanleiding geven tot. Twee veelvoorkomende typen AI-oorzaken zijn:

- Beschadiging van de toewijzing: een AI-systeem breidt of inhoudt verkoop kansen, resources of informatie. Voor beelden zijn onder andere huur, school toelatingen en leningen waarbij een model veel beter kan zijn bij het verzamelen van goede kandidaten over een specifieke groep personen dan onder andere groepen.

- Beschadiging van Quality-of-service: een AI-systeem werkt ook niet voor één groep mensen, zoals het voor een andere. Een voor beeld: een systeem voor spraak herkenning werkt mogelijk niet goed voor vrouwen als voor mannen.

Om oneerlijke werking in AI-systemen te verminderen, moet u deze Risico's beoordelen en beperken.

>[!NOTE]
> Verdeling is een sociaal-Technical uitdaging. Veel aspecten van verdeling, zoals rechtvaardigheid en het verval proces, worden niet vastgelegd in kwantitatieve verdeling-metrische gegevens. Daarnaast kunnen veel kwantitatieve verdeling-metrische gegevens niet tegelijkertijd aan de criteria voldoen. Het doel is om mensen de mogelijkheid te bieden verschillende strategieën voor risico beperking te beoordelen en vervolgens de wissel werking te bepalen die geschikt is voor hun scenario.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Verdeling-evaluatie en-oplossing met Fairlearn

Fairlearn is een open-source python-pakket dat machine learning systeem ontwikkelaars in staat stelt hun systemen verdeling te beoordelen en de waargenomen verdeling-problemen te verhelpen.

Fairlearn heeft twee onderdelen:

- Beoordelings Dashboard: een Jupyter notebook-widget waarmee wordt beoordeeld hoe de voor spellingen van een model van invloed zijn op verschillende groepen. Er kunnen ook meerdere modellen worden vergeleken met behulp van verdeling-en prestatie gegevens.
- Beperkende algoritmen: een set algoritmen voor het beperken van oneerlijkheid in binaire classificatie en regressie.

Met deze onderdelen kunnen gegevens wetenschappers en bedrijfs leiders alle trans acties tussen verdeling en prestaties door lopen en de risico beperkings strategie selecteren die het beste bij hun behoeften past.

## <a name="fairness-assessment"></a>Verdeling-evaluatie

In Fairlearn wordt verdeling geconceptuele met een aanpak die wordt aangeduid als **groeps verdeling**, waarin wordt gevraagd: welke groepen personen risico lopen voor het optreden van het probleem?

De relevante groepen, ook wel subpopulaties genoemd, worden gedefinieerd door middel van **gevoelige functies** of gevoelige kenmerken. Gevoelige functies worden door gegeven aan een Fairlearn-Estimator als een vector of matrix met de naam `sensitive_features` . De term impliceert dat de systeem ontwerper gevoelig moet zijn voor deze functies bij het bepalen van de groeps verdeling. Als mindful is, is het van belang of deze functies privacy-implicaties door persoons gegevens bevatten. Maar het woord ' gevoelig ' impliceert niet dat deze functies mogen worden gebruikt voor het maken van voor spellingen.

Tijdens de evaluatie fase wordt verdeling gekwantificeerd via de metrische gegevens over de pariteit. Met **metrische gegevens** over de pariteit kan het model gedrag van de verschillende groepen worden geëvalueerd en vergeleken, zoals de verhoudingen of verschillen. Fairlearn ondersteunt twee klassen van gegevens over de pariteit:


- Verschillen in model prestaties: met deze sets metrische gegevens wordt het verschil in de waarden van de geselecteerde prestatie gegevens over verschillende subgroepen berekend. Voorbeelden zijn:

  - de nauw keurigheid van de pariteit
  - fout frequentie van verschillen
  - verschillen in precisie
  - dispariteit in intrekken
  - depariteit in MAE
  - veel andere

- Verschillen in selectie criterium: deze metriek bevat het verschil in selectie verhouding tussen verschillende subgroepen. Een voor beeld hiervan is het verschil in de goedkeurings frequentie voor leningen. Selectie percentage betekent de Fractie van data Points in elke klasse die is geclassificeerd als 1 (in binaire classificatie) of distributie van voorspellings waarden (in regressie).

## <a name="unfairness-mitigation"></a>Beperking van oneerlijkheid

### <a name="parity-constraints"></a>Pariteits beperkingen

Fairlearn bevat een aantal mogelijkheden voor het beperken van de oneerlijkheid. Deze algoritmen bieden ondersteuning voor een set met beperkingen voor het gedrag van de Voorspellings functie met de naam **pariteits beperkingen** of criteria. Pariteits beperkingen vereisen dat bepaalde aspecten van het gedrag van de voor spellingen vergelijkbaar zijn met de groepen die gevoelige functies definiëren (bijvoorbeeld verschillende races). De beperkende algoritmen van Fairlearn gebruiken dergelijke pariteits beperkingen om de waargenomen verdeling problemen te verhelpen.

Fairlearn ondersteunt de volgende typen pariteits beperkingen:

|Pariteits beperking  | Doel  |Machine learning-taak  |
|---------|---------|---------|
|Demografische pariteit     |  Beperking van de toewijzing is ongedaan | Binaire classificatie, regressie |
|Gelijke conflicteert  | Vaststellen van de toewijzing en de kwaliteit van de service | Binaire classificatie        |
|Verlies gebonden groep     |  Problemen met de kwaliteit van de service beperken | Regressie |

### <a name="mitigation-algorithms"></a>Beperkende algoritmen

Fairlearn biedt postprocessing en reductie algoritmen voor het beperken van de oneerlijkheid:

- Reductie: deze algoritmen hebben een standaard Black-Box ML Estimator (bijvoorbeeld een LightGBM-model) en genereren een set met retrainde modellen met behulp van een reeks hergewogen trainings gegevens sets. Aanvragers van een bepaald geslacht kunnen bijvoorbeeld worden verkleind of omlaag worden gereduceerd om modellen opnieuw te trainen en de verschillen in verschillende groepen geslacht te verminderen. Gebruikers kunnen vervolgens een model kiezen dat de beste verhouding biedt tussen nauw keurigheid (of andere prestatie gegevens) en de verschillen, wat doorgaans moet worden gebaseerd op bedrijfs regels en kosten berekeningen.  
- Na het verwerken: deze algoritmen hebben een bestaande classificatie en de gevoelige functie als invoer. Vervolgens wordt een trans formatie van de voor spelling van de classificatie afgeleid om de opgegeven verdeling-beperkingen af te dwingen. Het grootste voor deel van de drempel optimalisatie is de eenvoud en flexibiliteit, omdat het model niet hoeft te worden getraind. 

| Algoritme | Beschrijving | Machine learning-taak | Gevoelige functies | Ondersteunde pariteits beperkingen | Algoritme type |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | Black-Box-benadering voor een billijke classificatie die wordt beschreven in [een verlagings benadering van een billijke classificatie](https://arxiv.org/abs/1803.02453) | Binaire classificatie | Categorische gegevens | [Demografische pariteit](#parity-constraints), [equaled conflicteert](#parity-constraints) | Passen |
| `GridSearch` | Black-Box-benadering beschreven in [een verlagings benadering van een billijke classificatie](https://arxiv.org/abs/1803.02453)| Binaire classificatie | Binair | [Demografische pariteit](#parity-constraints), [equaled conflicteert](#parity-constraints) | Passen |
| `GridSearch` | Black-Box-benadering waarmee een grid-Zoek variant van een billijke regressie wordt geïmplementeerd met het algoritme voor beperkt groeps verlies dat wordt beschreven in [eerlijke regressie: kwantitatieve definities en algoritmen op basis van reductie](https://arxiv.org/abs/1905.12843) | Regressie | Binair | [Verlies gebonden groep](#parity-constraints) | Passen |
| `ThresholdOptimizer` | Postprocessing-algoritme op basis van het papieren [voor het leren van de verkoop kansen in Super visie](https://arxiv.org/abs/1610.02413). Deze techniek neemt als invoer een bestaande classificatie en de gevoelige functie en maakt een monotone trans formatie van de voor spelling van de classificatie om de opgegeven pariteits beperkingen af te dwingen. | Binaire classificatie | Categorische gegevens | [Demografische pariteit](#parity-constraints), [equaled conflicteert](#parity-constraints) | Na verwerking |

## <a name="next-steps"></a>Volgende stappen

- Voor meer informatie over het gebruik van de verschillende onderdelen raadpleegt u de [Fairlearn github-opslag plaats](https://github.com/fairlearn/fairlearn/) en de [voorbeeld notitieblokken](https://github.com/fairlearn/fairlearn/tree/master/notebooks).
- Meer informatie over het behoud van de privacy van gegevens met behulp van [differentiële privacy en het WhisperNoise-pakket](concept-differential-privacy.md).