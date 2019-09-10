---
title: Meer informatie over geautomatiseerde ML-resultaten
titleSuffix: Azure Machine Learning service
description: Meer informatie over het weer geven en begrijpen van grafieken en metrische gegevens voor elk van uw geautomatiseerde machine learning uitvoeringen.
services: machine-learning
author: nilesha
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: 60ecd71419bbf65cceab257cb97a96929d24ec08
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860557"
---
# <a name="understand-automated-machine-learning-results"></a>Automatische machine learning resultaten begrijpen

In dit artikel leert u hoe u de diagrammen en metrische gegevens voor elk van uw geautomatiseerde machine learning-uitvoeringen kunt weer geven en begrijpen. 

Meer informatie over:
+ [Metrische gegevens, grafieken en curven voor classificatie modellen](#classification)
+ [Metrische gegevens, grafieken en grafieken voor regressie modellen](#regression)
+ [De interpretatie van modellen en de urgentie van het onderdeel](#explain-model)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer nog vandaag de [gratis of betaalde versie van de Azure Machine Learning Service](https://aka.ms/AMLFree).

* Maak een geautomatiseerd machine learning experiment, hetzij met de SDK, in de Azure Portal of op de landings pagina van uw werk ruimte (preview).

    * De SDK gebruiken om een [classificatie model](how-to-auto-train-remote.md) of [regressie model](tutorial-auto-train-models.md) te bouwen
    * Gebruik de [Azure portal of de landings pagina van uw werk ruimte (preview)](how-to-create-portal-experiments.md) om een classificatie of regressie model te maken door de juiste gegevens te uploaden.

## <a name="view-the-run"></a>De uitvoering weer geven

Nadat u een geautomatiseerd machine learning experiment hebt uitgevoerd, kunt u een overzicht van de uitvoeringen vinden in de werk ruimte van de machine learning-service. 

1. Ga naar uw werkruimte.

1. Selecteer **experimenten**in het linkerdeel venster van de werk ruimte.

   ![Schermopname van het experiment menu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Selecteer in de lijst met experimenten het abonnement dat u wilt verkennen.

   [![Proef lijst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Selecteer in de onderste tabel het **uitvoerings nummer**.

   Experimenteel uitvoeren) [ ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)

1. Selecteer in de tabel iteraties het **herhalings nummer** voor het model dat u verder wilt verkennen.

   [![Model experimenteren](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

U ziet ook dezelfde resultaten tijdens het uitvoeren wanneer u de `RunDetails` [Jupyter-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)gebruikt.

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

|Gegevens|Description|Berekening|Extra Parameters
--|--|--|--|
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
log_loss|Dit is de verlies-functie die wordt gebruikt voor logistieke regressie (wordt) en -extensies van het zoals neurale netwerken, gedefinieerd als de negatieve log kans van de waarde true labels een probabilistic classificatie voorspellingen gegeven. Voor één steek proef met True label YT in {0,1} en geschatte waarschijnlijkheid YP die YT = 1 is, is het logboek verlies van log P&#124;(YT YP) =-(YT log (YP) + (1-YT) logboek (1-YP)).|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Geen|
norm_macro_recall|Genormaliseerde Macro intrekken is Macro intrekken genormaliseerd zodat willekeurige prestaties een score van 0 krijgt en perfecte prestaties een score van 1 krijgt. Dit wordt bereikt door norm_macro_recall: = (recall_score_macro-R)/(1-R), waarbij R de verwachte waarde is van recall_score_macro voor wille keurige voor spellingen (R = 0,5 voor binaire classificatie en R = (1/C) voor problemen met de classificatie van C-klasse).|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Gem = "macro" |
precision_score_macro|De precisie is het percentage van de elementen die worden aangeduid als een bepaalde klasse die daadwerkelijk in die klasse. Macro is het reken kundige gemiddelde van de precisie voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|gemiddelde = "macro"|
precision_score_micro|De precisie is het percentage van de elementen die worden aangeduid als een bepaalde klasse die daadwerkelijk in die klasse. Micro wordt wereld wijd berekend door het totale aantal positieve en foutieve positieven te tellen.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|gemiddelde = "micro"|
precision_score_weighted|De precisie is het percentage van de elementen die worden aangeduid als een bepaalde klasse die daadwerkelijk in die klasse. Gewogen is het reken kundige gemiddelde van de precisie voor elke klasse, gewogen op basis van het aantal werkelijke instanties in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|gemiddelde = "gewogen"|
recall_score_macro|Intrekken is het percentage van de elementen in een bepaalde klasse daadwerkelijk die goed zijn gelabeld. Macro is het reken kundige gemiddelde van intrekken voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|gemiddelde = "macro"|
recall_score_micro|Intrekken is het percentage van de elementen in een bepaalde klasse daadwerkelijk die goed zijn gelabeld. Micro wordt wereld wijd berekend door het totale aantal positieve, onwaare negatieven en foutieve positieven te tellen|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|gemiddelde = "micro"|
recall_score_weighted|Intrekken is het percentage van de elementen in een bepaalde klasse daadwerkelijk die goed zijn gelabeld. Gewogen is het reken kundige gemiddelde van intrekken voor elke klasse, gewogen op basis van het aantal werkelijke instanties in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|gemiddelde = "gewogen"|
weighted_accuracy|Gewogen nauw keurigheid is nauw keurig wanneer het gewicht dat aan elk voor beeld is gegeven, gelijk is aan het aandeel van de werkelijke instanties in dat voor beeld van de klasse waar.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight is gelijk aan het aandeel van die klasse voor elk element in de doel-vector|

### <a name="confusion-matrix"></a>Verwarringsmatrix

Een verwarringsmatrix wordt gebruikt om te beschrijven van de prestaties van een model voor classificatie. Elke rij geeft de exemplaren van de klasse ' True ', en elke kolom vertegenwoordigt de instanties van de voorspelde klasse. De verwarringsmatrix ziet u de labels correct ingedeeld en de onjuist ingedeeld labels voor een bepaald model.

Voor problemen met de classificatie biedt Azure Machine Learning automatisch een verwarringsmatrix voor elk model dat is gebouwd. Voor elke Verwar ring wordt de frequentie weer gegeven van elk voorspeld label en elk label dat op het punt staat. Hoe donkerder de kleur, hoe hoger het aantal in het betreffende deel van de matrix. In het ideale geval zijn de donkerste kleuren langs de diagonaal van de matrix. 

Voorbeeld 1: Een classificatie model met een slechte ![nauw keurigheid van een classificatie model met een slechte nauw keurigheid](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

Voorbeeld 2: Een classificatie model met hoge nauw keurigheid ( ![ideaal) een classificatie model met hoge nauw keurigheid](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>Precisie-/ oproepdiagram grafiek

U kunt de precisie-/ oproepdiagram curven voor elk model om te bepalen welk model u al een acceptabele relatie tussen precisie en intrekken voor uw specifieke zakelijke probleem vergelijken met deze grafiek. In deze grafiek worden precisie-/ Oproepdiagram Macro gemiddeld, gemiddelde Micro-precisie-/ Oproepdiagram en de precisie-/ oproepdiagram die zijn gekoppeld aan alle klassen voor een model.

De term die precisie vertegenwoordigt die mogelijkheid voor een classificatie voor alle instanties juist label. Intrekken vertegenwoordigt de mogelijkheid voor een classificatie om alle exemplaren van een bepaald label te vinden. De precisie-/ oproepdiagram curve ziet u de relatie tussen deze twee concepten. Het model zou in het ideale geval zijn 100% nauwkeurigheid en 100% nauwkeurigheid.

Voorbeeld 1: Een classificatie model met lage precisie en laag ![een classificatie model met lage precisie en laag intrekken](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

Voorbeeld 2: Een classificatie model met ~ 100% Precision en ~ 100% intrekken (ideaal ![) een classificatie model met hoge precisie en intrekken](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ROC

Ontvanger operationele kenmerken (of ROC) is een diagram van de labels correct ingedeeld versus de onjuist ingedeeld labels voor een bepaald model. De ROC-curve zijn minder informatieve als training modellen op gegevenssets met hoge vertekening, zoals deze niet in de ONWAAR positief labels weergegeven wordt.

Voorbeeld 1: Een classificatie model met lage labels en hoge onwaare ![labels met lage labels en hoog/onwaar labels](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

Voorbeeld 2: Een classificatie model met hoge, echte labels en laagloze ![labels een classificatie model met hoogwaardige labels en lage labels op ONWAAR](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>Lift-curve

U kunt de lift van het model automatisch gemaakt met Azure Machine Learning aan de basislijn om te bekijken van de toename van de waarde van dat specifieke model vergelijken.

Lift-grafieken worden gebruikt voor het evalueren van de prestaties van een model voor classificatie. Hier ziet u hoeveel beter kunt u verwachten doen met een model in vergelijking met zonder een model. 

Voorbeeld 1: Het model voert erger uit dan een wille keurig selectie model ![een classificatie model dat erger is dan een wille keurig selectie model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

Voorbeeld 2: Model voert beter uit dan een wille ![keurig selectie model, een classificatie model dat beter presteert](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>Winsten curve

Een diagram kolomopslag evalueert de prestaties van een model classificatie op basis van elk deel van de gegevens. Hier ziet voor elke percentiel van de gegevensset, hoeveel u kunt beter verwachten om uit te voeren vergeleken met een willekeurige selectie-model.

Met de cumulatieve winsten grafiek kunt u de classificatie afsluitdatum met behulp van een percentage dat overeenkomt met een gewenste winst uit het model kiezen. Deze informatie geeft een andere manier om de resultaten in de bijbehorende lift-grafiek kijken.

Voorbeeld 1: Een classificatie model met minimale ![toename van een classificatie model met minimale toename](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

Voorbeeld 2: Een classificatie model met aanzienlijke toename ![van een classificatie model met aanzienlijke winst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>Kalibreren tekengebied

Voor alle problemen met de classificatie, kunt u de regel kalibreren voor micro-gemiddelde macro-gemiddelde en elke klasse in een bepaalde Voorspellend model bekijken. 

Een diagram kalibreren wordt gebruikt om het vertrouwen van een Voorspellend model weer te geven. Dit gebeurt door de relatie tussen de voorspelde kans en de werkelijke kans op, waarbij "kans" vertegenwoordigt de kans dat een bepaalde instantie deel uitmaakt van een label. Een goed geijkte model wordt uitgelijnd met de y = x regel, waar deze redelijk zeker van zijn in de voorspellingen is. Een model te veel confident worden uitgelijnd met de y = 0 regel, waar de voorspelde kans is aanwezig, maar er is geen daadwerkelijke kans.

Voorbeeld 1: Een meer goed gekalibreerd model ![ met meer goed gekalibreerd model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

Voorbeeld 2: Een over-verzekerd model ![van een model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Regressie resultaten

Thee de volgende metrische gegevens en grafieken zijn beschikbaar voor elk regressie model dat u bouwt met behulp van de geautomatiseerde machine learning mogelijkheden van Azure Machine Learning

+ [Metrische gegevens](#reg-metrics)
+ [Voorspelde vs. De waarde True](#pvt)
+ [Histogram van dit](#histo)


### <a name="reg-metrics"></a>Metrische gegevens over regressie

De volgende metrische gegevens worden opgeslagen in elke uitvoerings herhaling voor een regressie-of prognose taak.

|Gegevens|Description|Berekening|Extra Parameters
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

### <a name="pvt"></a>Voorspeld versus Waar

Voorspelde vs. Waar wordt de relatie tussen een voorspelde waarde en de waarde voor correlerende waar een probleem met regressie. Deze grafiek kan worden gebruikt voor het meten van prestaties van een model als de dichter bij de y = x regel de voorspelde waarden zijn, hoe beter de nauwkeurigheid van een Voorspellend model.

Na elke uitvoering ziet u een voorspelde versus waar graph voor elke regressiemodel. Als u wilt beveiligen privacy van gegevens, waarden samen worden binned en de grootte van elke bin wordt weergegeven als een staafdiagram in het onderste gedeelte van het grafiekgebied. U kunt het voorspellende model vergelijken met de lichtere schaduw-gebied met fout marges, op basis van de optimale waarde van waar het model moet worden.

Voorbeeld 1: Een regressie model met lage nauw keurigheid bij ![het voors pellen van een regressie model met lage nauw keurigheid in voor spellingen](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

Voorbeeld 2: Een regressie model met hoge nauw keurigheid in de voor spellingen [ ![van een regressie model met hoge nauw keurigheid in de voor spellingen](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Histogram van verschillen

Een resterende vertegenwoordigt een waargenomen y – de voorspelde y. Om weer te geven een foutmarge met lage afwijking, moet het histogram van dit als een rinkelende bel curve, gecentreerd rond 0 worden vormgegeven. 

Voorbeeld 1: Een regressie model met bias in het fouten ![sa-regressie model met afwijking van fouten](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

Voorbeeld 2: Een regressie model met een meer gelijkmatige verdeling van ![fouten in een regressie model met meer gelijkmatige verdeling van fouten](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>De interpretatie van modellen en de urgentie van het onderdeel

Met de functie urgentie kunt u zien hoe waardevol elke functie was in de constructie van een model. Deze berekening is standaard uitgeschakeld, omdat deze de uitvoerings tijd aanzienlijk kan verg Roten.   U kunt de beschrijving van een model voor alle modellen inschakelen of alleen het beste model maken.

U kunt de functie belang score voor het model algehele, evenals per klasse op een Voorspellend model bekijken. U kunt per functie zien hoe het belang worden vergeleken op basis van elke klasse en de algehele.

![Functie uitleg mogelijkheid](./media/how-to-understand-automated-ml/feature-importance.gif)

Zie voor meer informatie over het inschakelen van functies voor het maken van interpretaties de functie [automatische ml experimenten configureren in python](how-to-configure-auto-train.md#explain-the-model-interpretability).  Zie voor een voor beeld waarin het beste model wordt uitgelegd de [Aanbevolen model uitleg](how-to-auto-train-remote.md#explain).

## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over [automatische ml](concept-automated-ml.md) in azure machine learning.
+ Probeer het voor beeld van een beschrijving van het [geautomatiseerde machine learning model](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
