---
title: Resultaten van AutoML-experiment evalueren
titleSuffix: Azure Machine Learning
description: Meer informatie over het weer geven en evalueren van grafieken en metrische gegevens voor elk van uw geautomatiseerde machine learning experimenten.
services: machine-learning
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 10/09/2020
ms.topic: conceptual
ms.custom: how-to, contperfq2
ms.openlocfilehash: 681e965d5fb64e35374b580cbbb238defd619492
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311483"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Resultaten van automatische machine learning experimenten evalueren

In dit artikel vindt u informatie over het weer geven en evalueren van de resultaten van uw geautomatiseerde machine learning, AutoML, experimenten. Deze experimenten bestaan uit meerdere uitvoeringen, waarbij elke uitvoering een model maakt. Om u te helpen elk model te evalueren, genereert AutoML automatisch metrische gegevens over prestaties en grafieken die specifiek zijn voor uw soort experiment. 

AutoML biedt bijvoorbeeld verschillende grafieken voor classificatie-en regressie modellen. 

|Classificatie|Regressie
|---|---|
|<li> [Verwarringsmatrix](#confusion-matrix) <li>[Nauw keurigheid: grafiek intrekken](#precision-recall-chart) <li> [Bewerkings kenmerken van ontvanger (of ROC)](#roc) <li> [Bocht](#lift-curve)<li> [Toename curve](#gains-curve)<li> [Kalibratie tekening](#calibration-plot) | <li> [Voorspeld versus waar](#pvt) <li> [Histogram van verschillen](#histo)|

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Maak een experiment voor uw geautomatiseerde machine learning uitvoeren, hetzij met de SDK, hetzij in Azure Machine Learning Studio.

    * De SDK gebruiken om een [classificatie model](how-to-auto-train-remote.md) of [regressie model](tutorial-auto-train-models.md) te bouwen
    * Gebruik [Azure machine learning Studio](how-to-use-automated-ml-for-ml-models.md) om een classificatie of regressie model te maken door de juiste gegevens te uploaden.

## <a name="view-run-results"></a>Uitvoerings resultaten weer geven

Nadat uw geautomatiseerde machine learning-experiment is voltooid, kunt u een overzicht van de uitvoeringen vinden in uw machine learning-werk ruimte via de [Azure machine learning Studio](overview-what-is-machine-learning-studio.md). 

Voor SDK-experimenten ziet u dezelfde resultaten tijdens het uitvoeren wanneer u de `RunDetails` [Jupyter-widget](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py)gebruikt.

In de volgende stappen en animatie ziet u hoe u de uitvoerings geschiedenis en de metrische gegevens over prestaties en grafieken van een specifiek model in Studio kunt weer geven.

![Stappen voor het weer geven van uitvoerings geschiedenis en statistieken en grafieken voor model prestaties](./media/how-to-understand-automated-ml/view-run-metrics-ui.gif)

Bekijk de uitvoerings geschiedenis en de metrische gegevens voor de model prestaties in de studio: 

1. Meld u aan bij [de Studio](https://ml.azure.com/) en navigeer naar uw werk ruimte.
1. Selecteer in het linkerdeel venster van de werk ruimte de optie **uitvoeren**.
1. Selecteer in de lijst met experimenten het abonnement dat u wilt verkennen.
1. Selecteer in de onderste tabel de **uitvoeren**.
1. Selecteer op het tabblad **modellen** de naam van het **algoritme** voor het model dat u wilt verkennen.
1. Selecteer op het tabblad **metrieken** de metrische gegevens en grafieken die u voor het model wilt evalueren. 


<a name="classification"></a> 

## <a name="classification-performance-metrics"></a>Metrische classificatie prestaties

De volgende tabel bevat een overzicht van de prestatie gegevens van het model die AutoML berekent voor elk classificatie model dat voor uw experiment wordt gegenereerd. 

Gegevens|Beschrijving|Berekening|Extra para meters
--|--|--|--
AUC_macro| AUC is het gebied onder de ontvanger van het besturings systeem. Macro is het reken kundige gemiddelde van de AUC voor elke klasse.  | [Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Gem = "macro"|
AUC_micro| AUC is het gebied onder de ontvanger van het besturings systeem. Micro wordt wereld wijd berekend door de werkelijke positieven en onwaare positieven van elke klasse te combi neren.| [Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | gemiddelde = "micro"|
AUC_weighted  | AUC is het gebied onder de ontvanger van het besturings systeem. Gewogen is het reken kundige gemiddelde van de score voor elke klasse, gewogen op basis van het aantal werkelijke instanties in elke klasse.| [Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|gemiddeld = "gewogen"
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

### <a name="binary-vs-multiclass-metrics"></a>Binaire en metrische gegevens met meer klassen

AutoML onderscheidt zich niet tussen binaire en multimetrische metrische gegevens. Dezelfde validatie gegevens worden gerapporteerd, of een gegevensset twee klassen of meer dan twee klassen heeft. Sommige metrische gegevens zijn echter bedoeld voor de classificatie van verschillende klassen. Wanneer dit wordt toegepast op een binaire gegevensset, worden deze metrische gegevens niet als `true` klasse beschouwd, zoals u mogelijk verwacht. Metrische gegevens die duidelijk bedoeld zijn voor multi class, worden met `micro` ,, of geachtervoegseld `macro` `weighted` . Voor beelden zijn `average_precision_score` , `f1_score` ,, en `precision_score` `recall_score` `AUC` .

In plaats van intrekken te berekenen als `tp / (tp + fn)` , wordt het gemiddelde van de multiklasse-inhaal ( `micro` , `macro` of `weighted` ) gemiddeld boven beide klassen van een gegevensset voor binaire classificatie. Dit komt overeen met het berekenen van het terughalen voor de `true` klasse en de `false` klasse afzonderlijk, en vervolgens het gemiddelde van de twee.

## <a name="confusion-matrix"></a>Verwarringsmatrix

Een Verwar ring matrix beschrijft de prestaties van een classificatie model. Elke rij geeft de instanties van de echte of werkelijke klasse in uw gegevensset weer, en elke kolom vertegenwoordigt de exemplaren van de klasse die door het model is voor speld. 

Voor elke Verwar ring-matrix toont automatische ML de frequentie van elk voorspeld label (kolom) vergeleken met het werkelijke label (rij). Hoe donkerder de kleur, hoe hoger het aantal in het betreffende deel van de matrix. 

### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?

Een Verwar ring matrix vergelijkt de werkelijke waarde van de gegevensset met de voorspelde waarden die het model heeft gekregen. Als gevolg hiervan hebben machine learning modellen een grotere nauw keurigheid als het model de meeste waarden in de diagonaal heeft, wat betekent dat het model de juiste waarde voor speld heeft. Als een model klasse onevenwichtig heeft, helpt de Verwar ring matrix bij het detecteren van een verduidelijkend model.

#### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Voor beeld 1: een classificatie model met een slechte nauw keurigheid
![Een classificatie model met een slechte nauw keurigheid](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

#### <a name="example-2-a-classification-model-with-high-accuracy"></a>Voor beeld 2: een classificatie model met hoge nauw keurigheid 
![Een classificatie model met hoge nauw keurigheid](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Voor beeld 3: een classificatie model met hoge nauw keurigheid en hoge afwijking in model voorspellingen
![Een classificatie model met hoge nauw keurigheid en hoge afwijking in model voorspellingen](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>

## <a name="precision-recall-chart"></a>Nauw keurigheid: grafiek intrekken

De curve voor het intrekken van precisie toont de relatie tussen Precision en intrekken van een model. De term Precision vertegenwoordigt de mogelijkheid voor een model om alle exemplaren correct te labelen. Intrekken vertegenwoordigt de mogelijkheid voor een classificatie om alle exemplaren van een bepaald label te vinden.

Met deze grafiek kunt u de curven voor het intrekken van precisie vergelijken voor elk model om te bepalen welk model een acceptabele relatie heeft tussen Precision en intrekken voor uw specifieke bedrijfs probleem. Dit diagram toont de gemiddelde precisie van Macro's: terughalen, gemiddelde precisie van micron-terughalen en de nauwkeurigheids intrekken die zijn gekoppeld aan alle klassen voor een model. 

**Macro:** berekent de metrische gegevens onafhankelijk van elke klasse en neemt vervolgens het gemiddelde over, waarbij alle klassen gelijkmatig worden behandeld. **Micro-Gem** aggregeert echter de bijdragen van alle klassen om het gemiddelde te berekenen. Micro-Gem is de voor keur als er sprake is van een klasse-onevenwichtigheid in de gegevensset.

### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
Afhankelijk van het doel van het bedrijfs probleem, kan de perfecte nauw keurigheid voor het intrekken van de precisie verschillen. 

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Voor beeld 1: een classificatie model met lage precisie en weinig intrekken
![Een classificatie model met lage precisie en weinig intrekken](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Voor beeld 2: een classificatie model met ~ 100% Precision en ~ 100% intrekken 
![Een classificatie model met hoge precisie en intrekken](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

<a name="roc"></a>

## <a name="roc-chart"></a>ROC diagram

Het besturings kenmerk van de ontvanger (of ROC) is een tekening van de juist geclassificeerde labels versus de onjuist geclassificeerde labels voor een bepaald model. De ROC-curve kan minder informatieve zijn bij het trainen van modellen op gegevens sets met een hoge onevenwichtige klasse, aangezien de meerderheids klasse de bijdrage van minderheids klassen kan verdrinken.

U kunt het gebied onder het ROC diagram visualiseren als het aandeel van de juiste geclassificeerde steek proeven. Een ervaren gebruiker van de ROC grafiek kan er meer uitzien dan het gebied onder de curve en een Intuition ophalen voor de echte positieve en onjuiste positieve tarieven als functie van de classificatie drempel of beslissings grens.

### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
Een ROC-curve die de linkerbovenhoek benadert met 100% True positief percentage en 0% false positief percentage is het beste model. Een wille keurig model wordt weer gegeven als een platte lijn van linksonder naar de rechter bovenhoek. Erger dan wille keurig zou dip onder de lijn y = x.

#### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Voor beeld 1: een classificatie model met lage labels en hoge onwaar labels
![Classificatie model met lage labels en hoog/onwaar labels](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

#### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Voor beeld 2: een classificatie model met hoogwaardige labels en laagloze labels

![een classificatie model met hoogwaardige labels en lage labels met onwaar](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)


<a name="lift-curve"></a>

## <a name="lift-chart"></a>Lift-grafiek

Met een lift diagram worden de prestaties van classificatie modellen geëvalueerd. In een lift diagram ziet u hoe vaak een model beter presteert vergeleken met een wille keurig model. Dit geeft u een relatieve prestaties waarbij rekening wordt gehouden met het feit dat de classificatie moeilijker wordt naarmate u het aantal klassen verhoogt. Een wille keurig model voor spelt onjuist een grotere fractie van voor beelden van een gegevensset met tien klassen vergeleken met een gegevensset met twee klassen.

U kunt de lift van het model dat automatisch is gebouwd met Azure Machine Learning vergelijken met de basis lijn (wille keurig model) om de waarde van het betreffende model te bekijken.

### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?

Een beter model voor het uitvoeren van modellen heeft een lift curve die hoger is in de grafiek en verder van de basis lijn. 

#### <a name="example-1-a-classification-model-that-performs-poorly-compared-to-a-random-selection-model"></a>Voor beeld 1: een classificatie model dat slecht presteert ten opzichte van een wille keurig selectie model
![Een classificatie model dat erger is dan een wille keurig selectie model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

#### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Voor beeld 2: een classificatie model dat beter presteert dan een wille keurig selectie model
![Een classificatie model dat beter presteert](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

<a name="gains-curve"></a>

## <a name="cumulative-gains-chart"></a>Grafiek met cumulatieve toename

Een cumulatieve toename diagram evalueert de prestaties van een classificatie model door elk deel van de gegevens. In de grafiek wordt voor elk percentiel van de gegevensset weer gegeven hoeveel meer steek proeven nauw keurig zijn geclassificeerd in vergelijking met een model dat altijd onjuist is. Deze informatie biedt een andere manier om de resultaten in de bijbehorende Lift-grafiek te bekijken.

De cumulatieve toename grafiek helpt u bij het kiezen van de indelings beperking met behulp van een percentage dat overeenkomt met een gewenste toename van het model. U kunt het diagram van de cumulatieve toename vergelijken met de basis lijn (onjuist model) om het percentage van de voor beelden die goed zijn ingedeeld op elk betrouw bare percentiel te bekijken.

#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?

Net als bij een lift diagram, is het hoger uw cumulatieve toename-curve boven de basis lijn, hoe beter uw model presteert. Daarnaast is de gebogen lijn nauw keuriger dan de linkerbovenhoek van de grafiek, hoe groter uw model het beste wordt vergeleken met de basis lijn. 

##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Voor beeld 1: een classificatie model met minimale toename
![een classificatie model met minimale toename](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Voor beeld 2: een classificatie model met aanzienlijke winst
![Een classificatie model met aanzienlijke winst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

<a name="calibration-plot"></a>

## <a name="calibration-chart"></a>Kalibratie diagram

In een kalibratie plot wordt het vertrouwen van een voorspellend model weer gegeven. Dit doet u door de relatie tussen de voorspelde waarschijnlijkheid en de werkelijke kans weer te geven, waarbij ' kans ' staat voor de kans dat een bepaalde instantie tot een bepaald label behoort.

Voor alle classificatie problemen kunt u de kalibratie lijn voor micro-Average, macro-Gem en elke klasse in een bepaald voorspellend model controleren.

**Macro-gemiddelde** berekent de metrische gegevens onafhankelijk van elke klasse en nemen vervolgens het gemiddelde uit, waarbij alle klassen gelijkmatig worden behandeld. **Micro-Gem** aggregeert echter de bijdragen van alle klassen om het gemiddelde te berekenen. 

### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
Een goed gekalibreerd model wordt uitgelijnd met de lijn y = x, waarbij de kans wordt gedicteerd dat steek proeven bij elke klasse horen. Een over-vertrouwen model zal de waarschijnlijkheid voors pellen die bijna gelijk zijn aan nul en één, wat zelden duidelijk is over de klasse van elk voor beeld.

#### <a name="example-1-a-well-calibrated-model"></a>Voor beeld 1: een goed gekalibreerd model
![ meer goed gekalibreerd model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

#### <a name="example-2-an-over-confident-model"></a>Voor beeld 2: een over-vertrouwen model
![Een over-vertrouwen model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)


<a name="regression"></a> 

## <a name="regression-performance-metrics"></a>Gegevens over regressie prestaties

De volgende tabel bevat een overzicht van de prestatie gegevens van het model die AutoML berekent voor elk regressie-of prognose model dat voor uw experiment wordt gegenereerd. 

|Gegevens|Beschrijving|Berekening|Extra para meters
--|--|--|--|
explained_variance|De uitleg afwijking is de verhouding waarmee een wiskundig model account voor de variatie van een bepaalde gegevensset is gedefinieerd. Het is het percentage afname van de oorspronkelijke gegevens tot de variantie van de fouten. Wanneer het gemiddelde van de fouten 0 is, is het gelijk aan het kwadraat van de berekening (Zie r2_score hieronder).|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Geen|
r2_score|R ^ 2 is de determinatie coëfficiënt of het percentage verlaging in kwadratische fouten ten opzichte van een basislijn model dat het gemiddelde uitvoert. |[Berekening](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Geen|
spearman_correlation|' Spearman correlatie ' is een niet-parametrische meting van de monotonicity van de relatie tussen twee gegevens sets. In tegens telling tot de correlatie van Pearson, neemt de ' Spearman-correlatie niet in dat beide gegevens sets normaal gesp roken worden gedistribueerd. Net als bij andere correlatie coëfficiënten is dit een verschil tussen-1 en + 1 en 0 voor geen correlatie. Correlaties van-1 of + 1 impliceren een nauw keurige monotone relatie. Positieve correlaties impliceren dat als x stijgt, dus y. Negatieve correlaties impliceren dat x stijgt, y afneemt.|[Berekening](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Geen|
mean_absolute_error|De gemiddelde absolute fout is de verwachte waarde van de absolute waarde van het verschil tussen het doel en de voor spelling|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Geen|
normalized_mean_absolute_error|De genormaliseerde gemiddelde absolute fout is een absolute fout gedeeld door het bereik van de gegevens|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Delen door bereik van de gegevens|
median_absolute_error|Mediaan absolute fout is de mediaan van alle absolute verschillen tussen het doel en de voor spelling. Dit verlies is robuust voor uitbijters.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Geen|
normalized_median_absolute_error|Genormaliseerde mediaan absolute fout is de gemiddelde absolute fout gedeeld door het bereik van de gegevens|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Delen door bereik van de gegevens|
root_mean_squared_error|Root mean error is de vierkantswortel van het verwachte verschil in kwadraat tussen het doel en de voor spelling|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Geen|
normalized_root_mean_squared_error|Genormaliseerd root-kwadraat fout is het root-kwadraat fout gedeeld door het bereik van de gegevens|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Delen door bereik van de gegevens|
root_mean_squared_log_error|Het wortel gemiddelde van het logaritmische fout is de vierkantswortel van de verwachte kwadratische fout|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Geen|
normalized_root_mean_squared_log_error|Genormaliseerde logaritmische fout in kwadraat is het belangrijkste gemiddelde logboek fout gedeeld door het bereik van de gegevens|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Delen door bereik van de gegevens|

<a name="pvt"></a>

## <a name="predicted-vs-true-chart"></a>Voor speld versus waar grafiek

Voor speld versus waar ziet u de relatie tussen een voorspelde waarde en de bijbehorende waarde voor het correleren van een regressie probleem. 

Na elke uitvoering ziet u een voorspelde versus ware grafiek voor elk regressie model. Ter bescherming van de privacy van gegevens zijn waarden binning samen en de grootte van elke bin wordt weer gegeven als een staaf diagram in het onderste gedeelte van het grafiek gebied. U kunt het voorspellende model vergelijken met het lichtere scha kering van de fout marges, vergeleken met de ideale waarde van waar het model moet zijn.

### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
Deze grafiek kan worden gebruikt om de prestaties van een model te meten als dichter bij de y = x-lijn de voorspelde waarden, de betere prestaties van een voorspellend model.

#### <a name="example-1-a-regression-model-with-low-performance"></a>Voor beeld 1: een regressie model met lage prestaties
![Een regressie model met lage nauw keurigheid in voor spellingen](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

#### <a name="example-2-a-regression-model-with-high-performance"></a>Voor beeld 2: een regressie model met hoge prestaties
![Een regressie model met hoge nauw keurigheid in de voor spellingen](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)

<a name="histo"></a> 

## <a name="histogram-of-residuals-chart"></a>Grafiek met het histogram van verschillen

Automatische MILLILITERs bieden automatisch een grafiek van verschillen om de verdeling van fouten in de voor spellingen van een regressie model weer te geven. Een rest is het verschil tussen de voor spelling en de werkelijke waarde ( `y_pred - y_true` ). 

### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
Als u een fout marge met een lage afwijking wilt weer geven, moet het histogram van de resten worden gevormd als klok curve, gecentreerd rond nul.

#### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Voor beeld 1: een regressie model met afwijking van fouten
![SA-regressie model met afwijking van fouten](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

#### <a name="example-2-a-regression-model-with-a-more-even-distribution-of-errors"></a>Voor beeld 2: een regressie model met een meer gelijkmatige verdeling van fouten
![Een regressie model met een meer gelijkmatige verdeling van fouten](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

<a name="explain-model"></a>

## <a name="model-interpretability-and-feature-importance"></a>De interpretatie van modellen en de urgentie van het onderdeel
Automated ML biedt een machine learning interpretable dash board voor uw uitvoeringen.

Zie voor meer informatie over het inschakelen van functies voor het maken van voorzieningen [interpretive: model uitleg in automatische machine learning](how-to-machine-learning-interpretability-automl.md).

> [!NOTE]
> Het ForecastTCN-model wordt momenteel niet ondersteund door de uitleg-client. Dit model retourneert geen uitleg-dash board als het wordt geretourneerd als het beste model en biedt geen ondersteuning voor uitleg over de uitvoering van een on-demand.

## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over [automatische ml](concept-automated-ml.md) in azure machine learning.
+ Probeer de voorbeeld notitieblokken voor [automatische machine learning-modellen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) .