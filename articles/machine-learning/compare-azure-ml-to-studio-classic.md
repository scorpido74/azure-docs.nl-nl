---
title: Azure Machine Learning versus ML Studio (klassiek)
description: Hoe Azure Machine Learning verschilt van ML Studio (klassiek)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: dae708a417a1bf8ca4b118f218e353a28e5675c1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684956"
---
# <a name="how-azure-machine-learning-differs-from-ml-studio-classic"></a>Hoe Azure Machine Learning verschilt van ML Studio (klassiek)

In dit artikel worden de functies, mogelijkheden en interface van Azure Machine Learning vergeleken met ML Studio (klassiek). 

## <a name="about-ml-studio-classic"></a>Over ML Studio (klassiek)
[Ml Studio (klassiek)](/studio/what-is-ml-studio.md) is een samen werkende, visuele werk ruimte met slepen en neerzetten, waar u Machine Learning oplossingen kunt bouwen, testen en implementeren zonder dat u code hoeft te schrijven. Hierbij wordt gebruikgemaakt van vooraf gebouwde en vooraf geconfigureerde machine learning-algoritmen en modules voor het verwerken van gegevens, evenals een eigen reken platform.

## <a name="about-azure-machine-learning"></a>Over Azure Machine Learning

Ondertussen biedt [Azure machine learning](/service/overview-what-is-azure-ml.md) een webinterface met de naam Design (preview) **en** verschillende sdk's en CLI om snel gegevens te kunnen prepen, machine learning modellen te trainen en te implementeren. Met Azure Machine Learning u schaal, meerdere Framework-ondersteuning, geavanceerde ML-mogelijkheden, zoals geautomatiseerde machine learning en pijplijn ondersteuning.

Azure Machine Learning Designer biedt een vergelijk bare functionaliteit voor slepen en neerzetten naar Studio (klassiek). Maar in tegens telling tot het eigen reken platform van Studio (klassiek) gebruikt de ontwerper uw eigen reken resources, is schaalbaar en is deze volledig geïntegreerd in Azure Machine Learning.  

> [!TIP]
> Klanten die momenteel gebruikmaken van of evalueren van Machine Learning Studio (klassiek), worden aangemoedigd om [Azure machine learning Designer](https://docs.microsoft.com/azure/machine-learning/service/ui-concept-visual-interface) (preview) te proberen, met behulp van de modules voor slepen en neerzetten, __plus__ schaal baarheid, versie beheer en ondernemings beveiliging.

## <a name="comparison-azure-machine-learning-vs-ml-studio-classic"></a>Vergelijking: Azure Machine Learning versus ML Studio (klassiek)

Hier volgt een snelle vergelijking.

||  Azure Machine Learning Designer|Studio (klassiek) |
|---| --- | --- |
||Designer is beschikbaar als preview-versie, Azure Machine Learning GA|Algemeen beschikbaar (GA) | 
|Interface slepen en neerzetten| Ja | Ja|
|Probeer| Schalen met Compute target|Schaal (10 GB training data Limit) | 
|Modules voor Interface|  Veel populaire modules|Allerlei |
|Doelen van de trainings compute| AML Compute (GPU/CPU)|Eigen reken doel, alleen CPU|
|Berekenings doelen afstellen| Azure Kubernetes-service voor realtime-interferentie <br/>AML-Compute voor batch-deinterferentie|De indeling van de oorspronkelijke webservice, niet aanpasbaar | 
|ML-pijp lijn| Pijplijn ontwerpen <br/> gepubliceerde pijp lijn <br/> Pijplijn eindpunt <br/> [Meer informatie over ML-pijp lijn](service/concept-ml-pipelines.md)|Niet ondersteund | 
|ML OPS| Configureer bare implementatie, model-en pijplijn versie beheer|Basis model beheer en-implementatie | 
|Model| De standaard indeling, verschillende zijn afhankelijk van de trainings taak|Een eigen, niet-draag bare indeling.| 
|Automatische model training|Nog niet in de ontwerp functie, maar mogelijk via de interface en Sdk's.| Nee | 

## <a name="get-started-with-azure-machine-learning-designer"></a>Aan de slag met Azure Machine Learning Designer

De volgende bronnen kunnen u helpen aan de slag te gaan met Azure Machine Learning

- Lees het [Azure machine learning overzicht](service/tutorial-first-experiment-automated-ml.md) 

- [Maak uw eerste Design pijp lijn](service/tutorial-designer-automobile-price-train-score.md) om automatische prijzen te voors pellen.


![Voor beeld van Azure Machine Learning Designer](service/media/concept-ml-pipelines/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Volgende stappen

Naast de mogelijkheden voor slepen en neerzetten in de ontwerp functie is Azure Machine Learning andere hulpprogram ma's beschikbaar:  
  + [Python-notebooks gebruiken voor het trainen & implementeren van ML-modellen](./service/tutorial-1st-experiment-sdk-setup.md)
  + [R-korting gebruiken om & Deploy-modellen te trainen](./service/tutorial-1st-r-experiment.md) 
  + [Automatische machine learning gebruiken om & Deploy-modellen te trainen](./service/tutorial-designer-automobile-price-train-score.md) 
  + [De machine learning CLI gebruiken om een model te trainen en te implementeren](./service/tutorial-train-deploy-model-cli.md)

