---
title: 'Model evalueren: Module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module evaluate model in Azure Machine Learning service om de nauw keurigheid van een getraind model te meten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 17263c8e7300f427b7d82aea65e1f83edf6d6fc4
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128854"
---
# <a name="evaluate-model-module"></a>Model module evalueren

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om de nauw keurigheid van een getraind model te meten. U geeft een gegevensset op die uit een model gegenereerde scores bevat en de module **Evaluate model** berekent een aantal metrische gegevens over de evaluatie van de industrie standaard.
  
 De metrische gegevens die door het **Evalueer model** worden geretourneerd, zijn afhankelijk van het type model dat u evalueert:  
  
-   **Classificatie modellen**    
-   **Regressie modellen**    



> [!TIP]
> Als u geen ervaring hebt met het model, raden we u aan de video serie door Dr. Stephen Elston, als onderdeel van de [machine learning cursus](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) van EdX. 


Er zijn drie manieren om de module **Evaluate model** te gebruiken:

+ Genereer scores voor uw trainings gegevens en evalueer het model op basis van deze scores
+ Scores op het model genereren, maar deze scores vergelijken met scores op een gereserveerde testset
+ Vergelijk de scores voor twee verschillende, gerelateerde modellen, met dezelfde set gegevens

## <a name="use-the-training-data"></a>De trainings gegevens gebruiken

Als u een model wilt evalueren, moet u een gegevensset verbinden die een set van invoer kolommen en scores bevat.  Als er geen andere gegevens beschikbaar zijn, kunt u de oorspronkelijke gegevensset gebruiken.

1. Verbind de Score van de **gescoorde gegevensset** van het [score model](./score-model.md) met de invoer van een **evaluatie model**. 
2. Klik op **model module evalueren** en voer het experiment uit om de evaluatie scores te genereren.

## <a name="use-testing-data"></a>Test gegevens gebruiken

Een veelvoorkomend scenario in machine learning is het scheiden van uw oorspronkelijke gegevensset in trainings-en test gegevens sets, [](./split-data.md) met behulp van de gesplitste module of de [partitie-en voorbeeld](./partition-and-sample.md) module. 

1. Verbind de Score van de **gescoorde gegevensset** van het [score model](score-model.md) met de invoer van een **evaluatie model**. 
2. Verbind de uitvoer van de module voor gesplitste gegevens die de test gegevens bevat naar de rechter invoer van het **Evalueer model**.
2. Klik op **model module evalueren** en selecteer **geselecteerde uitvoeren** om de evaluatie scores te genereren.

## <a name="compare-scores-from-two-models"></a>Scores van twee modellen vergelijken

U kunt ook een tweede set scores verbinden om het **model te evalueren**.  De scores zijn mogelijk een gedeelde evaluatieset met bekende resultaten of een set resultaten van een ander model voor dezelfde gegevens.

Deze functie is handig omdat u de resultaten van twee verschillende modellen op dezelfde gegevens eenvoudig kunt vergelijken. U kunt ook scores van twee verschillende uitvoeringen vergelijken met dezelfde gegevens met verschillende para meters.

1. Verbind de Score van de **gescoorde gegevensset** van het [score model](score-model.md) met de invoer van een **evaluatie model**. 
2. Koppel de uitvoer van de module score model voor het tweede model aan de rechter invoer van het **Evalueer model**.
3. Klik met de rechter muisknop op **model evalueren**en selecteer **geselecteerde uitvoeren** om de evaluatie scores te genereren.

## <a name="results"></a>Resultaten

Nadat u het **model evalueren**hebt uitgevoerd, klikt u met de rechter muisknop op de module en selecteert u **evaluatie resultaten** om de resultaten te bekijken. U kunt:

+ Sla de resultaten op als een gegevensset, zodat u gemakkelijker kunt analyseren met andere hulpprogram ma's
+ Een visualisatie in de interface genereren

Als u gegevens sets verbindt met zowel de invoer van een **Evalueer model**, bevatten de resultaten metrische gegevens voor beide sets of beide modellen.
Het model of de gegevens die aan de linker poort zijn gekoppeld, worden eerst in het rapport weer gegeven, gevolgd door de metrieken voor de gegevensset of het model dat is gekoppeld aan de juiste poort.  

De volgende afbeelding vertegenwoordigt bijvoorbeeld een vergelijking van de resultaten van twee cluster modellen die zijn gebouwd op dezelfde gegevens, maar met verschillende para meters.  

![AML&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Omdat dit een cluster model is, zijn de evaluatie resultaten anders dan wanneer u scores van twee regressie modellen of twee classificatie modellen hebt vergeleken. De gehele presentatie is echter hetzelfde. 

## <a name="metrics"></a>Metrische gegevens

In deze sectie worden de metrische gegevens die worden geretourneerd voor de specifieke typen modellen beschreven die worden ondersteund voor gebruik met het evaluate- **model**:

+ [classificatie modellen](#bkmk_classification)
+ [regressie modellen](#bkmk_regression)

###  <a name="bkmk_classification"></a>Metrische gegevens voor classificatie modellen

De volgende metrische gegevens worden gerapporteerd bij het evalueren van classificatie modellen. Als u modellen vergelijkt, worden ze gerangschikt op basis van de metrische gegevens die u voor de evaluatie selecteert.  
  
-   **Nauw keurigheid** meet de goed keuring van een classificatie model als het aandeel van de werkelijke resultaten tot het totale aantal cases.  
  
-   **Nauw keurigheid** is het aandeel van de werkelijke resultaten ten opzichte van alle positieve resultaten.  
  
-   **Intrekken** is de Fractie van alle juiste resultaten die door het model worden geretourneerd.  
  
-   **F-Score** wordt berekend als het gewogen gemiddelde van de precisie en intrekken tussen 0 en 1, waarbij de ideale waarde voor de F-score 1 is.  
  
-   **AUC** meet het gebied onder de curve die is getekend met echte positieven op de y-as en fout-positieven op de x-as. Deze meet waarde is nuttig omdat deze een enkel getal bevat waarmee u modellen van verschillende typen kunt vergelijken.  
  
- **Gemiddeld logboek verlies** is een enkele score die wordt gebruikt om de sanctie voor verkeerde resultaten te drukken. Het wordt berekend als het verschil tussen de twee waarschijnlijke verdelingen: de werkelijke waarde en de naam van het model.  
  
- **Verlies van trainings logboeken** is een enkele score die het voor deel van de classificatie op een wille keurige voor spelling weergeeft. Het logboek verlies meet de onzekerheid van uw model door de waarschijnlijkheid dat het wordt uitgevoerd, te vergelijken met de bekende waarden van de labels. U wilt het logboek verlies voor het model als geheel minimaliseren.

##  <a name="bkmk_regression"></a>Metrische gegevens voor regressie modellen
 
De metrische gegevens die voor regressie modellen worden geretourneerd, zijn in het algemeen ontworpen om de hoeveelheid fout te schatten.  Een model wordt gezien als een goede grootte van de gegevens als het verschil tussen de waargenomen en voorspelde waarden klein is. Gezien het patroon van de verschillen (het verschil tussen een voorspeld punt en de bijbehorende werkelijke waarde), kan u echter een heleboel informatie over mogelijke afwijking in het model vertellen.  
  
 De volgende metrische gegevens worden gerapporteerd voor het evalueren van regressie modellen. Wanneer u modellen vergelijkt, worden ze gerangschikt op basis van de metrische gegevens die u voor de evaluatie selecteert.  
  
- **Gemiddelde absolute fout (Mae)** meet hoe dicht de voor spellingen zijn voor de werkelijke resultaten. een lagere score is dus beter.  
  
- **Root mean error (RMSE)** maakt een enkele waarde die de fout in het model samenvat. Door het verschil te squaringen, wordt het verschil tussen de voor spelling en de onderdictie van de metriek genegeerd.  
  
- **Relatieve absolute fout (Rae)** is het relatieve absolute verschil tussen de verwachte en werkelijke waarden; relatief omdat het gemiddelde verschil wordt gedeeld door het reken kundige gemiddelde.  
  
- Met **relatieve kwadratische fout (RSE)** wordt het totale kwadraat van de voorspelde waarden op dezelfde manier genormaliseerd door te delen door de totale kwadraat fout van de werkelijke waarden.  
  
- **Gemiddelde nul een fout (MZOE)** geeft aan of de voor spelling juist is of niet.  Met andere woorden: `ZeroOneLoss(x,y) = 1` wanneer `x!=y`; anders `0`.
  
- De **determinatie coëfficiënt**, vaak R<sup>2</sup>genoemd, vertegenwoordigt de voorspellende kracht van het model als een waarde tussen 0 en 1. Nul betekent dat het model wille keurig is (er wordt niets uitgelegd). 1 betekent dat er een perfecte aanpassing is. Wees echter voorzichtig bij het interpreteren van R<sup>2</sup> -waarden, omdat lage waarden volledig normaal kunnen zijn en hoge waarden kunnen worden verdacht.
  

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 