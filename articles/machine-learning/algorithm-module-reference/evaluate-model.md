---
title: 'Model evalueren: modulereferentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Model evalueren in Azure Machine Learning om de nauwkeurigheid van een getraind model te meten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: c1bcbb6a368c9c80f968c48c1a6e0bc6c95133d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456401"
---
# <a name="evaluate-model-module"></a>Modelmodule evalueren

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om de nauwkeurigheid van een getraind model te meten. U geeft een gegevensset met scores die uit een model zijn gegenereerd en de module **Model evalueren** berekent een reeks industriestandaardevaluatiestatistieken.
  
 De statistieken die worden geretourneerd per **model evalueren,** zijn afhankelijk van het type model dat u evalueert:  
  
-   **Classificatiemodellen**    
-   **Regressiemodellen**  
-   **Clustermodellen**  


> [!TIP]
> Als je nieuw bent in modelevaluatie, raden we de videoserie van Dr. Stephen Elston aan, als onderdeel van de [machine learning-cursus](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) van EdX. 


Er zijn drie manieren om de module **Model evalueren** te gebruiken:

+ Scores genereren over uw trainingsgegevens en het model evalueren op basis van deze scores
+ Scores genereren op het model, maar vergelijk deze scores met scores op een gereserveerde testset
+ Vergelijk scores voor twee verschillende, maar verwante modellen met dezelfde set gegevens

## <a name="use-the-training-data"></a>De trainingsgegevens gebruiken

Als u een model wilt evalueren, moet u een gegevensset met een set invoerkolommen en -scores verbinden.  Als er geen andere gegevens beschikbaar zijn, u uw oorspronkelijke gegevensset gebruiken.

1. Verbind de uitvoer van de **scoreset van** het [scoremodel](./score-model.md) met de invoer van **Het evalueren van model**. 
2. Klik **op Module Model evalueren** en voer de pijplijn uit om de evaluatiescores te genereren.

## <a name="use-testing-data"></a>Testgegevens gebruiken

Een veelvoorkomend scenario in machine learning is het scheiden van uw oorspronkelijke gegevensset in trainings- en testgegevenssets, met behulp van de [module Splitsen](./split-data.md) of de module Partitie [en Voorbeeld.](./partition-and-sample.md) 

1. Verbind de uitvoer van de **scoreset van** het [scoremodel](score-model.md) met de invoer van **Het evalueren van model**. 
2. Sluit de uitvoer van de module Gesplitste gegevens die de testgegevens bevat, aan op de rechterinvoer van **Evaluate Model**.
2. Klik **op Module Model evalueren** en selecteer Geselecteerd **uitvoeren** om de evaluatiescores te genereren.

## <a name="compare-scores-from-two-models"></a>Vergelijk scores van twee modellen

U ook een tweede set scores koppelen aan **Model evalueren.**  De scores kunnen een gedeelde evaluatieset zijn die resultaten heeft gekend, of een reeks resultaten van een ander model voor dezelfde gegevens.

Deze functie is handig omdat u eenvoudig resultaten van twee verschillende modellen op dezelfde gegevens vergelijken. U scores van twee verschillende runs vergelijken met dezelfde parameters met verschillende parameters.

1. Verbind de uitvoer van de **scoreset van** het [scoremodel](score-model.md) met de invoer van **Het evalueren van model**. 
2. Sluit de uitvoer van de module Scoremodel voor het tweede model aan op de rechterinvoer van **Evaluate Model.**
3. Verzend de pijplijn.

## <a name="results"></a>Resultaten

Nadat u **Het model evalueren**hebt uitgevoerd, klikt u met de rechtermuisknop op de module en selecteert u **Evaluatieresultaten visualiseren** om de resultaten te bekijken.

Als u gegevenssets koppelt aan beide ingangen van **Evaluate Model,** bevatten de resultaten statistieken voor beide gegevenssets of beide modellen.
Het model of de gegevens die aan de linkerpoort zijn gekoppeld, worden eerst in het rapport weergegeven, gevolgd door de statistieken voor de gegevensset of het model dat aan de rechterpoort is gekoppeld.  

De volgende afbeelding vertegenwoordigt bijvoorbeeld een vergelijking van de resultaten van twee clustermodellen die op dezelfde gegevens zijn gebouwd, maar met verschillende parameters.  

![Vergelijken2Modellen](media/module/evaluate-2-models.png)  

Omdat dit een clusteringmodel is, zijn de evaluatieresultaten anders dan wanneer u scores van twee regressiemodellen vergeleek of twee classificatiemodellen vergeleek. De algehele presentatie is echter hetzelfde. 

## <a name="metrics"></a>Metrische gegevens

In deze sectie worden de statistieken beschreven die zijn geretourneerd voor de specifieke typen modellen die worden ondersteund voor gebruik met **Evaluatiemodel:**

+ [classificatiemodellen](#metrics-for-classification-models)
+ [regressiemodellen](#metrics-for-regression-models)
+ [clustermodellen](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Statistieken voor classificatiemodellen

De volgende statistieken worden gerapporteerd bij het evalueren van classificatiemodellen.
  
-   **Nauwkeurigheid** meet de goedheid van een classificatiemodel als het aandeel van de werkelijke resultaten tot de totale gevallen.  
  
-   **Precisie** is het aandeel van de werkelijke resultaten over alle positieve resultaten.  
  
-   **Recall** is de fractie van alle juiste resultaten die door het model worden geretourneerd.  
  
-   **F-score** wordt berekend als het gewogen gemiddelde van precisie en terugroepen tussen 0 en 1, waar de ideale F-score waarde 1 is.  
  
-   **AUC** meet het gebied onder de curve uitgezet met echte positieven op de y-as en false positives op de x-as. Deze statistiek is handig omdat het een enkel getal biedt waarmee u modellen van verschillende typen vergelijken.  
  
- **Gemiddeld logverlies** is een enkele score die wordt gebruikt om de straf uit te drukken voor verkeerde resultaten. Het wordt berekend als het verschil tussen twee waarschijnlijkheidsverdelingen - de ware, en de ene in het model.  
  
- **Training log verlies** is een enkele score die het voordeel van de classificatie ten opzichte van een willekeurige voorspelling vertegenwoordigt. Het logverlies meet de onzekerheid van uw model door de waarschijnlijkheden die het uitproduceert te vergelijken met de bekende waarden (grondwaarheid) in de labels. U wilt logboekverlies voor het model als geheel minimaliseren.

### <a name="metrics-for-regression-models"></a>Statistieken voor regressiemodellen
 
De statistieken die zijn geretourneerd voor regressiemodellen zijn ontworpen om de hoeveelheid fouten te schatten.  Een model wordt geacht goed bij de gegevens te passen als het verschil tussen waargenomen en voorspelde waarden klein is. Echter, kijkend naar het patroon van de resten (het verschil tussen een voorspeld punt en de bijbehorende werkelijke waarde) kan u veel vertellen over potentiële bias in het model.  
  
 De volgende statistieken worden gerapporteerd voor het evalueren van regressiemodellen.
  
- **Gemiddelde absolute fout (MAE)** meet hoe dicht de voorspellingen zijn bij de werkelijke resultaten; dus, een lagere score is beter.  
  
- **Als hoofdgemiddelde kwadraatfout (RMSE)** wordt één waarde gemaakt die de fout in het model samenvat. Door het verschil te kwadrateren, negeert de statistiek het verschil tussen overvoorspelling en onder-voorspelling.  
  
- **Relatieve absolute fout (RAE)** is het relatieve absolute verschil tussen verwachte en werkelijke waarden; relatief omdat het gemiddelde verschil wordt gedeeld door het rekenkundig gemiddelde.  
  
- **Relatieve kwadraatfout (RSE)** normaliseert op dezelfde manier de totale kwadraatfout van de voorspelde waarden door te delen door de totale kwadraatfout van de werkelijke waarden.  
  

  
- **De bepalingscoëfficiënt**, ook wel R<sup>2</sup>genoemd, vertegenwoordigt de voorspellende kracht van het model als waarde tussen 0 en 1. Nul betekent dat het model willekeurig is (verklaart niets); 1 betekent dat er een perfecte pasvorm is. Voorzichtigheid is echter geboden bij<sup>2</sup> het interpreteren van R 2-waarden, omdat lage waarden volkomen normaal kunnen zijn en hoge waarden verdacht kunnen zijn.

###  <a name="metrics-for-clustering-models"></a>Statistieken voor clustermodellen

Omdat clustermodellen in veel opzichten aanzienlijk verschillen van classificatie- en regressiemodellen, [retourneert Evaluate Model](evaluate-model.md) ook een andere set statistieken voor clustermodellen.  
  
 De statistieken die worden geretourneerd voor een clusteringmodel beschrijven hoeveel gegevenspunten aan elk cluster zijn toegewezen, de hoeveelheid scheiding tussen clusters en hoe strak de gegevenspunten binnen elk cluster zijn gebundeld.  
  
 De statistieken voor het clustermodel worden gemiddeld over de gehele gegevensset, met extra rijen met de statistieken per cluster.  
  
De volgende statistieken worden gerapporteerd voor het evalueren van clusteringmodellen.
    
-   De scores in de kolom, **Gemiddelde afstand tot ander centrum,** geven aan hoe dicht, gemiddeld, elk punt in het cluster is bij de centroids van alle andere clusters.   

-   De scores in de **kolom, Gemiddelde afstand tot clustercentrum,** vertegenwoordigen de nabijheid van alle punten in een cluster met de centroid van dat cluster.  
  
-   De kolom **Aantal punten** geeft aan hoeveel gegevenspunten aan elk cluster zijn toegewezen, samen met het totale totale aantal gegevenspunten in een cluster.  
  
     Als het aantal gegevenspunten dat aan clusters is toegewezen kleiner is dan het totale aantal beschikbare gegevenspunten, betekent dit dat de gegevenspunten niet aan een cluster kunnen worden toegewezen.  
  
-   De scores in de kolom, **Maximale afstand tot clustercentrum,** vertegenwoordigen de som van de afstanden tussen elk punt en de centroid van het cluster van dat punt.  
  
     Als dit aantal hoog is, kan dit betekenen dat het cluster op grote schaal wordt verspreid. U moet deze statistiek samen met het **gemiddelde afstand tot clustercentrum** bekijken om de spreiding van het cluster te bepalen.   

-   De **gecombineerde evaluatiescore** onder aan elke sectie resultaten geeft de gemiddelde scores weer voor de clusters die in dat specifieke model zijn gemaakt.  
  

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 