---
title: Meer informatie over geautomatiseerde ML-resultaten
titleSuffix: Azure Machine Learning
description: Meer informatie over het weer geven en begrijpen van grafieken en metrische gegevens voor elk van uw geautomatiseerde machine learning uitvoeringen.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 69cf79f8258f85f2fb5e787f91aa843837d0a3a1
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75538486"
---
# <a name="understand-automated-machine-learning-results"></a>Automatische machine learning resultaten begrijpen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u de diagrammen en metrische gegevens voor elk van uw geautomatiseerde machine learning-uitvoeringen kunt weer geven en begrijpen. 

Meer informatie over:
+ [Metrische gegevens, grafieken en curven voor classificatie modellen](#classification)
+ [Metrische gegevens, grafieken en grafieken voor regressie modellen](#regression)
+ [De interpretatie van modellen en de urgentie van het onderdeel](#explain-model)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

* Maak een experiment voor uw geautomatiseerde machine learning uitvoeren, hetzij met de SDK, hetzij in Azure Machine Learning Studio.

    * De SDK gebruiken om een [classificatie model](how-to-auto-train-remote.md) of [regressie model](tutorial-auto-train-models.md) te bouwen
    * Gebruik [Azure machine learning Studio](how-to-create-portal-experiments.md) om een classificatie of regressie model te maken door de juiste gegevens te uploaden.

## <a name="view-the-run"></a>De uitvoering weer geven

Nadat u een geautomatiseerd machine learning experiment hebt uitgevoerd, kunt u een overzicht van de uitvoeringen vinden in uw machine learning-werk ruimte. 

1. Ga naar uw werkruimte.

1. Selecteer **experimenten**in het linkerdeel venster van de werk ruimte.

   ![Schermopname van het experiment menu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Selecteer in de lijst met experimenten het abonnement dat u wilt verkennen.

   [lijst met ![experimenten](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Selecteer in de onderste tabel de **uitvoeren**.

   [uitvoering van![-experiment](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. Selecteer in de modellen de **algoritme naam** voor het model dat u verder wilt verkennen.

   [![-experiment model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

U ziet dezelfde resultaten tijdens het uitvoeren wanneer u de [Jupyter-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)`RunDetails`gebruikt.

## <a name="classification"></a>Classificatie resultaten

Thee de volgende metrische gegevens en grafieken zijn beschikbaar voor elk classificatie model dat u bouwt met behulp van de geautomatiseerde machine learning mogelijkheden van Azure Machine Learning

+ [Metrische gegevens](#classification-metrics)
+ [Verwarringsmatrix](#confusion-matrix)
+ [Precisie-/ Oproepdiagram grafiek](#precision-recall-chart)
+ [Ontvanger operationele kenmerken (of ROC)](#roc)
+ [Lift-curve](#lift-curve)
+ [Winsten curve](#gains-curve)
+ [Kalibreren tekengebied](#calibration-plot)

### <a name="classification-metrics"></a>Classificatie metrische gegevens

De volgende metrische gegevens worden opgeslagen in elke uitvoerings herhaling voor een classificatie taak.

Gegevens|Beschrijving|Berekening|Extra Parameters
--|--|--|--
AUC_Macro| AUC is het gebied onder de ontvanger operationele Characteristic-Curve. Macro is het rekenkundige gemiddelde van de AUC voor elke categorie.  | [Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | gemiddelde = "macro"|
AUC_Micro| AUC is het gebied onder de ontvanger operationele Characteristic-Curve. Micro wordt wereld wijd berekend door de werkelijke positieven en onwaare positieven van elke klasse te combi neren.| [Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | gemiddelde = "micro"|
AUC_Weighted  | AUC is het gebied onder de ontvanger operationele Characteristic-Curve. Gewogen is het reken kundige gemiddelde van de score voor elke klasse, gewogen op basis van het aantal werkelijke instanties in elke klasse.| [Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|gemiddelde = "gewogen"
accuracy|Nauwkeurigheid is het percentage van de voorspelde labels die precies overeenkomen met de waarde true labels. |[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Geen|
average_precision_score_macro|Gemiddelde precisie bevat een overzicht van een curve precisie-/ oproepdiagram als het gewogen gemiddelde van Precision-systemen op elke drempelwaarde, met de toename in het intrekken van de vorige drempel gebruikt als het gewicht behaald. Macro is het reken kundige gemiddelde van de gemiddelde precisie Score van elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|gemiddelde = "macro"|
average_precision_score_micro|Gemiddelde precisie bevat een overzicht van een curve precisie-/ oproepdiagram als het gewogen gemiddelde van Precision-systemen op elke drempelwaarde, met de toename in het intrekken van de vorige drempel gebruikt als het gewicht behaald. Micro wordt wereld wijd berekend door de werkelijke positieven en de fout-positieven bij elke afsluit proces te combi neren.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|gemiddelde = "micro"|
average_precision_score_weighted|Gemiddelde precisie bevat een overzicht van een curve precisie-/ oproepdiagram als het gewogen gemiddelde van Precision-systemen op elke drempelwaarde, met de toename in het intrekken van de vorige drempel gebruikt als het gewicht behaald. Gewogen is het reken kundige gemiddelde van de gemiddelde precisie score voor elke klasse, gewogen op basis van het aantal werkelijke instanties in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|gemiddelde = "gewogen"|
balanced_accuracy|Met gelijke taakverdeling nauwkeurigheid is het rekenkundige gemiddelde van terugroeping voor elke categorie.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|gemiddelde = "macro"|
f1_score_macro|F1 score is het gemiddelde harmonische van precisie- en intrekken. Macro is het reken kundige gemiddelde van de F1-score voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|gemiddelde = "macro"|
f1_score_micro|F1 score is het gemiddelde harmonische van precisie- en intrekken. Micro wordt wereld wijd berekend door het totale aantal positieve, onwaare negatieven en valse positieven te tellen.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|gemiddelde = "micro"|
f1_score_weighted|F1 score is het gemiddelde harmonische van precisie- en intrekken. Gewogen gemiddelde frequentie van de klasse van F1 score voor elke objectklasse|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|gemiddelde = "gewogen"|
log_loss|Dit is de verlies-functie die wordt gebruikt voor logistieke regressie (wordt) en -extensies van het zoals neurale netwerken, gedefinieerd als de negatieve log kans van de waarde true labels een probabilistic classificatie voorspellingen gegeven. Voor één steek proef met True label YT in {0,1} en geschatte waarschijnlijke kans YP dat YT = 1, het logboek verlies is: log P&#124;(YT YP) =-(YT log (YP) + (1-YT) logboek (1-YP)).|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Geen|
norm_macro_recall|Genormaliseerde Macro intrekken is Macro intrekken genormaliseerd zodat willekeurige prestaties een score van 0 krijgt en perfecte prestaties een score van 1 krijgt. Dit wordt bereikt door norm_macro_recall: = (recall_score_macro-R)/(1-R), waarbij R de verwachte waarde is van recall_score_macro voor wille keurige voor spellingen (R = 0,5 voor binaire classificatie en R = (1/C) voor problemen met de classificatie van C-klasse).|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Gem = "macro" |
precision_score_macro|Precisie is het percentage positieve voorspelde elementen met een juiste label. Macro is het reken kundige gemiddelde van de precisie voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|gemiddelde = "macro"|
precision_score_micro|Precisie is het percentage positieve voorspelde elementen met een juiste label. Micro wordt wereld wijd berekend door het totale aantal positieve en foutieve positieven te tellen.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|gemiddelde = "micro"|
precision_score_weighted|Precisie is het percentage positieve voorspelde elementen met een juiste label. Gewogen is het reken kundige gemiddelde van de precisie voor elke klasse, gewogen op basis van het aantal werkelijke instanties in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|gemiddelde = "gewogen"|
recall_score_macro|Intrekken is het percentage van de juiste label elementen van een bepaalde klasse. Macro is het reken kundige gemiddelde van intrekken voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|gemiddelde = "macro"|
recall_score_micro|Intrekken is het percentage van de juiste label elementen van een bepaalde klasse. Micro wordt wereld wijd berekend door het totale aantal positieve, onwaare negatieven en foutieve positieven te tellen|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|gemiddelde = "micro"|
recall_score_weighted|Intrekken is het percentage van de juiste label elementen van een bepaalde klasse. Gewogen is het reken kundige gemiddelde van intrekken voor elke klasse, gewogen op basis van het aantal werkelijke instanties in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|gemiddelde = "gewogen"|
weighted_accuracy|Gewogen nauw keurigheid is nauw keurig wanneer het gewicht dat aan elk voor beeld is gegeven, gelijk is aan het aandeel van de werkelijke instanties in dat voor beeld van de klasse waar.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight is gelijk aan het aandeel van die klasse voor elk element in de doel-vector|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Verwarringsmatrix

#### <a name="what-is-a-confusion-matrix"></a>Wat is een Verwar ring-matrix?
Een verwarringsmatrix wordt gebruikt om te beschrijven van de prestaties van een model voor classificatie. Elke rij geeft de instanties van de echte of werkelijke klasse in uw gegevensset weer, en elke kolom vertegenwoordigt de exemplaren van de klasse die door het model is voor speld. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Wat doet automatische ML met de Verwar ring-matrix?
Voor problemen met de classificatie biedt Azure Machine Learning automatisch een verwarringsmatrix voor elk model dat is gebouwd. Voor elke Verwar ring wordt de frequentie weer gegeven van elk voorspeld label (kolom) vergeleken met het werkelijke label (Row). Hoe donkerder de kleur, hoe hoger het aantal in het betreffende deel van de matrix. 

#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
De werkelijke waarde van de gegevensset wordt vergeleken met de voorspelde waarden die het model heeft gekregen. Als gevolg hiervan hebben machine learning modellen een grotere nauw keurigheid als het model de meeste waarden in de diagonaal heeft, wat betekent dat het model de juiste waarde voor speld heeft. Als een model klasse onevenwichtig heeft, helpt de Verwar ring matrix bij het detecteren van een omliggend model.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Voor beeld 1: een classificatie model met een slechte nauw keurigheid
![Een classificatie model met een slechte nauw keurigheid](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Voor beeld 2: een classificatie model met hoge nauw keurigheid 
![Een classificatie model met hoge nauw keurigheid](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Voor beeld 3: een classificatie model met hoge nauw keurigheid en hoge afwijking in model voorspellingen
![Een classificatie model met hoge nauw keurigheid en hoge afwijking in model voorspellingen](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Precisie-/ oproepdiagram grafiek
#### <a name="what-is-a-precision-recall-chart"></a>Wat is een precisie: een grafiek intrekken?
De curve voor het intrekken van precisie toont de relatie tussen Precision en intrekken van een model. De term precisie geeft aan dat de mogelijkheid voor een model om alle exemplaren correct te labelen. Intrekken vertegenwoordigt de mogelijkheid voor een classificatie om alle exemplaren van een bepaald label te vinden.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Wat doen automatische ML met de grafiek voor precisie intrekken?

U kunt de precisie-/ oproepdiagram curven voor elk model om te bepalen welk model u al een acceptabele relatie tussen precisie en intrekken voor uw specifieke zakelijke probleem vergelijken met deze grafiek. In deze grafiek worden precisie-/ Oproepdiagram Macro gemiddeld, gemiddelde Micro-precisie-/ Oproepdiagram en de precisie-/ oproepdiagram die zijn gekoppeld aan alle klassen voor een model. 

Macro: berekent de metrische gegevens onafhankelijk van elke klasse en neemt vervolgens het gemiddelde over, waarbij alle klassen gelijk worden behandeld. Met micro gemiddelde worden de bijdragen van alle klassen echter geaggregeerd om het gemiddelde te berekenen. Micro-Gem is de voor keur als er sprake is van een klasse-onevenwichtigheid in de gegevensset.

#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
Afhankelijk van het doel van het bedrijfs probleem, kan de perfecte nauw keurigheid voor het intrekken van de precisie verschillen. Enkele voor beelden worden hieronder gegeven

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Voor beeld 1: een classificatie model met lage precisie en weinig intrekken
![Een classificatie model met lage precisie en weinig intrekken](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Voor beeld 2: een classificatie model met ~ 100% Precision en ~ 100% intrekken 
![een classificatie model met hoge precisie en intrekken](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>ROC diagram

#### <a name="what-is-a-roc-chart"></a>Wat is een ROC diagram?
Ontvanger operationele kenmerken (of ROC) is een diagram van de labels correct ingedeeld versus de onjuist ingedeeld labels voor een bepaald model. De ROC-curve zijn minder informatieve als training modellen op gegevenssets met hoge vertekening, zoals deze niet in de ONWAAR positief labels weergegeven wordt.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Wat doet automatische ML in het schema ROC?
Automated ML genereert de macro gemiddelde nauwkeurigheid-terughalen, gemiddelde Precision-intrekken en de uitroepen van de precisie die zijn gekoppeld aan alle klassen voor een model. 

Macro: berekent de metrische gegevens onafhankelijk van elke klasse en neemt vervolgens het gemiddelde over, waarbij alle klassen gelijk worden behandeld. Met micro gemiddelde worden de bijdragen van alle klassen echter geaggregeerd om het gemiddelde te berekenen. Micro-Gem is de voor keur als er sprake is van een klasse-onevenwichtigheid in de gegevensset.

#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
In het ideale geval heeft het model dichter bij 100% op ware positieve frequentie en dichter bij 0% ONWAAR positief. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Voor beeld 1: een classificatie model met lage labels en hoge onwaar labels
![Classificatie model met lage labels en hoog/onwaar labels](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Voor beeld 2: een classificatie model met hoogwaardige labels en laagloze labels
![een classificatie model met hoogwaardige labels en lage labels voor onwaar](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Lift-grafiek
#### <a name="what-is-a-lift-chart"></a>Wat is een lift diagram?
Lift-grafieken worden gebruikt voor het evalueren van de prestaties van een model voor classificatie. Er wordt weer gegeven hoe beter u kunt verwachten met het gegenereerde model, vergeleken met zonder model wat betreft nauw keurigheid.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Wat doet automatische ML met het lift diagram?
U kunt de lift van het model automatisch gemaakt met Azure Machine Learning aan de basislijn om te bekijken van de toename van de waarde van dat specifieke model vergelijken.
#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Voor beeld 1: een classificatie model dat erger is dan een wille keurig selectie model
![Een classificatie model dat erger is dan een wille keurig selectie model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Voor beeld 2: een classificatie model dat beter presteert dan een wille keurig selectie model
![een classificatie model dat betere](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Grafiek met toename
#### <a name="what-is-a-gains-chart"></a>Wat is een winst diagram?

Een diagram kolomopslag evalueert de prestaties van een model classificatie op basis van elk deel van de gegevens. Hier ziet voor elke percentiel van de gegevensset, hoeveel u kunt beter verwachten om uit te voeren vergeleken met een willekeurige selectie-model.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Wat doet automatische ML in het organigram?
Met de cumulatieve winsten grafiek kunt u de classificatie afsluitdatum met behulp van een percentage dat overeenkomt met een gewenste winst uit het model kiezen. Deze informatie geeft een andere manier om de resultaten in de bijbehorende lift-grafiek kijken.

#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Voor beeld 1: een classificatie model met minimale toename
![een classificatie model met minimale toename](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Voor beeld 2: een classificatie model met aanzienlijke winst
![een classificatie model met grote winst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Kalibratie diagram

#### <a name="what-is-a-calibration-chart"></a>Wat is een ijklijn?
Een diagram kalibreren wordt gebruikt om het vertrouwen van een Voorspellend model weer te geven. Dit gebeurt door de relatie tussen de voorspelde kans en de werkelijke kans op, waarbij "kans" vertegenwoordigt de kans dat een bepaalde instantie deel uitmaakt van een label.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Wat doet automatische ML met de ijklijn?
Voor alle problemen met de classificatie, kunt u de regel kalibreren voor micro-gemiddelde macro-gemiddelde en elke klasse in een bepaalde Voorspellend model bekijken.

Macro: berekent de metrische gegevens onafhankelijk van elke klasse en neemt vervolgens het gemiddelde over, waarbij alle klassen gelijk worden behandeld. Met micro gemiddelde worden de bijdragen van alle klassen echter geaggregeerd om het gemiddelde te berekenen. 
#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
 Een goed gekalibreerd model wordt uitgelijnd met de lijn y = x, waar het redelijk is in de voor spellingen. Een model te veel confident worden uitgelijnd met de y = 0 regel, waar de voorspelde kans is aanwezig, maar er is geen daadwerkelijke kans. 


##### <a name="example-1-a-well-calibrated-model"></a>Voor beeld 1: een goed gekalibreerd model
![ meer goed gekalibreerd model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Voor beeld 2: een over-vertrouwen model
![Een over-vertrouwen model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Regressie resultaten

Thee de volgende metrische gegevens en grafieken zijn beschikbaar voor elk regressie model dat u bouwt met behulp van de geautomatiseerde machine learning mogelijkheden van Azure Machine Learning

+ [Metrische gegevens](#reg-metrics)
+ [Voorspeld versus waar](#pvt)
+ [Histogram van dit](#histo)


### <a name="reg-metrics"></a>Metrische gegevens over regressie

De volgende metrische gegevens worden opgeslagen in elke uitvoerings herhaling voor een regressie-of prognose taak.

|Gegevens|Beschrijving|Berekening|Extra Parameters
--|--|--|--|
explained_variance|Uitgelegd afwijking is de verhouding die een wiskundige model gebruikersaccounts voor de variant van een bepaalde verzameling. Het is dat het percentage variantie van de oorspronkelijke gegevens om de afwijking van de fouten te verlagen. Wanneer het gemiddelde van de fouten 0 is, is het gelijk aan de afwijking van uitgelegd.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Geen|
r2_score|R2 is de coëfficiënt bepalen of de procent vermindering in gekwadrateerde fouten ten opzichte van een basislijn-model dat u het gemiddelde weergeeft. |[Berekening](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Geen|
spearman_correlation|Spearman correlatie is een nonparametric meting van de monotonicity van de relatie tussen twee gegevenssets. In tegenstelling tot de correlatie Pearson de correlatie Spearman wordt niet vanuit gegaan dat beide gegevenssets normaal gesproken worden gedistribueerd. Dit varieert net als andere coëfficiënten correlatie tussen-1 en + 1 met 0, wat geen correlatie impliceert. Correlaties op basis van -1 of + 1 houdt in dat een exacte monotone relatie. Positieve correlaties impliceren dat x toeneemt, dus y wordt. Negatieve correlaties impliceren dat x toeneemt, y afneemt.|[Berekening](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Geen|
mean_absolute_error|Mean absolute fout is de verwachte waarde van de absolute waarde van het verschil tussen het doel en de voorspelling|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Geen|
normalized_mean_absolute_error|Genormaliseerde mean absolute fout is mean Absolute Error gedeeld door het bereik van de gegevens|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Delen door het bereik van de gegevens|
median_absolute_error|Mediaan absolute fout is de mediaan van alle absolute verschillen tussen het doel en de voorspelling. Dit verlies is robuuste naar uitbijters.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Geen|
normalized_median_absolute_error|Genormaliseerde mediaan absolute fout is mediaan absolute fout gedeeld door het bereik van de gegevens|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Delen door het bereik van de gegevens|
root_mean_squared_error|Root mean squared fout is de vierkantswortel van het verwachte gekwadrateerde verschil tussen het doel en de voorspelling|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Geen|
normalized_root_mean_squared_error|Genormaliseerde hoofdmap mean squared fout root mean squared fout gedeeld door het bereik van de gegevens is|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Delen door het bereik van de gegevens|
root_mean_squared_log_error|Root mean squared log-fout is de vierkantswortel van de verwachte gekwadrateerde logaritmische fout|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Geen|
normalized_root_mean_squared_log_error|Genormaliseerde logaritmische fout in kwadraat is het belangrijkste gemiddelde logboek fout gedeeld door het bereik van de gegevens|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Delen door het bereik van de gegevens|

### <a name="pvt"></a>Voor speld versus waar grafiek
#### <a name="what-is-a-predicted-vs-true-chart"></a>Wat is een voor speld versus waar grafiek?
Voor speld versus waar ziet u de relatie tussen een voorspelde waarde en de bijbehorende waarde voor het correleren van een regressie probleem. Deze grafiek kan worden gebruikt voor het meten van prestaties van een model als de dichter bij de y = x regel de voorspelde waarden zijn, hoe beter de nauwkeurigheid van een Voorspellend model.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Wat doen automatische ML met de voor spelling en ware grafiek?
Na elke uitvoering ziet u een voorspelde versus waar graph voor elke regressiemodel. Als u wilt beveiligen privacy van gegevens, waarden samen worden binned en de grootte van elke bin wordt weergegeven als een staafdiagram in het onderste gedeelte van het grafiekgebied. U kunt het voorspellende model vergelijken met de lichtere schaduw-gebied met fout marges, op basis van de optimale waarde van waar het model moet worden.

#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Voor beeld 1: een classificatie model met lage nauw keurigheid
![Een regressie model met lage nauw keurigheid in voor spellingen](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Voor beeld 2: een regressie model met hoge nauw keurigheid 
[Een regressie model ![met hoge nauw keurigheid in de voor spellingen](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Grafiek met het histogram van verschillen
#### <a name="what-is-a-residuals-chart"></a>Wat is een verschillen diagram?
Een resterende vertegenwoordigt een waargenomen y – de voorspelde y. Om weer te geven een foutmarge met lage afwijking, moet het histogram van dit als een rinkelende bel curve, gecentreerd rond 0 worden vormgegeven. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Wat doet automatische ML in het diagram verschillen?
Automatische MILLILITERs bieden automatisch een grafiek van verschillen om de verdeling van fouten in de voor spellingen weer te geven.
#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
Een goed model heeft meestal een klok curve of fouten rond nul.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Voor beeld 1: een regressie model met afwijking van fouten
![SA-regressie model met afwijking van fouten](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Voor beeld 2: een regressie model met meer gelijkmatige verdeling van fouten
![Een regressie model met een meer gelijkmatige verdeling van fouten](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>De interpretatie van modellen en de urgentie van het onderdeel
Automated ML biedt een machine learning interpretable dash board voor uw uitvoeringen.
Zie voor meer informatie over het inschakelen van functies voor het maken van interpretaties de [procedures](how-to-machine-learning-interpretability-automl.md) voor het inschakelen van de functie voor het maken van interpretiteit in geautomatiseerde ml experimenten.

## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over [automatische ml](concept-automated-ml.md) in azure machine learning.
+ Probeer de voorbeeld notitieblokken voor [automatische machine learning-modellen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) .
