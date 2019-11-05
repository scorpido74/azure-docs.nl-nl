---
title: Meer informatie over geautomatiseerde ML-resultaten
titleSuffix: Azure Machine Learning
description: Meer informatie over het weer geven en begrijpen van grafieken en metrische gegevens voor elk van uw geautomatiseerde machine learning uitvoeringen.
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 93695e0bbcb81a570519a6f74cfdeab4ef85f076
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489401"
---
# <a name="understand-automated-machine-learning-results"></a>Automatische machine learning resultaten begrijpen
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

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

   ![Scherm opname van het menu experiment](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Selecteer in de lijst met experimenten het abonnement dat u wilt verkennen.

   [lijst met ![experimenten](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Selecteer in de onderste tabel het **uitvoerings nummer**.

   [uitvoering van![-experiment](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. Selecteer in de tabel iteraties het **herhalings nummer** voor het model dat u verder wilt verkennen.

   [![-experiment model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

U ziet dezelfde resultaten tijdens het uitvoeren wanneer u de [Jupyter-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)`RunDetails`gebruikt.

## <a name="classification"></a>Classificatie resultaten

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

|Gegevens|Beschrijving|Berekening|Extra para meters
--|--|--|--|
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
log_loss|Dit is de functie verlies die wordt gebruikt in (MultiNomial) logistiek regressie en uitbrei dingen van IT, zoals Neural-netwerken, gedefinieerd als de negatieve kans op Logboeken van de werkelijke labels op basis van de voor spellingen van een Probabilistic-classificatie. Voor één steek proef met True label YT in {0,1} en geschatte waarschijnlijke kans YP dat YT = 1, het logboek verlies is: log P&#124;(YT YP) =-(YT log (YP) + (1-YT) logboek (1-YP)).|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Geen|
norm_macro_recall|Genormaliseerde macro intrekken is macro intrekken genormaliseerd, zodat wille keurige prestaties een Score van 0 hebben en de perfecte prestaties een Score van 1 hebben. Dit wordt bereikt door norm_macro_recall: = (recall_score_macro-R)/(1-R), waarbij R de verwachte waarde is van recall_score_macro voor wille keurige voor spellingen (R = 0,5 voor binaire classificatie en R = (1/C) voor problemen met de classificatie van C-klasse).|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Gem = "macro" |
precision_score_macro|Precisie is het percentage van elementen als een bepaalde klasse die zich in die klasse bevindt. Macro is het reken kundige gemiddelde van de precisie voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Gem = "macro"|
precision_score_micro|Precisie is het percentage van elementen als een bepaalde klasse die zich in die klasse bevindt. Micro wordt wereld wijd berekend door het totale aantal positieve en foutieve positieven te tellen.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|gemiddelde = "micro"|
precision_score_weighted|Precisie is het percentage van elementen als een bepaalde klasse die zich in die klasse bevindt. Gewogen is het reken kundige gemiddelde van de precisie voor elke klasse, gewogen op basis van het aantal werkelijke instanties in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|gemiddeld = "gewogen"|
recall_score_macro|Intrekken is het percentage elementen dat daad werkelijk in een bepaalde klasse wordt genoemd. Macro is het reken kundige gemiddelde van intrekken voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Gem = "macro"|
recall_score_micro|Intrekken is het percentage elementen dat daad werkelijk in een bepaalde klasse wordt genoemd. Micro wordt wereld wijd berekend door het totale aantal positieve, onwaare negatieven en foutieve positieven te tellen|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|gemiddelde = "micro"|
recall_score_weighted|Intrekken is het percentage elementen dat daad werkelijk in een bepaalde klasse wordt genoemd. Gewogen is het reken kundige gemiddelde van intrekken voor elke klasse, gewogen op basis van het aantal werkelijke instanties in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|gemiddeld = "gewogen"|
weighted_accuracy|Gewogen nauw keurigheid is nauw keurig wanneer het gewicht dat aan elk voor beeld is gegeven, gelijk is aan het aandeel van de werkelijke instanties in dat voor beeld van de klasse waar.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight is een vector gelijk aan het aandeel van die klasse voor elk element in het doel|

### <a name="confusion-matrix"></a>Verwar ring matrix

Een Verwar ring matrix wordt gebruikt om de prestaties van een classificatie model te beschrijven. Elke rij toont de instanties van de klasse True en elke kolom vertegenwoordigt de instanties van de voorspelde klasse. De Verwar ring matrix toont de juist geclassificeerde labels en de onjuist geclassificeerde labels voor een bepaald model.

Voor classificatie problemen biedt Azure Machine Learning automatisch een Verwar ring matrix voor elk model dat is gebouwd. Voor elke Verwar ring wordt de frequentie weer gegeven van elk voorspeld label en elk label dat op het punt staat. Hoe donkerder de kleur, hoe hoger het aantal in het betreffende deel van de matrix. In het ideale geval zijn de donkerste kleuren langs de diagonaal van de matrix. 

Voor beeld 1: een classificatie model met een slechte nauw keurigheid ![een classificatie model met een slechte nauw keurigheid](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

Voor beeld 2: een classificatie model met hoge nauw keurigheid (ideaal) ![een classificatie model met hoge nauw keurigheid](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>Nauw keurigheid: grafiek intrekken

Met deze grafiek kunt u de curven voor het intrekken van precisie vergelijken voor elk model om te bepalen welk model een acceptabele relatie heeft tussen Precision en intrekken voor uw specifieke bedrijfs probleem. Dit diagram toont de gemiddelde precisie van Macro's: terughalen, gemiddelde precisie van micron-terughalen en de nauwkeurigheids intrekken die zijn gekoppeld aan alle klassen voor een model.

De term precisie duidt op de mogelijkheid voor een classificatie om alle exemplaren correct te labelen. Intrekken vertegenwoordigt de mogelijkheid voor een classificatie om alle exemplaren van een bepaald label te vinden. In de curve voor het intrekken van precisie wordt de relatie tussen deze twee concepten weer gegeven. In het ideale geval heeft het model 100% nauw keurigheid en een nauw keurigheid van 100%.

Voor beeld 1: een classificatie model met lage precisie en lage intrekken ![een classificatie model met lage precisie en lage intrekken](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

Voor beeld 2: een classificatie model met ~ 100% Precision en ~ 100% intrekken (ideaal) ![een classificatie model met hoge precisie en intrekken](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ROC

Het besturings kenmerk van de ontvanger (of ROC) is een tekening van de juist geclassificeerde labels versus de onjuist geclassificeerde labels voor een bepaald model. De ROC-curve kan minder informatieve zijn bij het trainen van modellen op gegevens sets met een hoge afwijking, omdat er geen positieve labels worden weer gegeven.

Voor beeld 1: een classificatie model met lage labels en hoge onwaare labels ![classificatie model met lage labels en hoge onwaare labels](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

Voor beeld 2: een classificatie model met hoogwaardige labels en lage onwaarde labels ![een classificatie model met hoge labels en lage onwaare labels](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>Bocht

U kunt de lift van het model dat automatisch is gebouwd met Azure Machine Learning, vergelijken met de basis lijn om de waarde van het betreffende model te bekijken.

Lift grafieken worden gebruikt om de prestaties van een classificatie model te evalueren. Er wordt weer gegeven hoe beter u kunt verwachten met een model vergeleken met zonder een model. 

Voor beeld 1: model wordt verergert dan een wille keurig selectie model ![een classificatie model dat erger is dan een wille keurig selectie model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

Voor beeld 2: model presteert beter dan een wille keurig selectie model ![een classificatie model dat betere](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>Toename curve

Een winst diagram evalueert de prestaties van een classificatie model door elk deel van de gegevens. Er wordt weer gegeven voor elk percentiel van de gegevensset, hoe beter u kunt verwachten dat ze worden uitgevoerd ten opzichte van een wille keurig selectie model.

Gebruik het diagram cumulatieve toename om u te helpen bij het kiezen van de indelings beperking met behulp van een percentage dat overeenkomt met een gewenste toename van het model. Deze informatie biedt een andere manier om de resultaten in de bijbehorende Lift-grafiek te bekijken.

Voor beeld 1: een classificatie model met minimale toename ![een classificatie model met minimale winst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

Voor beeld 2: een classificatie model met aanzienlijk winst ![een classificatie model met aanzienlijke winst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>Kalibratie tekening

Voor alle classificatie problemen kunt u de kalibratie lijn voor micro-Average, macro-Gem en elke klasse in een bepaald voorspellend model controleren. 

Er wordt een kalibratie tekening gebruikt om het vertrouwen van een voorspellend model weer te geven. Dit doet u door de relatie tussen de voorspelde waarschijnlijkheid en de werkelijke kans weer te geven, waarbij ' kans ' staat voor de kans dat een bepaalde instantie tot een bepaald label behoort. Een goed gekalibreerd model wordt uitgelijnd met de lijn y = x, waar het redelijk is in de voor spellingen. Een model dat groter is dan wordt uitgelijnd met de lijn y = 0, waar de voorspelde kans aanwezig is, maar er is geen werkelijke kans.

Voor beeld 1: een meer goed gekalibreerd model ![ meer goed gekalibreerd model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

Voor beeld 2: een te vertrouwen model ![een model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Regressie resultaten

Thee de volgende metrische gegevens en grafieken zijn beschikbaar voor elk regressie model dat u bouwt met behulp van de geautomatiseerde machine learning mogelijkheden van Azure Machine Learning

+ [Metrische gegevens](#reg-metrics)
+ [Voorspeld versus waar](#pvt)
+ [Histogram van verschillen](#histo)


### <a name="reg-metrics"></a>Metrische gegevens over regressie

De volgende metrische gegevens worden opgeslagen in elke uitvoerings herhaling voor een regressie-of prognose taak.

|Gegevens|Beschrijving|Berekening|Extra para meters
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

### <a name="pvt"></a>Voorspeld versus waar

Voor speld versus waar ziet u de relatie tussen een voorspelde waarde en de bijbehorende waarde voor het correleren van een regressie probleem. Deze grafiek kan worden gebruikt om de prestaties van een model te meten als dichter bij de y = x-lijn de voorspelde waarden zijn, hoe beter de nauw keurigheid van een voorspellend model is.

Na elke uitvoering ziet u een voorspelde versus ware grafiek voor elk regressie model. Ter bescherming van de privacy van gegevens zijn waarden binning samen en de grootte van elke bin wordt weer gegeven als een staaf diagram in het onderste gedeelte van het grafiek gebied. U kunt het voorspellende model vergelijken met het lichtere scha kering van de fout marges, vergeleken met de ideale waarde van waar het model moet zijn.

Voor beeld 1: een regressie model met lage nauw keurigheid in voor spellingen ![een regressie model met lage nauw keurigheid in voor spellingen](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

Voor beeld 2: een regressie model met hoge nauw keurigheid in de voor spellingen [![een regressie model met hoge nauw keurigheid in de voor spellingen](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Histogram van verschillen

Een residu vertegenwoordigt een waargenomen y: de voorspelde y. Als u een fout marge met een lage afwijking wilt weer geven, moet het histogram van de resten worden gevormd als klok curve, gecentreerd rond 0. 

Voor beeld 1: een regressie model met afwijking van fouten ![SA-regressie model met afwijking van de fouten](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

Voor beeld 2: een regressie model met meer gelijkmatige verdeling van fouten ![een regressie model met een meer gelijkmatige verdeling van fouten](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>De interpretatie van modellen en de urgentie van het onderdeel

Met de functie urgentie kunt u zien hoe waardevol elke functie was in de constructie van een model. Deze berekening is standaard uitgeschakeld, omdat deze de uitvoerings tijd aanzienlijk kan verg Roten.   U kunt de beschrijving van een model voor alle modellen inschakelen of alleen het beste model maken.

U kunt de prioriteits Score van het onderdeel voor het algemene model en per klasse in een voorspellend model controleren. U kunt per onderdeel zien hoe de urgentie overeenkomt met elke klasse en over het algemeen.

![Functie uitleg](./media/how-to-understand-automated-ml/feature-importance.gif)

Zie voor meer informatie over het inschakelen van functies voor het maken van interpretaties de [procedures](how-to-machine-learning-interpretability-automl.md) voor het inschakelen van de functie voor het maken van interpretiteit in geautomatiseerde ml experimenten.

## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over [automatische ml](concept-automated-ml.md) in azure machine learning.
+ Probeer het voor beeld van een beschrijving van het [geautomatiseerde machine learning model](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
