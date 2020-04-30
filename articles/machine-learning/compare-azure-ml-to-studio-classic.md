---
title: Azure Machine Learning versus Machine Learning Studio (klassiek)
description: Wat is het verschil tussen Azure Machine Learning en Machine Learning Studio (klassiek)?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 5577a9847ff405397c553028a6dfdf2df80d03fd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80371844"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning versus Machine Learning Studio (klassiek)

In dit artikel leert u het verschil tussen Azure Machine Learning en Machine Learning Studio (klassiek). 

Azure Machine Learning biedt python-en R-Sdk's **en** de designer ' slepen en neerzetten ' om machine learning modellen te bouwen en implementeren. Studio (klassiek) biedt alleen een zelfstandige ervaring voor slepen en neerzetten.

We raden aan dat nieuwe gebruikers Azure Machine Learning kiezen voor een breed scala aan geavanceerde machine learning-hulpprogram ma's.

## <a name="quick-comparison"></a>Snelle vergelijking

De volgende tabel bevat een overzicht van enkele van de belangrijkste verschillen tussen Azure Machine Learning en Studio (klassiek):

| | Machine Learning Studio (klassiek) | Azure Machine Learning |
|---| --- | --- |
| Interface slepen en neerzetten | Ondersteund | Ondersteund- [Azure machine learning Designer (preview-versie)](concept-designer.md) | 
| Experiment | Schaalbaar (10-GB training-gegevens limiet) | Schalen met Compute target |
| Doelen van de trainings compute | Eigen reken doel, alleen CPU-ondersteuning | Breed scala aan aanpas bare [trainings doelen](concept-compute-target.md#train). Inclusief GPU-en CPU-ondersteuning | 
| Doelen voor implementatie compute | De indeling van de oorspronkelijke webservice, niet aanpasbaar | Breed scala aan aanpas bare [reken doelen](concept-compute-target.md#deploy)voor de implementatie. Inclusief GPU-en CPU-ondersteuning |
| ML-pijp lijn | Niet ondersteund | Flexibele, modulaire [pijp lijnen](concept-ml-pipelines.md) bouwen om werk stromen te automatiseren |
| MLOps | Basis model beheer en-implementatie | Entiteit versie beheer (model, gegevens, werk stromen), werk stroom automatisering, integratie met CICD-hulp programma [en meer](concept-model-management-and-deployment.md) |
| Model indeling | Alleen een eigen indeling, Studio (klassiek) | Meerdere ondersteunde indelingen, afhankelijk van het type trainings taak |
| Automatische model training en afstemming tuning |  Niet ondersteund | [Ondersteund in de SDK en de Visual-werk ruimte](concept-automated-ml.md) | 
| Detectie van gegevens drift | Niet ondersteund | [Ondersteund in de SDK-en Visual-werk ruimte](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Migreren van Machine Learning Studio (klassiek)

Op dit moment is er geen manier om Studio-assets (Classic) te migreren naar Azure Machine Learning Designer (preview). Huidige studio-gebruikers (klassiek) kunnen hun machine learning-assets blijven gebruiken. We moedigen echter aan alle gebruikers te overwegen om de ontwerp functie te gebruiken, die een vertrouwde functionaliteit voor slepen en neerzetten biedt met **verbeterde werk stroom en schaal** baarheid, versie beheer en Bedrijfs beveiliging.

## <a name="get-started-with-azure-machine-learning"></a>Aan de slag met Azure Machine Learning

De volgende bronnen kunnen u helpen aan de slag te gaan met Azure Machine Learning. 

- Lees het [Azure machine learning overzicht](overview-what-is-azure-ml.md).

- Maak uw [eerste experiment met de python-SDK](tutorial-1st-experiment-sdk-setup.md).

- [Maak uw eerste Design pijp lijn](tutorial-designer-automobile-price-train-score.md) om automatische prijzen te voors pellen.

![Voor beeld van Azure Machine Learning Designer](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Volgende stappen

Naast de mogelijkheden voor slepen en neerzetten in de ontwerp functie is Azure Machine Learning andere hulpprogram ma's beschikbaar:  
  + [Python-notebooks gebruiken voor het trainen & implementeren van ML-modellen](tutorial-1st-experiment-sdk-setup.md)
  + [R-korting gebruiken om & Deploy-modellen te trainen](tutorial-1st-r-experiment.md) 
  + [Automatische machine learning gebruiken om & Deploy-modellen te trainen](tutorial-first-experiment-automated-ml.md)  
  + [De machine learning CLI gebruiken om een model te trainen en te implementeren](tutorial-train-deploy-model-cli.md)

