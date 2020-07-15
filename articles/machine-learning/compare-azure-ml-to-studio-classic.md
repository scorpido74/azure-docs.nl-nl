---
title: Azure Machine Learning t.o.v. Machine Learning Studio (klassiek)
description: Wat is het verschil tussen Azure Machine Learning en Machine Learning Studio (klassiek)?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 1d4896a2dd2115e5654baffe394618b30a294d5b
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231740"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Documentatie voor Azure Machine Learning t.o.v. Machine Learning Studio (klassiek)

In dit artikel leert u het verschil tussen Azure Machine Learning en Machine Learning Studio (klassiek). 

Azure Machine Learning biedt python- en R-SDK's **en** de ontwerpfunctie met slepen en neerzetten om machine learning-modellen te bouwen en te implementeren. Studio (klassiek) biedt alleen een zelfstandige ervaring voor slepen en neerzetten.

We raden nieuwe gebruikers Azure Machine Learning aan om te kiezen voor het breedste scala aan geavanceerde machine learning-hulpprogramma's.

## <a name="quick-comparison"></a>Snelle vergelijking

De volgende tabel bevat een overzicht van enkele van de belangrijkste verschillen tussen Azure Machine Learning en Studio (klassiek):

| Functie | Machine Learning Studio (klassiek) | Azure Machine Learning |
|---| --- | --- |
| Interface met slepen en neerzetten | Ondersteund | Ondersteund - [De Azure Machine Learning-ontwerpfunctie (preview)](concept-designer.md) <br/>(Hiervoor is Enterprise Workspace vereist) | 
| Experiment | Schaalbaar (max. 10 GB aan trainingsgegevens) | Schalen met rekendoel |
| Rekendoelen voor training | Eigen rekendoel, alleen CPU-ondersteuning | Breed scala aan aanpasbare [rekendoelen voor training](concept-compute-target.md#train). Inclusief GPU- en CPU-ondersteuning | 
| Rekendoelen voor implementatie | Bedrijfseigen webservice-indeling, niet aanpasbaar | Breed scala aan aanpasbare [rekendoelen voor implementatie](concept-compute-target.md#deploy). Inclusief GPU- en CPU-ondersteuning |
| ML-pijplijn | Niet ondersteund | Bouw flexibele, modulaire [pijplijnen](concept-ml-pipelines.md) om werkstromen te automatiseren |
| MLOps | Eenvoudig modellen beheren en implementeren | Versiebeheer voor entiteiten (model, gegevens, werkstromen), werkstroomautomatisering, integratie met CICD-hulpprogramma, [en meer](concept-model-management-and-deployment.md) |
| Modelindeling | Eigen indeling, alleen Studio (klassiek) | Meerdere ondersteunde indelingen, afhankelijk van het type trainingstaak |
| Geautomatiseerde modeltraining en afstemming van hyperparameters |  Niet ondersteund | [Ondersteund in de SDK en de visuele werkruimte](concept-automated-ml.md) | 
| Gegevensdriftdetectie | Niet ondersteund | [Ondersteund in de SDK en visuele werkruimte](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Migreren van Machine Learning Studio (klassiek)

Op dit moment is er geen manier om assets uit Studio (klassiek) te migreren naar de Azure Machine Learning-ontwerpfunctie (preview). Huidige gebruikers van Studio (klassiek) kunnen hun machine learning-assets blijven gebruiken. We moedigen alle gebruikers echter aan om het gebruik van de ontwerpfunctie te overwegen. Deze biedt een vertrouwde functionaliteit voor slepen en neerzetten met verbeterde werkstroom **plus** schaalbaarheid, versiebeheer en hoogwaardige beveiliging.

## <a name="get-started-with-azure-machine-learning"></a>Aan de slag met Azure Machine Learning

De volgende bronnen kunnen u helpen aan de slag te gaan met Azure Machine Learning. 

- Lees het [overzicht van Azure Machine Learning](overview-what-is-azure-ml.md).

- Maak uw [eerste experiment met de Python-SDK](tutorial-1st-experiment-sdk-setup.md).

- [Maak uw eerste ontwerppijplijn](tutorial-designer-automobile-price-train-score.md) om automatische prijzen te voorspellen.

![Voorbeeld van ontwerpfunctie van Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Volgende stappen

Naast de mogelijkheden voor slepen en neerzetten in de ontwerpfunctie biedt Azure Machine Learning ook andere hulpprogramma's:  
  + [Python-notebooks gebruiken voor het trainen en implementeren van ML-modellen](tutorial-1st-experiment-sdk-setup.md)
  + [R Markdown gebruiken voor het trainen en implementeren van ML-modellen](tutorial-1st-r-experiment.md) 
  + [Geautomatiseerde machine learning gebruiken voor het trainen en implementeren van ML-modellen](tutorial-first-experiment-automated-ml.md)  
  + [De machine learning-CLI gebruiken voor het trainen en implementeren van een model](tutorial-train-deploy-model-cli.md)

