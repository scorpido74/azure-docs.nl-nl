---
title: Wat zijn ML-pijp lijnen
titleSuffix: Azure Machine Learning
description: In dit artikel meer informatie over de machine learning-pijplijnen die u kunt maken met de Azure Machine Learning-SDK voor Python en de voordelen van pijplijnen. Machine learning (ML) pijplijnen worden gebruikt door de datawetenschappers te bouwen, te optimaliseren en beheren van de machine learning-werkstromen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 07efde7c3664ba1866e59f23c31b9c385ed9c366
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035491"
---
# <a name="what-are-ml-pipelines-in-azure-machine-learning"></a>Wat zijn ML-pijp lijnen in Azure Machine Learning?

Meer informatie over de machine learning-pijp lijnen die u kunt bouwen en beheren met Azure Machine Learning. 

Met behulp van machine learning (ML) pijplijnen, gegevenswetenschappers, gegevensengineers en IT-professionals kan samenwerken in de stappen voor het:
+ Voorbereiden van gegevens, zoals normalizations en transformaties
+ Modeltraining
+ Model-evaluatie
+ Implementatie

Meer informatie over het [uw eerste pijplijn](how-to-create-your-first-pipeline.md).

![Machine learning-pijp lijnen in Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Welke Azure pipeline-technologie moet ik gebruiken?

De Azure-Cloud biedt verschillende andere pijp lijnen, elk met een ander doel. De volgende tabel geeft een lijst van de verschillende pijp lijnen en waarvoor ze worden gebruikt:

| Pijplijn | Wat het doet | Canonieke pijp |
| ---- | ---- | ---- |
| Azure Machine Learning pijp lijnen | Hiermee definieert u herbruikbare machine learning werk stromen die kunnen worden gebruikt als sjabloon voor uw machine learning scenario's. | Model voor gegevens > |
| [Azure Data Factory-pijplijnen](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Groepeert gegevens verplaatsing, trans formatie en controle activiteiten die nodig zijn om een taak uit te voeren.  | Gegevens > gegevens |
| [Azure-pijp lijnen](https://azure.microsoft.com/services/devops/pipelines/) | Continue integratie en levering van uw toepassing op elk platform/elke Cloud  | Code-> app/service |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Waarom zou u pijplijnen met Azure Machine Learning bouwen?

Machine learning-pijp lijnen Optimaliseer uw werk stroom met snelheid, portabiliteit en hergebruik zodat u zich kunt concentreren op uw expertise, machine learning in plaats van op de infra structuur en automatisering.

Pijp lijnen worden samengesteld uit meerdere **stappen**, die afzonderlijke reken kundige eenheden in de pijp lijn zijn. Elke stap kan onafhankelijk worden uitgevoerd en geïsoleerde reken bronnen gebruiken.
Met de onafhankelijke stappen kunnen meerdere gegevens wetenschappers tegelijkertijd op dezelfde pijp lijn werken zonder dat er meer belasting bronnen hoeft te worden gebruikt, en kunt u voor elke stap eenvoudig verschillende reken typen of-grootten gebruiken.

Nadat de pijp lijn is ontworpen, is er vaak meer nauw keuriger om de training van de pijp lijn te verfijnen. Wanneer u een pijp lijn opnieuw uitvoert, springt de uitvoering naar de afzonderlijke stappen die opnieuw moeten worden uitgevoerd, zoals een bijgewerkt trainings script, en wordt wat er niet is gewijzigd, overgeslagen. De dezelfde paradigma is van toepassing op ongewijzigd scripts die worden gebruikt voor het uitvoeren van de stap. Deze functionaliteit voor hergebruik helpt te voor komen dat kost bare en tijdrovende stappen zoals gegevens opname en trans formatie worden uitgevoerd als de onderliggende gegevens niet zijn gewijzigd.

Met Azure Machine Learning kunt u verschillende tool kits en frameworks, zoals PyTorch of tensor flow, gebruiken voor elke stap in de pijp lijn. Azure-coördinaten tussen de verschillende [reken doelen](concept-azure-machine-learning-architecture.md) die u gebruikt, zodat uw tussenliggende gegevens eenvoudig kunnen worden gedeeld met de downstream-reken doelen.

U kunt [de metrische gegevens voor uw pijplijn experimenten rechtstreeks bijhouden](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) in azure portal of de [landings pagina van uw werk ruimte (preview)](https://ml.azure.com). Nadat een pijp lijn is gepubliceerd, kunt u een REST-eind punt configureren. Hiermee kunt u de pijp lijn opnieuw uitvoeren vanaf elk platform of elke stack.

## <a name="key-advantages"></a>Belangrijkste voordelen

De belangrijkste voor delen van het gebruik van pijp lijnen voor uw machine learning-werk stromen zijn:

|Groot voordeel|Description|
|:-------:|-----------|
|**Zonder toezicht&nbsp;wordt uitgevoerd**|Stappen plannen om parallel of op een betrouw bare en zonder toezicht te worden uitgevoerd. De voor bereiding en het model leren van gegevens kunnen de afgelopen dagen of weken zijn en met pijp lijnen kunt u zich richten op andere taken terwijl het proces wordt uitgevoerd. |
|**Heterogene compute**|Gebruik meerdere pijp lijnen die betrouwbaar zijn afgestemd op heterogene en schaal bare reken resources en opslag locaties. Maak efficiënt gebruik van beschik bare reken resources door afzonderlijke pijplijn stappen uit te voeren op verschillende reken doelen, zoals HDInsight, GPU data Science Vm's en Databricks.|
|**Herbruikbaarheid**|Maak pijplijn sjablonen voor specifieke scenario's, zoals retraining en batch-scores. Activeer gepubliceerde pijp lijnen van externe systemen via eenvoudige REST-aanroepen.|
|**Wijzigingen bijhouden en versiebeheer**|In plaats van gegevens en resultaat paden hand matig te traceren tijdens het herhalen, gebruikt u de SDK van de pijp lijnen om uw gegevens bronnen, invoer en uitvoer expliciet een naam en versie te gegeven. U kunt ook scripts en gegevens afzonderlijk beheren voor een verhoogde productiviteit.|
|**Werking**|Met pijp lijnen kunnen gegevens wetenschappers samen werken aan alle gebieden van het machine learning-ontwerp proces, terwijl ze gelijktijdig kunnen werken aan pijplijn stappen.|

## <a name="the-python-sdk-for-pipelines"></a>De Python-SDK voor pijplijnen

[Gebruik de python-SDK](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) om uw ml-pijp lijnen te maken in uw favoriete Integrated Development Environment (IDE) of Jupyter-notebooks. De SDK van Azure Machine Learning biedt imperatieve constructies voor sequentiëren en gebruik de stappen in uw pijplijnen wanneer niet afhankelijk van de gegevens aanwezig is. 

Met behulp van declaratieve gegevensafhankelijkheden, kunt u uw taken optimaliseren. De SDK bevat een Framework met vooraf ontwikkelde modules voor algemene taken, zoals gegevens overdracht en model publicatie. U kunt het framework uitbreiden om uw eigen conventies te model leren door aangepaste stappen te implementeren die opnieuw kunnen worden gebruikt in pijp lijnen. U kunt ook Compute-doelen en opslag bronnen rechtstreeks vanuit de SDK beheren.

Sla uw pijp lijnen op als sjablonen en implementeer deze in een REST-eind punt voor batch Score-of trainings taken.

Er zijn twee Python-pakketten voor pijp lijnen met Azure Machine Learning: [azureml-pijp lijnen-kern](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) en [azureml-pijp lijn-stappen](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Als u snel aan de slag wilt gaan, gebruikt u een van de vooraf ontwikkelde modules, zoals:

* Python-script uitvoeren in een stap met [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep)
* Gegevens overbrengen tussen opslag opties met [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep)
* Een AutoML-pijplijn stap maken met [AutoMLStep](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlstep)

## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over het [uw eerste pijplijn](how-to-create-your-first-pipeline.md).

+ Meer informatie over het [uitvoeren van batch voorspellingen voor grote gegevens](tutorial-pipeline-batch-scoring-classification.md).

+ Zie de [SDK-documentatie voor pijp lijnen](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py).

+ Probeer een voor beeld van Jupyter-notitie blokken [Azure machine learning-pijp lijnen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Meer informatie over het [uitvoeren van notitie blokken om deze service te verkennen](samples-notebooks.md).
