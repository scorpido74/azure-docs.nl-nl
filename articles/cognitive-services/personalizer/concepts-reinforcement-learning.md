---
title: Reinforcement Learning - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer gebruikt informatie over acties en de huidige context om betere rankingsuggesties te doen. De informatie over deze acties en context zijn kenmerken of eigenschappen die worden aangeduid als functies.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 36071cdee25cfa99fc54b0e5c0c0aa822cb5fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68662843"
---
# <a name="what-is-reinforcement-learning"></a>Wat is Reinforcement Learning?

Reinforcement Learning is een benadering van machine learning die gedrag leert door feedback te krijgen van het gebruik ervan.
 
Reinforcement Learning werkt door:

* Het bieden van een kans of mate van vrijheid om een gedrag uit te voeren - zoals het maken van beslissingen of keuzes.
* Het verstrekken van contextuele informatie over de omgeving en keuzes.
* Feedback geven over hoe goed het gedrag een bepaald doel bereikt.

Hoewel er veel subtypen en stijlen van versterking leren, dit is hoe het concept werkt in Personalizer:

* Uw toepassing biedt de mogelijkheid om een stuk inhoud te tonen uit een lijst met alternatieven.
* Uw applicatie geeft informatie over elk alternatief en de context van de gebruiker.
* Uw toepassing berekent een _beloningsscore._

In tegenstelling tot sommige benaderingen van reinforcement learning, heeft Personalizer geen simulatie nodig om in te werken. De leeralgoritmen zijn ontworpen om te reageren op een buitenwereld (versus het te controleren) en te leren van elk datapunt met het inzicht dat het een unieke kans is die tijd en geld kost om te creëren, en dat er een niet-nul spijt (verlies van mogelijke beloning) suboptimale prestaties gebeurt.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Wat voor soort versterking leren algoritmen gebruikt Personalizer?

De huidige versie van Personalizer maakt gebruik van **contextuele bandieten**, een benadering van versterking leren dat is ingelijst rond het maken van beslissingen of keuzes tussen discrete acties, in een bepaalde context.

Het _beslissingsgeheugen_, het model dat is opgeleid om de best mogelijke beslissing vast te leggen, gezien een context, maakt gebruik van een set van lineaire modellen. Deze hebben herhaaldelijk bedrijfsresultaten laten zien en zijn een bewezen aanpak, deels omdat ze zeer snel kunnen leren van de echte wereld zonder multi-pass training nodig te hebben, en gedeeltelijk omdat ze begeleide leermodellen en diepe neurale netwerkmodellen.

De locatie van het verkennen/exploiteren van verkeer wordt willekeurig gemaakt volgens het percentage dat is ingesteld voor verkenning, en het standaardalgoritme voor verkenning is epsilon-gulzig.

### <a name="history-of-contextual-bandits"></a>Geschiedenis van contextuele bandieten

John Langford bedacht de naam Contextual Bandits (Langford en Zhang [2007]) om een hardnekkige subset van versterkingsleren te beschrijven en heeft gewerkt aan een half dozijn papers die ons begrip van hoe te leren in dit paradigma verbeteren:

* Beygelzimer et al. [2011]
* Dudík et al. [2011a,b]
* Agarwal et al. [2014, 2012]
* Beygelzimer en Langford [2009]
* Li et al. [2010]

John heeft ook eerder verschillende tutorials gegeven over onderwerpen als Joint Prediction (ICML 2015), Contextual Bandit Theory (NIPS 2013), Active Learning (ICML 2009) en Sample Complexity Bounds (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Welke machine learning frameworks gebruikt Personalizer?

Personalizer gebruikt [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) momenteel als basis voor de machine learning. Dit framework zorgt voor maximale doorvoer en laagste latentie bij het maken van personalisatierangen en het trainen van het model met alle gebeurtenissen.

## <a name="references"></a>Verwijzingen

* [Contextuele beslissingen nemen met een lage technische schuld](https://arxiv.org/abs/1606.03966)
* [Een reductiebenadering van eerlijke classificatie](https://arxiv.org/abs/1803.02453)
* [Efficiënte contextuele bandieten in niet-stationaire werelden](https://arxiv.org/abs/1708.01799)
* [Residual Loss Voorspelling: Versterking: leren zonder incrementele feedback](https://openreview.net/pdf?id=HJNMYceCW)
* [Instructies en visuele waarnemingen toewijzen aan acties met reinforcement learning](https://arxiv.org/abs/1704.08795)
* [Leren om beter te zoeken dan je leraar](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Volgende stappen

[Offline-evaluatie](concepts-offline-evaluation.md) 