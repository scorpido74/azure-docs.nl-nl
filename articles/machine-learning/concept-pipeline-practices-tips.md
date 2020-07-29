---
title: Machine learning-pijplijnen doorlopen en ontwikkelen
titleSuffix: Azure Machine Learning
description: Patronen, procedures en tips voor snelle ontwikkeling
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 05/01/2020
ms.openlocfilehash: 2ea353469ed111eebb591aa6ba86c652683cc2f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82858195"
---
# <a name="iterating-and-evolving-machine-learning-pipelines"></a>Machine learning-pijplijnen doorlopen en ontwikkelen

Azure Machine Learning-pijp lijnen bieden een efficiënte manier om uw code te modularize, resultaten opnieuw te gebruiken en uw reken resources te optimaliseren. Hier volgen enkele praktische tips en procedures voor het werken met pijp lijnen.

## <a name="how-do-you-get-started-with-pipelines"></a>Hoe gaat u aan de slag met pijp lijnen?

Er zijn verschillende opties om aan de slag te gaan als uw pijp lijnen nieuw zijn:

* Als u het beste kunt lezen en het bouw proces opnieuw wilt maken, kunt u het artikel [machine learning pijp lijnen maken en uitvoeren met Azure machine learning SDK](how-to-create-your-first-pipeline.md) goed aanpassen 
* Als u interactief wilt leren in een Jupyter-notebook, hebt u de pijp lijn ontwikkeld in het [Azure machine learning pijp lijnen: aan](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-getting-started.ipynb) de slag-laptop is mogelijk geschikt voor u
* Als u de voor keur geeft aan een code-eerste situatie, wordt het klonen van de [Azure machine learning pipelines-demo](https://github.com/microsoft/aml-pipelines-demo) opslag plaats een goed uitgangs punt.

## <a name="how-do-you-modularize-pipeline-code"></a>Hoe kunt u pijplijn code modularize? 

Modules en de- `ModuleStep` klasse bieden u een goede kans om uw ml code te modularize. Het moet echter wel worden bewaard, maar het verplaatsen van de stappen van de pijp lijn is duurder dan een functie aanroep. De vraag is dat u niet zoveel te vragen hebt: zijn deze functies en gegevens conceptueel anders dan die in deze andere sectie? " maar ik wil dat deze functies en gegevens afzonderlijk worden ontwikkeld? " of ' is deze reken kracht duur en kan ik de uitvoer opnieuw gebruiken? ' Zie thisn'tebook voor meer informatie [over het maken van een module, ModuleVersion en gebruiken in een pijp lijn met ModuleStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb).

Zoals eerder besproken, is het scheiden van de gegevens voorbereiding uit training vaak een van de mogelijkheden. Soms is de gegevens voorbereiding complex en tijdrovend genoeg om het proces in afzonderlijke pijplijn stappen te verstoren. Andere mogelijkheden zijn het testen en analyseren van post training. 

## <a name="how-do-you-speed-pipeline-iteration"></a>Hoe versnelt u de iteratie van pijp lijnen? 

Veelgebruikte technieken voor het snel herhalen van pijp lijnen zijn onder andere: 

- De pijp lijn klonen (een kopie maken) en de pijp lijn snel opnieuw uitvoeren
- Het reken proces actief houden, zodat de opstart tijd wordt voor komen
- Door de gegevens te configureren en de stappen voor het opnieuw gebruiken toe te staan, kan de pijp lijn de herberekening van ongewijzigde gegevens overs Laan

Als u snel wilt herhalen, kunt u uw pijp lijn klonen, een pijp lijn maken en de pijp lijn opnieuw uitvoeren. Een andere handige techniek is als u uw reken kracht warm blijft, de kosten voor het draaien van de nieuwe reken capaciteit niet worden berekend. Als u de stap instelt om hergebruik van het resultaat van een uitvoering toe te staan, worden de resultaten waar mogelijk opnieuw door de herhaalde uitvoering opnieuw gebruikt (wanneer de stappen niet worden gewijzigd).

## <a name="how-do-you-collaborate-using-ml-pipelines"></a>Hoe kan ik samen werken met ML-pijp lijnen? 

Een afzonderlijke pijp lijn is een natuurlijke lijn voor het splitsen van de inspanningen. Meerdere ontwikkel aars of zelfs meerdere teams kunnen met verschillende stappen werken, zolang de gegevens en argumenten die tussen de stappen worden geplaatst, zijn overeengekomen. 

Tijdens de actieve ontwikkeling kunt u `PipelineRun` resultaten ophalen en `StepRun` uitvoeren vanuit de werk ruimte, deze objecten gebruiken om de uiteindelijke en tussenliggende uitvoer te downloaden en deze artefacten te gebruiken voor uw eigen modulaire werk.

## <a name="use-pipelines-to-test-techniques-in-isolation"></a>Pijp lijnen gebruiken om technieken in isolatie te testen

Oplossingen voor de echte wereld wijde omvatten doorgaans een aanzienlijke aanpassing van elke stap. Onbewerkte gegevens moeten vaak op een of andere manier worden voor bereid: gefilterd, getransformeerd en uitgebreid. De trainings processen hebben mogelijk meerdere mogelijke architecturen en, voor een diepere kennis, veel mogelijke variaties voor laag grootten en activerings functies. Zelfs met een consistente architectuur kan afstemming Search aanzienlijke WINS produceren.

Naast de hulpprogram ma's zoals [AutoML](concept-automated-ml.md) en [geautomatiseerde afstemming Search](how-to-tune-hyperparameters.md), kunnen pijp lijnen een belang rijk hulp middel zijn voor het testen van oplossingen. Als u verschillende varianten van uw pijplijn stappen hebt, is het eenvoudig om afzonderlijke uitvoeringen te genereren, waarbij de variaties worden geprobeerd: 

```python
data_preparation_variants = [data1, data2, data3]
data_augmentation_variants = [aug1, aug2]
architecture_variants = [train1, train2, train3]

# Cartesian product
all_variants = np.array(np.meshgrid(data_preparation_variants,data_augmentation_variants,architecture_variants)).T.reshape(-1,3)

pipelines = [Pipeline(workspace=ws, steps=variant.tolist(), description=str(variant)) for variant in all_variants]

```

