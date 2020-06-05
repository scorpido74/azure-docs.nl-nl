---
title: Meer informatie over geautomatiseerde ML-resultaten
titleSuffix: Azure Machine Learning
description: Meer informatie over het weer geven en begrijpen van grafieken en metrische gegevens voor elk van uw geautomatiseerde machine learning uitvoeringen.
services: machine-learning
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 12/05/2019
ms.openlocfilehash: e424163a2fc07d92eee110396cf8cce57e686622
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84431698"
---
# <a name="understand-automated-machine-learning-results"></a>Geautomatiseerde machine learning-resultaten begrijpen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u de diagrammen en metrische gegevens voor elk van uw geautomatiseerde machine learning-uitvoeringen kunt weer geven en begrijpen. 

Meer informatie over:
+ [Metrische gegevens, grafieken en curven voor classificatie modellen](#classification)
+ [Metrische gegevens, grafieken en grafieken voor regressie modellen](#regression)
+ [De interpretatie van modellen en de urgentie van het onderdeel](#explain-model)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een gratis account aan voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Maak een experiment voor uw geautomatiseerde machine learning uitvoeren, hetzij met de SDK, hetzij in Azure Machine Learning Studio.

    * De SDK gebruiken om een [classificatie model](how-to-auto-train-remote.md) of [regressie model](tutorial-auto-train-models.md) te bouwen
    * Gebruik [Azure machine learning Studio](how-to-use-automated-ml-for-ml-models.md) om een classificatie of regressie model te maken door de juiste gegevens te uploaden.

## <a name="view-the-run"></a>De uitvoering weer geven

Nadat u een geautomatiseerd machine learning experiment hebt uitgevoerd, kunt u een overzicht van de uitvoeringen vinden in uw machine learning-werk ruimte. 

1. Ga naar uw werkruimte.

1. Selecteer **experimenten**in het linkerdeel venster van de werk ruimte.

   ![Scherm opname van het menu experiment](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Selecteer in de lijst met experimenten het abonnement dat u wilt verkennen.

   [![Proef lijst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Selecteer in de onderste tabel de **uitvoeren**.

   [ ![ Experimenteel uitvoeren](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. Selecteer in de modellen de **algoritme naam** voor het model dat u verder wilt verkennen.

   [![Model experimenteren](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

U ziet ook dezelfde resultaten tijdens het uitvoeren wanneer u de Jupyter- `RunDetails` [widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)gebruikt.

## <a name="classification-results"></a><a name="classification"></a>Classificatie resultaten

Thee de volgende metrische gegevens en grafieken zijn beschikbaar voor elk classificatie model dat u bouwt met behulp van de geautomatiseerde machine learning mogelijkheden van Azure Machine Learning

+ [Metrische gegevens](#classification-metrics)
+ [Verwar ring matrix](#confusion-matrix)
+ [Nauw keurigheid: grafiek intrekken](#precision-recall-chart)
+ [Bewerkings kenmerken van ontvanger (of ROC)](#roc)
+ [Bocht](#lift-curve)
+ [Toename curve](#gains-curve)
+ [Kalibratie tekening](#calibration-plot)

### <a name="classification-metrics"></a>Metrische classificaties

De volgende metrische gegevens worden opgeslagen in elke uitvoerings herhaling voor een classificatie taak.

Metrisch|Beschrijving|Berekening|Extra para meters
--|--|--|--
AUC_Macro| AUC is het gebied onder de ontvanger van het besturings systeem. Macro is het reken kundige gemiddelde van de AUC voor elke klasse.  | [Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Gem = "macro"|
AUC_Micro| AUC is het gebied onder de ontvanger van het besturings systeem. Micro wordt wereld wijd berekend door de werkelijke positieven en onwaare positieven van elke klasse te combi neren.| [Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | gemiddelde = "micro"|
AUC_Weighted  | AUC is het gebied onder de ontvanger van het besturings systeem. Gewogen is het reken kundige gemiddelde van de score voor elke klasse, gewogen op basis van het aantal werkelijke instanties in elke klasse.| [Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|gemiddeld = "gewogen"
accuracy|Nauw keurigheid is het percentage voorspelde labels dat exact overeenkomt met de werkelijke labels. |[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Geen|
average_precision_score_macro|De gemiddelde nauw keurigheid van een curve is een gebogen lijn die het gewogen gemiddelde van de nauw keurigheid van elke drempel waarde bereikt, waarbij de toename van de vorige drempel waarde die als gewicht wordt gebruikt, wordt ingetrokken. Macro is het reken kundige gemiddelde van de gemiddelde precisie Score van elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Gem = "macro"|
average_precision_score_micro|De gemiddelde nauw keurigheid van een curve is een gebogen lijn die het gewogen gemiddelde van de nauw keurigheid van elke drempel waarde bereikt, waarbij de toename van de vorige drempel waarde die als gewicht wordt gebruikt, wordt ingetrokken. Micro wordt wereld wijd berekend door de werkelijke positieven en de fout-positieven bij elke afsluit proces te combi neren.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|gemiddelde = "micro"|
average_precision_score_weighted|De gemiddelde nauw keurigheid van een curve is een gebogen lijn die het gewogen gemiddelde van de nauw keurigheid van elke drempel waarde bereikt, waarbij de toename van de vorige drempel waarde die als gewicht wordt gebruikt, wordt ingetrokken. Gewogen is het reken kundige gemiddelde van de gemiddelde precisie score voor elke klasse, gewogen op basis van het aantal werkelijke instanties in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|gemiddeld = "gewogen"|
balanced_accuracy|Nauw keurigheid in evenwicht is het reken kundige gemiddelde van intrekken voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Gem = "macro"|
f1_score_macro|F1-Score is het harmonische gemiddelde van Precision en intrekken. Macro is het reken kundige gemiddelde van de F1-score voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Gem = "macro"|
f1_score_micro|F1-Score is het harmonische gemiddelde van Precision en intrekken. Micro wordt wereld wijd berekend door het totale aantal positieve, onwaare negatieven en valse positieven te tellen.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|gemiddelde = "micro"|
f1_score_weighted|F1-Score is het harmonische gemiddelde van Precision en intrekken. Gewogen gemiddelde per klasse frequentie van F1-score voor elke klasse|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|gemiddeld = "gewogen"|
log_loss|Dit is de functie verlies die wordt gebruikt in (MultiNomial) logistiek regressie en uitbrei dingen van IT, zoals Neural-netwerken, gedefinieerd als de negatieve kans op Logboeken van de werkelijke labels op basis van de voor spellingen van een Probabilistic-classificatie. Voor één steek proef met True label YT in {0,1} en geschatte waarschijnlijkheid YP die YT = 1 is, is het logboek verlies van log P (yt&#124;YP) =-(YT log (YP) + (1-YT) logboek (1-YP)).|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Geen|
norm_macro_recall|Genormaliseerde macro intrekken is macro intrekken genormaliseerd, zodat wille keurige prestaties een Score van 0 hebben en de perfecte prestaties een Score van 1 hebben. Dit wordt bereikt door norm_macro_recall: = (recall_score_macro-R)/(1-R), waarbij R de verwachte waarde is van recall_score_macro voor wille keurige voor spellingen (R = 0,5 voor binaire classificatie en R = (1/C) voor problemen met de classificatie van C-klasse).|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Gem = "macro" |
precision_score_macro|Precisie is het percentage positieve voorspelde elementen met een juiste label. Macro is het reken kundige gemiddelde van de precisie voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Gem = "macro"|
precision_score_micro|Precisie is het percentage positieve voorspelde elementen met een juiste label. Micro wordt wereld wijd berekend door het totale aantal positieve en foutieve positieven te tellen.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|gemiddelde = "micro"|
precision_score_weighted|Precisie is het percentage positieve voorspelde elementen met een juiste label. Gewogen is het reken kundige gemiddelde van de precisie voor elke klasse, gewogen op basis van het aantal werkelijke instanties in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|gemiddeld = "gewogen"|
recall_score_macro|Intrekken is het percentage van de juiste label elementen van een bepaalde klasse. Macro is het reken kundige gemiddelde van intrekken voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Gem = "macro"|
recall_score_micro|Intrekken is het percentage van de juiste label elementen van een bepaalde klasse. Micro wordt wereld wijd berekend door het totale aantal positieve, onwaare negatieven en foutieve positieven te tellen|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|gemiddelde = "micro"|
recall_score_weighted|Intrekken is het percentage van de juiste label elementen van een bepaalde klasse. Gewogen is het reken kundige gemiddelde van intrekken voor elke klasse, gewogen op basis van het aantal werkelijke instanties in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|gemiddeld = "gewogen"|
weighted_accuracy|Gewogen nauw keurigheid is nauw keurig wanneer het gewicht dat aan elk voor beeld is gegeven, gelijk is aan het aandeel van de werkelijke instanties in dat voor beeld van de klasse waar.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight is een vector die gelijk is aan het aandeel van die klasse voor elk element in het doel|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Verwar ring matrix

#### <a name="what-is-a-confusion-matrix"></a>Wat is een Verwar ring-matrix?
Een Verwar ring matrix wordt gebruikt om de prestaties van een classificatie model te beschrijven. Elke rij geeft de instanties van de echte of werkelijke klasse in uw gegevensset weer, en elke kolom vertegenwoordigt de exemplaren van de klasse die door het model is voor speld. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Wat doet automatische ML met de Verwar ring-matrix?
Voor classificatie problemen biedt Azure Machine Learning automatisch een Verwar ring matrix voor elk model dat is gebouwd. Voor elke Verwar ring wordt de frequentie weer gegeven van elk voorspeld label (kolom) vergeleken met het werkelijke label (Row). Hoe donkerder de kleur, hoe hoger het aantal in het betreffende deel van de matrix. 

#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
De werkelijke waarde van de gegevensset wordt vergeleken met de voorspelde waarden die het model heeft gekregen. Als gevolg hiervan hebben machine learning modellen een grotere nauw keurigheid als het model de meeste waarden in de diagonaal heeft, wat betekent dat het model de juiste waarde voor speld heeft. Als een model klasse onevenwichtig heeft, helpt de Verwar ring matrix bij het detecteren van een omliggend model.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Voor beeld 1: een classificatie model met een slechte nauw keurigheid
![Een classificatie model met een slechte nauw keurigheid](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Voor beeld 2: een classificatie model met hoge nauw keurigheid 
![Een classificatie model met hoge nauw keurigheid](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Voor beeld 3: een classificatie model met hoge nauw keurigheid en hoge afwijking in model voorspellingen
![Een classificatie model met hoge nauw keurigheid en hoge afwijking in model voorspellingen](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Nauw keurigheid: grafiek intrekken
#### <a name="what-is-a-precision-recall-chart"></a>Wat is een precisie: een grafiek intrekken?
De curve voor het intrekken van precisie toont de relatie tussen Precision en intrekken van een model. De term precisie geeft aan dat de mogelijkheid voor een model om alle exemplaren correct te labelen. Intrekken vertegenwoordigt de mogelijkheid voor een classificatie om alle exemplaren van een bepaald label te vinden.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Wat doen automatische ML met de grafiek voor precisie intrekken?

Met deze grafiek kunt u de curven voor het intrekken van precisie vergelijken voor elk model om te bepalen welk model een acceptabele relatie heeft tussen Precision en intrekken voor uw specifieke bedrijfs probleem. Dit diagram toont de gemiddelde precisie van Macro's: terughalen, gemiddelde precisie van micron-terughalen en de nauwkeurigheids intrekken die zijn gekoppeld aan alle klassen voor een model. 

Macro: berekent de metrische gegevens onafhankelijk van elke klasse en neemt vervolgens het gemiddelde over, waarbij alle klassen gelijk worden behandeld. Met micro gemiddelde worden de bijdragen van alle klassen echter geaggregeerd om het gemiddelde te berekenen. Micro-Gem is de voor keur als er sprake is van een klasse-onevenwichtigheid in de gegevensset.

#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
Afhankelijk van het doel van het bedrijfs probleem, kan de perfecte nauw keurigheid voor het intrekken van de precisie verschillen. Enkele voor beelden worden hieronder gegeven

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Voor beeld 1: een classificatie model met lage precisie en weinig intrekken
![Een classificatie model met lage precisie en weinig intrekken](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Voor beeld 2: een classificatie model met ~ 100% Precision en ~ 100% intrekken 
![Een classificatie model met hoge precisie en intrekken](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>ROC diagram

#### <a name="what-is-a-roc-chart"></a>Wat is een ROC diagram?
Het besturings kenmerk van de ontvanger (of ROC) is een tekening van de juist geclassificeerde labels versus de onjuist geclassificeerde labels voor een bepaald model. De ROC-curve kan minder informatieve zijn bij het trainen van modellen op gegevens sets met een hoge afwijking, omdat er geen positieve labels worden weer gegeven.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Wat doet automatische ML in het schema ROC?
Automated ML genereert de macro gemiddelde nauwkeurigheid-terughalen, gemiddelde Precision-intrekken en de uitroepen van de precisie die zijn gekoppeld aan alle klassen voor een model. 

Macro: berekent de metrische gegevens onafhankelijk van elke klasse en neemt vervolgens het gemiddelde over, waarbij alle klassen gelijk worden behandeld. Met micro gemiddelde worden de bijdragen van alle klassen echter geaggregeerd om het gemiddelde te berekenen. Micro-Gem is de voor keur als er sprake is van een klasse-onevenwichtigheid in de gegevensset.

#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
In het ideale geval heeft het model dichter bij 100% op ware positieve frequentie en dichter bij 0% ONWAAR positief. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Voor beeld 1: een classificatie model met lage labels en hoge onwaar labels
![Classificatie model met lage labels en hoog/onwaar labels](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Voor beeld 2: een classificatie model met hoogwaardige labels en laagloze labels
![een classificatie model met hoogwaardige labels en lage labels met onwaar](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Lift-grafiek
#### <a name="what-is-a-lift-chart"></a>Wat is een lift diagram?
Lift grafieken worden gebruikt om de prestaties van een classificatie model te evalueren. Er wordt weer gegeven hoe beter u kunt verwachten met het gegenereerde model, vergeleken met zonder model wat betreft nauw keurigheid.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Wat doet automatische ML met het lift diagram?
U kunt de lift van het model dat automatisch is gebouwd met Azure Machine Learning, vergelijken met de basis lijn om de waarde van het betreffende model te bekijken.
#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Voor beeld 1: een classificatie model dat erger is dan een wille keurig selectie model
![Een classificatie model dat erger is dan een wille keurig selectie model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Voor beeld 2: een classificatie model dat beter presteert dan een wille keurig selectie model
![Een classificatie model dat beter presteert](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Grafiek met toename
#### <a name="what-is-a-gains-chart"></a>Wat is een winst diagram?

Een winst diagram evalueert de prestaties van een classificatie model door elk deel van de gegevens. Er wordt weer gegeven voor elk percentiel van de gegevensset, hoe beter u kunt verwachten dat ze worden uitgevoerd ten opzichte van een wille keurig selectie model.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Wat doet automatische ML in het organigram?
Gebruik het diagram cumulatieve toename om u te helpen bij het kiezen van de indelings beperking met behulp van een percentage dat overeenkomt met een gewenste toename van het model. Deze informatie biedt een andere manier om de resultaten in de bijbehorende Lift-grafiek te bekijken.

#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Voor beeld 1: een classificatie model met minimale toename
![een classificatie model met minimale toename](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Voor beeld 2: een classificatie model met aanzienlijke winst
![Een classificatie model met aanzienlijke winst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Kalibratie diagram

#### <a name="what-is-a-calibration-chart"></a>Wat is een ijklijn?
Er wordt een kalibratie tekening gebruikt om het vertrouwen van een voorspellend model weer te geven. Dit doet u door de relatie tussen de voorspelde waarschijnlijkheid en de werkelijke kans weer te geven, waarbij ' kans ' staat voor de kans dat een bepaalde instantie tot een bepaald label behoort.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Wat doet automatische ML met de ijklijn?
Voor alle classificatie problemen kunt u de kalibratie lijn voor micro-Average, macro-Gem en elke klasse in een bepaald voorspellend model controleren.

Macro: berekent de metrische gegevens onafhankelijk van elke klasse en neemt vervolgens het gemiddelde over, waarbij alle klassen gelijk worden behandeld. Met micro gemiddelde worden de bijdragen van alle klassen echter geaggregeerd om het gemiddelde te berekenen. 
#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
 Een goed gekalibreerd model wordt uitgelijnd met de lijn y = x, waar het redelijk is in de voor spellingen. Een model dat groter is dan wordt uitgelijnd met de lijn y = 0, waar de voorspelde kans aanwezig is, maar er is geen werkelijke kans. 


##### <a name="example-1-a-well-calibrated-model"></a>Voor beeld 1: een goed gekalibreerd model
![ meer goed gekalibreerd model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Voor beeld 2: een over-vertrouwen model
![Een over-vertrouwen model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a>Regressie resultaten

Thee de volgende metrische gegevens en grafieken zijn beschikbaar voor elk regressie model dat u bouwt met behulp van de geautomatiseerde machine learning mogelijkheden van Azure Machine Learning

+ [Metrische gegevens](#reg-metrics)
+ [Voorspeld versus waar](#pvt)
+ [Histogram van verschillen](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a>Metrische gegevens over regressie

De volgende metrische gegevens worden opgeslagen in elke uitvoerings herhaling voor een regressie-of prognose taak.

|Metrisch|Beschrijving|Berekening|Extra para meters
--|--|--|--|
explained_variance|De uitleg afwijking is de verhouding waarmee een wiskundig model account voor de variatie van een bepaalde gegevensset is gedefinieerd. Het is het percentage afname van de oorspronkelijke gegevens tot de variantie van de fouten. Wanneer het gemiddelde van de fouten 0 is, is deze gelijk aan de uitleg afwijking.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Geen|
r2_score|R2 is de coëfficiënt voor het bepalen van de berekening of het percentage verlaging in kwadratische fouten vergeleken met een basis lijn model dat het gemiddelde uitvoert. |[Berekening](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Geen|
spearman_correlation|' Spearman correlatie ' is een niet-parametrische meting van de monotonicity van de relatie tussen twee gegevens sets. In tegens telling tot de correlatie van Pearson, neemt de ' Spearman-correlatie niet in dat beide gegevens sets normaal gesp roken worden gedistribueerd. Net als bij andere correlatie coëfficiënten is dit een verschil tussen-1 en + 1 en 0 voor geen correlatie. Correlaties van-1 of + 1 impliceren een nauw keurige monotone relatie. Positieve correlaties impliceren dat als x stijgt, dus y. Negatieve correlaties impliceren dat x stijgt, y afneemt.|[Berekening](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Geen|
mean_absolute_error|De gemiddelde absolute fout is de verwachte waarde van de absolute waarde van het verschil tussen het doel en de voor spelling|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Geen|
normalized_mean_absolute_error|De genormaliseerde gemiddelde absolute fout is een absolute fout gedeeld door het bereik van de gegevens|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Delen door bereik van de gegevens|
median_absolute_error|Mediaan absolute fout is de mediaan van alle absolute verschillen tussen het doel en de voor spelling. Dit verlies is robuust voor uitbijters.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Geen|
normalized_median_absolute_error|Genormaliseerde mediaan absolute fout is de gemiddelde absolute fout gedeeld door het bereik van de gegevens|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Delen door bereik van de gegevens|
root_mean_squared_error|Root mean error is de vierkantswortel van het verwachte verschil in kwadraat tussen het doel en de voor spelling|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Geen|
normalized_root_mean_squared_error|Genormaliseerd root-kwadraat fout is het root-kwadraat fout gedeeld door het bereik van de gegevens|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Delen door bereik van de gegevens|
root_mean_squared_log_error|Het wortel gemiddelde van het logaritmische fout is de vierkantswortel van de verwachte kwadratische fout|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Geen|
normalized_root_mean_squared_log_error|Genormaliseerde logaritmische fout in kwadraat is het belangrijkste gemiddelde logboek fout gedeeld door het bereik van de gegevens|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Delen door bereik van de gegevens|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a>Voor speld versus waar grafiek
#### <a name="what-is-a-predicted-vs-true-chart"></a>Wat is een voor speld versus waar grafiek?
Voor speld versus waar ziet u de relatie tussen een voorspelde waarde en de bijbehorende waarde voor het correleren van een regressie probleem. Deze grafiek kan worden gebruikt om de prestaties van een model te meten als dichter bij de y = x-lijn de voorspelde waarden zijn, hoe beter de nauw keurigheid van een voorspellend model is.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Wat doen automatische ML met de voor spelling en ware grafiek?
Na elke uitvoering ziet u een voorspelde versus ware grafiek voor elk regressie model. Ter bescherming van de privacy van gegevens zijn waarden binning samen en de grootte van elke bin wordt weer gegeven als een staaf diagram in het onderste gedeelte van het grafiek gebied. U kunt het voorspellende model vergelijken met het lichtere scha kering van de fout marges, vergeleken met de ideale waarde van waar het model moet zijn.

#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Voor beeld 1: een classificatie model met lage nauw keurigheid
![Een regressie model met lage nauw keurigheid in voor spellingen](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Voor beeld 2: een regressie model met hoge nauw keurigheid 
[![Een regressie model met hoge nauw keurigheid in de voor spellingen](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a>Grafiek met het histogram van verschillen
#### <a name="what-is-a-residuals-chart"></a>Wat is een verschillen diagram?
Een residu vertegenwoordigt een waargenomen y: de voorspelde y. Als u een fout marge met een lage afwijking wilt weer geven, moet het histogram van de resten worden gevormd als klok curve, gecentreerd rond 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Wat doet automatische ML in het diagram verschillen?
Automatische MILLILITERs bieden automatisch een grafiek van verschillen om de verdeling van fouten in de voor spellingen weer te geven.
#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
Een goed model heeft meestal een klok curve of fouten rond nul.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Voor beeld 1: een regressie model met afwijking van fouten
![SA-regressie model met afwijking van fouten](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Voor beeld 2: een regressie model met meer gelijkmatige verdeling van fouten
![Een regressie model met een meer gelijkmatige verdeling van fouten](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a>De interpretatie van modellen en de urgentie van het onderdeel
Automated ML biedt een machine learning interpretable dash board voor uw uitvoeringen.
Zie voor meer informatie over het inschakelen van functies voor het maken van interpretaties de [procedures](how-to-machine-learning-interpretability-automl.md) voor het inschakelen van de functie voor het maken van interpretiteit in geautomatiseerde ml experimenten.

## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over [automatische ml](concept-automated-ml.md) in azure machine learning.
+ Probeer de voorbeeld notitieblokken voor [automatische machine learning-modellen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) .
