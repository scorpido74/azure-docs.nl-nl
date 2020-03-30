---
title: Een machine learning-algoritme selecteren
titleSuffix: Azure Machine Learning
description: Azure Machine Learning-algoritmen selecteren voor onder toezicht en onbewaakt leren in clustering-, classificatie- of regressieexperimenten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 03/05/2020
ms.openlocfilehash: e0482bac9569a834adf3e1cdef2b3f702980eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328660"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Algoritmen selecteren voor Azure Machine Learning

Een veel voorkomende vraag is "Welk machine learning-algoritme moet ik gebruiken?" Het algoritme dat u selecteert, is voornamelijk afhankelijk van twee verschillende aspecten van uw scenario voor gegevenswetenschap:

 - **Wat wilt u met uw gegevens doen?** In het bijzonder, wat is de zakelijke vraag die u wilt beantwoorden door te leren van uw gegevens uit het verleden?

 - **Wat zijn de vereisten van uw data science scenario?** Wat is met name de nauwkeurigheid, trainingstijd, lineariteit, aantal parameters en het aantal functies dat uw oplossing ondersteunt?

 ![Overwegingen voor het kiezen van algoritmen: Wat wilt u weten? Wat zijn de scenariovereisten?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Bedrijfsscenario's en het opspeelblad voor machine learning-algoritmen

De [Azure Machine Learning Algorithm Cheat Sheet](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri) helpt u met de eerste overweging: Wat wilt u doen met uw **gegevens?** Zoek op het opspeelblad voor machine learning-algoritmen naar taken die u wilt uitvoeren en zoek vervolgens een [Azure Machine Learning-ontwerpalgoritme](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri) voor de oplossing voor voorspellende analyses. 

Machine Learning ontwerper biedt een uitgebreid portfolio van algoritmen, zoals [Multiclass Decision Forest](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri), [Aanbeveling systemen,](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri) [Neural Network Regression](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri), [Multiclass Neural Network](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri), en [K-Means Clustering](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri). Elk algoritme is ontworpen om een ander type machine learning-probleem aan te pakken. Zie het [Machine Learning-ontwerpalgoritme en moduleverwijzing](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) voor een volledige lijst, samen met documentatie over hoe elk algoritme werkt en hoe parameters kunnen worden afgestemd om het algoritme te optimaliseren.

> [!NOTE]
> Als u het spiekbriefje van het machine learning-algoritme wilt downloaden, gaat u naar [het spiekblad voor Azure Machine learning-algoritmen](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri).
> 
> 

Houd, samen met richtlijnen in het Azure Machine Learning Algorithm Cheat Sheet, rekening met andere vereisten bij het kiezen van een machine learning-algoritme voor uw oplossing. Hieronder volgen aanvullende factoren om rekening mee te houden, zoals de nauwkeurigheid, trainingstijd, lineariteit, aantal parameters en het aantal functies.

## <a name="additional-requirements-for-a-data-science-scenario"></a>Aanvullende vereisten voor een data science-scenario

Zodra u weet wat u met uw gegevens wilt doen, moet u aanvullende vereisten voor uw oplossing bepalen. 

Maak keuzes en eventueel trade-offs voor de volgende vereisten:

- Nauwkeurigheid
- Trainingstijd
- Lineariteit
- Aantal parameters
- Aantal functies

## <a name="accuracy"></a>Nauwkeurigheid

Nauwkeurigheid in machine learning meet de effectiviteit van een model als het aandeel van de werkelijke resultaten tot de totale gevallen. In Machine Learning-ontwerper berekent de [module Model evalueren](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri) een reeks industriestandaardevaluatiestatistieken. U deze module gebruiken om de nauwkeurigheid van een getraind model te meten.

Het verkrijgen van het meest nauwkeurige antwoord mogelijk is niet altijd nodig. Soms is een benadering voldoende, afhankelijk van waar u het voor wilt gebruiken. Als dat het geval is, u in staat zijn om uw verwerkingstijd drastisch te snijden door vast te houden met meer benaderende methoden. Geschatte methoden hebben ook natuurlijk de neiging om overfitting te voorkomen.

Er zijn drie manieren om de module Model evalueren te gebruiken:

- Scores genereren over uw trainingsgegevens om het model te evalueren
- Scores genereren op het model, maar vergelijk deze scores met scores op een gereserveerde testset
- Vergelijk scores voor twee verschillende, maar verwante modellen met dezelfde set gegevens

Zie [Modelmodule evalueren](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri)voor een volledige lijst met statistieken en benaderingen die u gebruiken om de nauwkeurigheid van machine learning-modellen te evalueren.

## <a name="training-time"></a>Trainingstijd

Bij begeleid leren betekent training het gebruik van historische gegevens om een machine learning-model te bouwen dat fouten minimaliseert. Het aantal minuten of uren dat nodig is om een model te trainen varieert sterk tussen algoritmen. De trainingstijd is vaak nauw verbonden met nauwkeurigheid; de ene begeleidt meestal de andere. 

Bovendien zijn sommige algoritmen gevoeliger voor het aantal gegevenspunten dan andere. U een specifiek algoritme kiezen omdat u een tijdsbeperking hebt, vooral wanneer de gegevensset groot is.

In Machine Learning-ontwerper is het maken en gebruiken van een machine learning-model meestal een proces in drie stappen:

1.  Configureer een model door een bepaald type algoritme te kiezen en vervolgens de parameters of hyperparameters te definiëren. 

2.  Geef een gegevensset op die is gelabeld en gegevens heeft die compatibel zijn met het algoritme. Sluit zowel de gegevens als het model aan op de [module Model trainen.](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri)

3.  Nadat de training is voltooid, gebruikt u het getrainde model met een van de [scoremodules](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri) om voorspellingen te doen over nieuwe gegevens.

## <a name="linearity"></a>Lineariteit

Lineariteit in statistieken en machine learning betekent dat er een lineaire relatie is tussen een variabele en een constante in uw gegevensset. Lineaire classificatiealgoritmen gaan er bijvoorbeeld van uit dat klassen kunnen worden gescheiden door een rechte lijn (of de hoger-dimensionale analoog).

Veel machine learning-algoritmen maken gebruik van lineariteit. In Azure Machine Learning designer bevatten ze: 

- [Multiclass logistieke regressie](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Twee-klasse logistieke regressie](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Vectormachines ondersteunen](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

Lineaire regressiealgoritmen gaan ervan uit dat gegevenstrends een rechte lijn volgen. Deze veronderstelling is niet slecht voor sommige problemen, maar voor anderen vermindert het nauwkeurigheid. Ondanks hun nadelen zijn lineaire algoritmen populair als eerste strategie. Ze hebben de neiging om algoritmisch eenvoudig en snel te trainen.

![Niet-lineaire klassegrens](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***Niet-lineaire klassegrens***: *Vertrouwen op een lineair classificatiealgoritme zou resulteren in een lage nauwkeurigheid.*

![Gegevens met een niet-lineaire trend](./media/how-to-select-algorithms/nonlinear-trend.png)

***Gegevens met een niet-lineaire trend***: *Het gebruik van een lineaire regressiemethode zou veel grotere fouten genereren dan nodig is.*

## <a name="number-of-parameters"></a>Aantal parameters

Parameters zijn de knoppen die een gegevenswetenschapper mag draaien bij het instellen van een algoritme. Het zijn getallen die het gedrag van het algoritme beïnvloeden, zoals fouttolerantie of aantal iteraties, of opties tussen varianten van hoe het algoritme zich gedraagt. De trainingstijd en nauwkeurigheid van het algoritme kunnen soms gevoelig zijn voor het verkrijgen van precies de juiste instellingen. Algoritmen met grote aantallen parameters vereisen meestal de meeste trial and error om een goede combinatie te vinden.

Als alternatief is er de [Tune Model Hyperparameters module](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri) in Machine Learning ontwerper: Het doel van deze module is het bepalen van de optimale hyperparameters voor een machine learning model. De module bouwt en test meerdere modellen met behulp van verschillende combinaties van instellingen. Het vergelijkt metrische gegevens over alle modellen om de combinaties van instellingen te krijgen. 

Hoewel dit een geweldige manier is om ervoor te zorgen dat u de parameterruimte hebt overspannen, neemt de tijd die nodig is om een model te trainen exponentieel toe met het aantal parameters. Het voordeel is dat het hebben van veel parameters meestal aangeeft dat een algoritme meer flexibiliteit heeft. Het kan vaak een zeer goede nauwkeurigheid bereiken, mits u de juiste combinatie van parameterinstellingen vinden.

## <a name="number-of-features"></a>Aantal functies

In machine learning is een functie een kwantificeerbare variabele van het fenomeen dat u probeert te analyseren. Voor bepaalde typen gegevens kan het aantal functies zeer groot zijn in vergelijking met het aantal gegevenspunten. Dit is vaak het geval met genetica of tekstuele gegevens. 

Een groot aantal functies kan veen naar beneden sommige leren algoritmen, waardoor de opleiding tijd onhaalbaar lang. [Ondersteuningsvectormachines](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) zijn bijzonder geschikt voor scenario's met een groot aantal functies. Om deze reden zijn ze gebruikt in veel toepassingen van het ophalen van informatie tot tekst- en afbeeldingsclassificatie. Ondersteuningsvectormachines kunnen worden gebruikt voor zowel classificatie- als regressietaken.

Functieselectie verwijst naar het proces waarbij statistische tests worden toegepast op ingangen, met een opgegeven uitvoer. Het doel is om te bepalen welke kolommen meer voorspellend zijn voor de uitvoer. De [module Filtergebaseerde functieselectie](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri) in Machine Learning-ontwerper biedt meerdere algoritmes voor het selecteren van functies om uit te kiezen. De module bevat correlatiemethoden zoals Pearson-correlatie en chi-kwadraatwaarden.

U ook de [module Permutatiefunctiebelang](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri) gebruiken om een set functiebelangscores voor uw gegevensset te berekenen. U deze scores vervolgens gebruiken om u te helpen bij het bepalen van de beste functies die u in een model gebruiken.


## <a name="next-steps"></a>Volgende stappen

 - [Meer informatie over Azure Machine Learning designer](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - Zie Machine Learning designer algoritme en module referentie voor beschrijvingen van alle machine learning-algoritmen die beschikbaar zijn in Azure Machine [Learning-ontwerper](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)
 - Zie [Deep Learning vs. Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri) om de relatie tussen deep learning, machine learning en AI te verkennen
