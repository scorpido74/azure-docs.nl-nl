---
title: Beheer veelvoorkomende ML-modelvalkuilen met geautomatiseerde machine learning.
titleSuffix: Azure Machine Learning
description: Identificeer en beheer veelvoorkomende valkuilen van ML-modellen met de geautomatiseerde machine learning-oplossingen van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/27/2020
ms.openlocfilehash: e0bc1aa48dfb40ea146fa79fdfd57da841ca1404
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385530"
---
# <a name="manage-ml-pitfalls-with-automated-machine-learning"></a>Ml-valkuilen beheren met geautomatiseerde machine learning

Over-fitting en onevenwichtige gegevens zijn veel voorkomende valkuilen bij het bouwen van machine learning-modellen. Standaard biedt de geautomatiseerde machine learning van Azure Machine Learning grafieken en statistieken om u te helpen deze risico's te identificeren en implementeert u best practices om ze te beperken. 

## <a name="identify-over-fitting"></a>Overfitting identificeren

Over-fitting in machine learning treedt op wanneer een model te goed bij de trainingsgegevens past en daardoor niet nauwkeurig kan voorspellen op ongeziene testgegevens. Met andere woorden, het model heeft eenvoudig specifieke patronen en ruis in de trainingsgegevens uit het hoofd geleerd, maar is niet flexibel genoeg om voorspellingen te doen over echte gegevens.

Denk aan de volgende getrainde modellen en de bijbehorende trein- en testnauwkeurigheid.

| Model | Treinnauwkeurigheid | Testnauwkeurigheid |
|-------|----------------|---------------|
| A | 99,9% | 95% |
| B | 87% | 87% |
| C | 99,9% | 45% |

Gezien model **A,** is er een veel voorkomende misvatting dat als de testnauwkeurigheid op onzichtbare gegevens lager is dan de nauwkeurigheid van de opleiding, het model over-uitgerust is. Echter, test nauwkeurigheid moet altijd minder dan training nauwkeurigheid, en het onderscheid voor over-fit vs adequaat fit komt neer op *hoeveel* minder nauwkeurig. 

Bij het vergelijken van modellen **A** en **B**, model **A** is een beter model, omdat het een hogere testnauwkeurigheid heeft, en hoewel de testnauwkeurigheid is iets lager op 95%, het is niet een significant verschil dat suggereert over-fitting aanwezig is. Je zou niet kiezen voor model **B** gewoon omdat de trein en test nauwkeurigheid zijn dichter bij elkaar.

Model **C** is een duidelijk geval van overmontage; de nauwkeurigheid van de training is zeer hoog, maar de nauwkeurigheid van de test is niet ergens in de buurt zo hoog. Dit onderscheid is subjectief, maar komt voort uit kennis van uw probleem en gegevens, en welke grootheden van fouten aanvaardbaar zijn.

## <a name="prevent-over-fitting"></a>Overmontage voorkomen

In de meest flagrante gevallen, een over-ingepast model zal aannemen dat de functie waarde combinaties gezien tijdens de training zal altijd resulteren in exact dezelfde output voor het doel.

De beste manier om overfitting te voorkomen is door ml-best practices te volgen, waaronder:

* Meer trainingsgegevens gebruiken en statistische bias elimineren
* Doellekkage voorkomen
* Minder functies gebruiken
* **Regularisatie en hyperparameter optimalisatie**
* **Complexiteitsbeperkingen van het model**
* **Kruisvalidatie**

In de context van geautomatiseerde ML zijn de eerste drie bovenstaande items **best-practices die u implementeert.** De laatste drie vetgedrukte items zijn **best-practices geautomatiseerdml implementeert** standaard te beschermen tegen over-fitting. In andere instellingen dan geautomatiseerde ML zijn alle zes best-practices de moeite waard om te voorkomen dat overpassende modellen over-fitting modellen.

### <a name="best-practices-you-implement"></a>Best practices die u implementeert

Het gebruik van **meer gegevens** is de eenvoudigste en best mogelijke manier om overmontage te voorkomen, en als een toegevoegde bonus verhoogt meestal de nauwkeurigheid. Wanneer u meer gegevens gebruikt, wordt het moeilijker voor het model om exacte patronen te onthouden en wordt het gedwongen om oplossingen te bereiken die flexibeler zijn om aan meer voorwaarden te voldoen. Het is ook belangrijk om **statistische bias**te herkennen, om ervoor te zorgen dat uw trainingsgegevens geen geïsoleerde patronen bevatten die niet zullen bestaan in live-voorspellingsgegevens. Dit scenario kan moeilijk op te lossen zijn, omdat er mogelijk niet over-fitting tussen uw trein en testsets, maar er kan over-fitting aanwezig in vergelijking met live testgegevens.

Doellekkage is een soortgelijk probleem, waarbij u misschien niet over-fitting tussen trein / test sets te zien, maar het verschijnt op voorspelling-tijd. Doellekkage treedt op wanneer uw model "cheats" tijdens de training door het hebben van toegang tot gegevens die het normaal gesproken niet zou moeten hebben op voorspelling-tijd. Bijvoorbeeld, als uw probleem is om te voorspellen op maandag wat een commodity prijs zal zijn op vrijdag, maar een van uw functies per ongeluk opgenomen gegevens van donderdag, dat zou gegevens van het model zal niet hebben op voorspelling-tijd, omdat het niet kan zien in de toekomst. Doel lekkage is een gemakkelijke fout te missen, maar wordt vaak gekenmerkt door abnormaal hoge nauwkeurigheid voor uw probleem. Als u probeert om voorraadprijs te voorspellen en een model bij 95% nauwkeurigheid opleidde, is er waarschijnlijk doellekkage ergens in uw eigenschappen.

Het verwijderen van functies kan ook helpen bij overfitting door te voorkomen dat het model te veel velden heeft om specifieke patronen te onthouden, waardoor het flexibeler wordt. Het kan moeilijk zijn om kwantitatief te meten, maar als u functies verwijderen en dezelfde nauwkeurigheid behouden, hebt u het model waarschijnlijk flexibeler gemaakt en het risico op overmontage verminderd.

### <a name="best-practices-automated-ml-implements"></a>Aanbevolen procedures geautomatiseerde ML implementeert

Regularisatie is het proces van het minimaliseren van een kostenfunctie om complexe en over-ingebouwde modellen te bestraffen. Er zijn verschillende soorten regularisatiefuncties, maar in het algemeen bestraffen ze allemaal de modelcoëfficiëntgrootte, variantie en complexiteit. Geautomatiseerde ML gebruikt L1 (Lasso), L2 (Ridge) en ElasticNet (L1 en L2 tegelijkertijd) in verschillende combinaties met verschillende modelhyperparameter-instellingen die overfitting regelen. In eenvoudige termen, geautomatiseerde ML zal variëren hoeveel een model is geregeld en kies het beste resultaat.

Automated ML implementeert ook expliciete modelcomplexiteitsbeperkingen om overfitting te voorkomen. In de meeste gevallen is deze implementatie specifiek voor beslissingsboom of bosalgoritmen, waar de individuele boom max-diepte beperkt is, en het totale aantal bomen dat wordt gebruikt in bos- of ensembletechnieken beperkt is.

Cross-validatie (CV) is het proces van het nemen van vele subsets van uw volledige trainingsgegevens en het trainen van een model op elke subset. Het idee is dat een model zou kunnen krijgen "geluk" en hebben grote nauwkeurigheid met een subset, maar door het gebruik van vele subsets het model zal niet bereiken deze hoge nauwkeurigheid elke keer. Wanneer u CV doet, verstrekt u een validatie-holdout-gegevensset, geeft u uw CV-plooien op (aantal subsets) en wordt uw model getraind en worden hyperparameters opgesteld om fouten op uw validatieset te minimaliseren. Een CV vouw kan worden over-fit, maar door het gebruik van veel van hen vermindert de kans dat uw uiteindelijke model is over-fit. De afweging is dat CV resulteert in langere trainingstijden en dus hogere kosten, want in plaats van een model een keer te trainen, train je het één keer voor elke *n* CV subsets. 

> [!NOTE]
> Cross-validatie is standaard niet ingeschakeld. het moet worden geconfigureerd in geautomatiseerde ML-instellingen. Nadat cross-validatie is geconfigureerd en er een validatiegegevensset is verstrekt, wordt het proces voor u geautomatiseerd. Raadpleeg 

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Modellen identificeren met onevenwichtige gegevens

Onevenwichtige gegevens worden vaak gevonden in gegevens voor machine learning classificatiescenario's en verwijst naar gegevens die een onevenredige verhouding van waarnemingen in elke klasse bevatten. Deze onbalans kan leiden tot een verkeerd waargenomen positief effect van de nauwkeurigheid van een model, omdat de invoergegevens bias naar één klasse hebben, wat resulteert in het getrainde model om die bias na te bootsen. 

Aangezien classificatiealgoritmen vaak worden geëvalueerd op basis van nauwkeurigheid, is het controleren van de nauwkeurigheidsscore van een model een goede manier om te bepalen of het werd beïnvloed door onevenwichtige gegevens. Had het echt een hoge nauwkeurigheid of echt lage nauwkeurigheid voor bepaalde klassen?

Bovendien genereren geautomatiseerde ML-uitvoeringen automatisch de volgende grafieken, waardoor u de juistheid van de classificaties van uw model begrijpen en modellen identificeren die mogelijk worden beïnvloed door onevenwichtige gegevens.

Grafiek| Beschrijving
---|---
[Verwarring Matrix](how-to-understand-automated-ml.md#confusion-matrix)| Evalueert de correct geclassificeerde labels op basis van de werkelijke labels van de gegevens. 
[Precisie-recall](how-to-understand-automated-ml.md#precision-recall-chart)| Evalueert de verhouding van de juiste labels ten opzichte van de verhouding van gevonden labelexemplaren van de gegevens 
[ROC Curves](how-to-understand-automated-ml.md#roc)| Evalueert de verhouding van de juiste labels ten opzichte van de verhouding van fout-positieve labels.

## <a name="handle-imbalanced-data"></a>Onevenwichtige gegevens verwerken 

Als onderdeel van zijn doel om de machine learning-workflow te vereenvoudigen, heeft geautomatiseerde ML mogelijkheden ingebouwd om onevenwichtige gegevens zoals, 

- Een **gewichtskolom**: geautomatiseerde ML ondersteunt een gewogen kolom als invoer, waardoor rijen in de gegevens omhoog of omlaag worden gewogen, waardoor een klasse min of meer "belangrijk" kan worden.

- De algoritmen die worden gebruikt door geautomatiseerde ML kunnen onbalans tot 20:1 goed verwerken, wat betekent dat de meest voorkomende klasse 20 keer meer rijen in de gegevens kan hebben dan de minst voorkomende klasse.

De volgende technieken zijn extra opties om onevenwichtige gegevens buiten geautomatiseerde ML te verwerken. 

- Resampling naar zelfs de klasse onbalans, hetzij door up-sampling van de kleinere klassen of down-sampling de grotere klassen. Deze methoden vereisen expertise om te verwerken en te analyseren.

- Gebruik een prestatiestatistiek die beter omgaat met onevenwichtige gegevens. Bijvoorbeeld, de F1 score is een gewogen gemiddelde van precisie en terugroepen. Precisie meet de nauwkeurigheid van een classificatie- lage precisie duidt op een groot aantal false positives--, terwijl recall meet de volledigheid van een classificatie- lage terugroepactie duidt op een groot aantal valse negatieven. 

## <a name="next-steps"></a>Volgende stappen

Bekijk voorbeelden en leer hoe u modellen bouwen met behulp van geautomatiseerde machine learning:

+ Volg de [zelfstudie: train automatisch een regressiemodel met Azure Machine Learning](tutorial-auto-train-models.md)

+ De instellingen voor automatisch trainingsexperiment configureren:
  + Gebruik deze stappen [in](how-to-use-automated-ml-for-ml-models.md)Azure Machine Learning-studio.
  + Gebruik bij de Python SDK [deze stappen.](how-to-configure-auto-train.md)


