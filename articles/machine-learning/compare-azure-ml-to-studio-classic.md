---
title: Azure Machine Learning vs. Machine Learning Studio (klassiek)
description: Wat is het verschil tussen Azure Machine Learning en Machine Learning Studio (klassiek)?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 5577a9847ff405397c553028a6dfdf2df80d03fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371844"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning vs Machine Learning Studio (klassiek)

In dit artikel leert u het verschil tussen Azure Machine Learning en Machine Learning Studio (klassiek). 

Azure Machine Learning biedt Python en R SDKs **en** de 'drag-and-drop'-ontwerper om machine learning-modellen te bouwen en te implementeren. Studio (klassiek) biedt alleen een standalone drag-and-drop ervaring.

We raden nieuwe gebruikers aan azure machine learning te kiezen voor het breedste scala aan geavanceerde machine learning-tools.

## <a name="quick-comparison"></a>Snelle vergelijking

In de volgende tabel worden enkele van de belangrijkste verschillen tussen Azure Machine Learning en Studio (klassiek) samengevat:

| | Machine Learning Studio (klassiek) | Azure Machine Learning |
|---| --- | --- |
| Interface slepen en neerzetten | Ondersteund | Ondersteund - [Azure Machine Learning-ontwerper (voorbeeld)](concept-designer.md) | 
| Experiment | Schaalbaar (trainingsgegevenslimiet van 10 GB) | Schalen met rekendoel |
| Rekendoelen trainen | Eigen compute target, CPU-ondersteuning alleen | Breed scala aan aanpasbare [trainingscomputetargets.](concept-compute-target.md#train) Inclusief GPU- en CPU-ondersteuning | 
| Compute targets voor implementatie | Eigen webservice-indeling, niet aanpasbaar | Breed scala aan aanpasbare [implementatiecomputetargets.](concept-compute-target.md#deploy) Inclusief GPU- en CPU-ondersteuning |
| ML-pijplijn | Niet ondersteund | Bouw flexibele, modulaire [pijplijnen](concept-ml-pipelines.md) om workflows te automatiseren |
| MLOps Mlops | Basismodelbeheer en -implementatie | Entiteitversiebeheer (model, gegevens, workflows), workflowautomatisering, integratie met CICD-tooling [en meer](concept-model-management-and-deployment.md) |
| Modelindeling | Eigen formaat, Studio (klassiek) alleen | Meerdere ondersteunde indelingen, afhankelijk van het type trainingstaak |
| Geautomatiseerde modeltraining en hyperparametertuning |  Niet ondersteund | [Ondersteund in de SDK en visuele werkruimte](concept-automated-ml.md) | 
| Detectie van gegevensdrift | Niet ondersteund | [Ondersteund in SDK en visuele werkruimte](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Migreren van Machine Learning Studio (klassiek)

Momenteel is er geen manier om Studio-(klassieke) assets te migreren naar Azure Machine Learning designer (preview). Huidige Studio (klassieke) gebruikers kunnen hun machine learning assets blijven gebruiken. We moedigen alle gebruikers echter aan om te overwegen de ontwerper te gebruiken, wat een vertrouwde drag-and-drop-ervaring biedt met verbeterde workflow **plus** schaalbaarheid, versiebeheer en bedrijfsbeveiliging.

## <a name="get-started-with-azure-machine-learning"></a>Aan de slag met Azure Machine Learning

Met de volgende bronnen u aan de slag met Azure Machine Learning. 

- Lees het [overzicht van Azure Machine Learning](overview-what-is-azure-ml.md).

- Maak uw [eerste experiment met de Python SDK.](tutorial-1st-experiment-sdk-setup.md)

- [Maak uw eerste designerpijplijn](tutorial-designer-automobile-price-train-score.md) om automatische prijzen te voorspellen.

![Voorbeeld van Azure Machine Learning-ontwerper](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Volgende stappen

Naast de sleep-and-drop-mogelijkheden in de ontwerper, heeft Azure Machine Learning andere hulpprogramma's beschikbaar:  
  + [Python-notitieblokken gebruiken om ML-modellen te trainen & implementeren](tutorial-1st-experiment-sdk-setup.md)
  + [R Markdown gebruiken om ML-modellen & implementeren te trainen](tutorial-1st-r-experiment.md) 
  + [Gebruik geautomatiseerde machine learning om ML-modellen & implementeren te trainen](tutorial-first-experiment-automated-ml.md)  
  + [Gebruik de machine learning CLI om een model te trainen en te implementeren](tutorial-train-deploy-model-cli.md)

