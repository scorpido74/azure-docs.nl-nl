---
title: Azure Machine Learning versus Machine Learning Studio (klassiek)
description: Hoe Azure Machine Learning verschilt van Machine Learning Studio (klassiek)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: a3122a3ae1687369b87d193efc693b3b7c659aac
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2020
ms.locfileid: "76311458"
---
# <a name="how-azure-machine-learning-differs-from-machine-learning-studio-classic"></a>Hoe Azure Machine Learning verschilt van Machine Learning Studio (klassiek)

In dit artikel worden de functies, mogelijkheden en interface van Azure Machine Learning vergeleken met Machine Learning Studio (klassiek). 

## <a name="about-machine-learning-studio-classic"></a>Over Machine Learning Studio (klassiek)
[Machine learning Studio (klassiek)](studio/what-is-ml-studio.md) is een samen werkende, visuele werk ruimte met slepen en neerzetten, waar u Machine Learning oplossingen kunt bouwen, testen en implementeren zonder dat u code hoeft te schrijven. Hierbij wordt gebruikgemaakt van vooraf gebouwde en vooraf geconfigureerde machine learning-algoritmen en modules voor het verwerken van gegevens, evenals een eigen reken platform.

## <a name="about-azure-machine-learning"></a>Over Azure Machine Learning

Ondertussen biedt [Azure machine learning](overview-what-is-azure-ml.md) een webinterface met de naam Design (preview) **en** verschillende sdk's en CLI om snel gegevens te kunnen prepen, machine learning modellen te trainen en te implementeren. Met Azure Machine Learning u schaal, meerdere Framework-ondersteuning, geavanceerde ML-mogelijkheden, zoals geautomatiseerde machine learning en pijplijn ondersteuning.

Azure Machine Learning Designer biedt een vergelijk bare functionaliteit voor slepen en neerzetten naar Studio (klassiek). Maar in tegens telling tot het eigen reken platform van Studio (klassiek) gebruikt de ontwerper uw eigen reken resources, is schaalbaar en is deze volledig geïntegreerd in Azure Machine Learning.  

> [!TIP]
> Klanten die momenteel gebruikmaken van of evalueren van Machine Learning Studio (klassiek), worden aangemoedigd om [Azure machine learning Designer](https://docs.microsoft.com/azure/machine-learning/concept-designer) (preview) te proberen, met behulp van de modules voor slepen en neerzetten, __plus__ schaal baarheid, versie beheer en ondernemings beveiliging.

## <a name="comparison-azure-machine-learning-vs-machine-learning-studio-classic"></a>Vergelijking: Azure Machine Learning versus Machine Learning Studio (klassiek)

Hier volgt een snelle vergelijking.

||  Azure Machine Learning Designer|Studio (klassiek) |
|---| --- | --- |
||De ontwerp functie is beschikbaar als preview-versie, Azure Machine Learning GA|Algemeen beschikbaar (GA) | 
|Interface slepen en neerzetten| Ja | Ja|
|Experiment| Schalen met Compute target|Schaal (10 GB training data Limit) | 
|Modules voor Interface| [Veel populaire modules](algorithm-module-reference/module-reference.md) | Allerlei |
|Doelen van de trainings compute| AML Compute (GPU/CPU)|Eigen reken doel, alleen CPU|
|Berekenings doelen afstellen| Azure Kubernetes-service voor realtime-interferentie <br/>AML-Compute voor batch-deinterferentie|De indeling van de oorspronkelijke webservice, niet aanpasbaar | 
|ML-pijp lijn| Pijplijn ontwerpen <br/> Gepubliceerde pijp lijn <br/> Pijplijn eindpunt <br/> [Meer informatie over ML-pijp lijn](concept-ml-pipelines.md)|Niet ondersteund | 
|ML OPS| Configureer bare implementatie, model-en pijplijn versie beheer|Basis model beheer en-implementatie | 
|Model| De standaard indeling, verschillende zijn afhankelijk van de trainings taak|Een eigen, niet-draag bare indeling.| 
|Automatische model training|Nog niet in de ontwerp functie, maar mogelijk via de interface en Sdk's.| Nee | 

## <a name="get-started-with-azure-machine-learning"></a>Aan de slag met Azure Machine Learning

De volgende bronnen kunnen u helpen aan de slag te gaan met Azure Machine Learning

- Lees het [Azure machine learning overzicht](tutorial-first-experiment-automated-ml.md) 

- [Maak uw eerste Design pijp lijn](tutorial-designer-automobile-price-train-score.md) om automatische prijzen te voors pellen.

![Voor beeld van Azure Machine Learning Designer](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Volgende stappen

Naast de mogelijkheden voor slepen en neerzetten in de ontwerp functie is Azure Machine Learning andere hulpprogram ma's beschikbaar:  
  + [Python-notebooks gebruiken voor het trainen & implementeren van ML-modellen](tutorial-1st-experiment-sdk-setup.md)
  + [R-korting gebruiken om & Deploy-modellen te trainen](tutorial-1st-r-experiment.md) 
  + [Automatische machine learning gebruiken om & Deploy-modellen te trainen](tutorial-designer-automobile-price-train-score.md) 
  + [De machine learning CLI gebruiken om een model te trainen en te implementeren](tutorial-train-deploy-model-cli.md)

