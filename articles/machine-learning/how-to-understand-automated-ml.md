---
title: Inzicht in geautomatiseerde ML-resultaten
titleSuffix: Azure Machine Learning
description: Meer informatie over het weergeven en begrijpen van grafieken en statistieken voor elk van uw geautomatiseerde machine learning-uitvoeringen.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: c5f12da3606361b504d4581916d9645fa3cd24f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283457"
---
# <a name="understand-automated-machine-learning-results"></a>Geautomatiseerde machine learning-resultaten begrijpen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u de grafieken en statistieken voor elk van uw geautomatiseerde machine learning-uitvoeringen bekijken en begrijpen. 

Meer informatie over:
+ [Statistieken, grafieken en curven voor classificatiemodellen](#classification)
+ [Statistieken, grafieken en grafieken voor regressiemodellen](#regression)
+ [Model interpreteerbaarheid en functiebelang](#explain-model)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)

* Maak een experiment voor uw geautomatiseerde machine learning-run, met de SDK of in Azure Machine Learning-studio.

    * De SDK gebruiken om een [classificatiemodel](how-to-auto-train-remote.md) of [regressiemodel](tutorial-auto-train-models.md) te maken
    * Gebruik [Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md) om een classificatie- of regressiemodel te maken door de juiste gegevens te uploaden.

## <a name="view-the-run"></a>Bekijk de run

Na het uitvoeren van een geautomatiseerd machine learning-experiment, is een geschiedenis van de runs te vinden in uw machine learning-werkruimte. 

1. Ga naar uw werkruimte.

1. Selecteer **Experimenten**in het linkerdeelvenster van de werkruimte .

   ![Schermafbeelding van het experimentmenu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Selecteer in de lijst met experimenten degene die u wilt verkennen.

   [![Experimentlijst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Selecteer in de onderste tabel de **run**.

   Experiment run ) [ ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)

1. Selecteer in de modellen de **naam Algoritme** voor het model dat u verder wilt verkennen.

   [![Experimentmodel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

U ziet ook dezelfde resultaten tijdens een `RunDetails`run wanneer u de [Jupyter widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification-results"></a><a name="classification"></a>Classificatieresultaten

De volgende statistieken en grafieken zijn beschikbaar voor elk classificatiemodel dat u maakt met behulp van de geautomatiseerde machine learning-mogelijkheden van Azure Machine Learning

+ [Statistieken](#classification-metrics)
+ [Verwarringsmatrix](#confusion-matrix)
+ [Precisie-recall-diagram](#precision-recall-chart)
+ [Bedieningseigenschappen van de ontvanger (of ROC)](#roc)
+ [Liftcurve](#lift-curve)
+ [Winstcurve](#gains-curve)
+ [Kalibratieplot](#calibration-plot)

### <a name="classification-metrics"></a>Classificatiestatistieken

De volgende statistieken worden opgeslagen in elke run iteratie voor een classificatietaak.

Gegevens|Beschrijving|Berekening|Extra Parameters
--|--|--|--
AUC_Macro| AUC is het gebied onder de ontvanger operating characteristic curve. Macro is het rekenkundig gemiddelde van de AUC voor elke klasse.  | [Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | gemiddeld="macro"|
AUC_Micro| AUC is het gebied onder de ontvanger operating characteristic curve. Micro wordt wereldwijd berekend door de echte positieven en false positives van elke klasse te combineren.| [Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | gemiddeld="micro"|
AUC_Weighted  | AUC is het gebied onder de ontvanger operating characteristic curve. Gewogen is het rekenkundig gemiddelde van de score voor elke klasse, gewogen door het aantal werkelijke exemplaren in elke klasse.| [Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|gemiddeld="gewogen"
accuracy|Nauwkeurigheid is het percentage voorspelde labels dat precies overeenkomt met de echte labels. |[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Geen|
average_precision_score_macro|Gemiddelde precisie vat een precisie-recall curve als het gewogen gemiddelde van de precisie bereikt op elke drempel, met de toename van de recall van de vorige drempel gebruikt als het gewicht. Macro is het rekenkundig gemiddelde van de gemiddelde precisiescore van elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|gemiddeld="macro"|
average_precision_score_micro|Gemiddelde precisie vat een precisie-recall curve als het gewogen gemiddelde van de precisie bereikt op elke drempel, met de toename van de recall van de vorige drempel gebruikt als het gewicht. Micro wordt wereldwijd berekend door het combineren van de echte positieven en false positives bij elke cutoff.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|gemiddeld="micro"|
average_precision_score_weighted|Gemiddelde precisie vat een precisie-recall curve als het gewogen gemiddelde van de precisie bereikt op elke drempel, met de toename van de recall van de vorige drempel gebruikt als het gewicht. Gewogen is het rekenkundig gemiddelde van de gemiddelde precisiescore voor elke klasse, gewogen door het aantal werkelijke exemplaren in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|gemiddeld="gewogen"|
balanced_accuracy|Evenwichtige nauwkeurigheid is het rekenkundig gemiddelde van terugroepen voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|gemiddeld="macro"|
f1_score_macro|F1 score is het harmonische gemiddelde van precisie en terugroepen. Macro is het rekenkundig gemiddelde van de F1-score voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|gemiddeld="macro"|
f1_score_micro|F1 score is het harmonische gemiddelde van precisie en terugroepen. Micro wordt wereldwijd berekend door het tellen van de totale true positives, false negatives en false positives.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|gemiddeld="micro"|
f1_score_weighted|F1 score is het harmonische gemiddelde van precisie en terugroepen. Gewogen gemiddelde per klasse frequentie van f1 score voor elke klasse|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|gemiddeld="gewogen"|
log_loss|Dit is de verliesfunctie die wordt gebruikt in (multinomial) logistieke regressie en uitbreidingen ervan, zoals neurale netwerken, gedefinieerd als de negatieve log-waarschijnlijkheid van de ware labels gegeven voorspellingen van een probabilistische classificatie. Voor een enkel monster met {0,1} true label yt in en geschatte waarschijnlijkheid yp dat yt = 1, het log verlies is -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp)).|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Geen|
norm_macro_recall|Genormaliseerde Macro Recall is Macro Recall genormaliseerd, zodat willekeurige prestaties heeft een score van 0 en perfecte prestaties heeft een score van 1. Dit wordt bereikt door norm_macro_recall := (recall_score_macro - R)/(1 - R), waarbij R de verwachte waarde is van recall_score_macro voor willekeurige voorspellingen (d.w.z. R=0,5 voor binaire classificatie en R=(1/C) voor C-klasse classificatieproblemen).|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|gemiddeld = "macro" |
precision_score_macro|Precisie is het percentage positief voorspelde elementen die correct zijn gelabeld. Macro is het rekenkundig gemiddelde van precisie voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|gemiddeld="macro"|
precision_score_micro|Precisie is het percentage positief voorspelde elementen die correct zijn gelabeld. Micro wordt wereldwijd berekend door het tellen van de totale true positives en false positives.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|gemiddeld="micro"|
precision_score_weighted|Precisie is het percentage positief voorspelde elementen die correct zijn gelabeld. Gewogen is het rekenkundig gemiddelde van precisie voor elke klasse, gewogen op aantal echte instanties in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|gemiddeld="gewogen"|
recall_score_macro|Recall is het percentage correct gelabelde elementen van een bepaalde klasse. Macro is het rekenkundig gemiddelde van terugroepen voor elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|gemiddeld="macro"|
recall_score_micro|Recall is het percentage correct gelabelde elementen van een bepaalde klasse. Micro wordt wereldwijd berekend door het tellen van de totale true positives, false negatives en false positives|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|gemiddeld="micro"|
recall_score_weighted|Recall is het percentage correct gelabelde elementen van een bepaalde klasse. Gewogen is het rekenkundig gemiddelde van terugroepen voor elke klasse, gewogen op aantal echte instanties in elke klasse.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|gemiddeld="gewogen"|
weighted_accuracy|Gewogen nauwkeurigheid is nauwkeurigheid waarbij het gewicht dat aan elk voorbeeld wordt gegeven gelijk is aan het aandeel true-instanties in de werkelijke klasse van dat voorbeeld.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight is een vector die gelijk is aan het aandeel van die klasse voor elk element in het doel|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Verwarringsmatrix

#### <a name="what-is-a-confusion-matrix"></a>Wat is een verwarringsmatrix?
Een verwarringsmatrix wordt gebruikt om de prestaties van een classificatiemodel te beschrijven. Elke rij geeft de exemplaren van de werkelijke of werkelijke klasse in uw gegevensset weer en elke kolom vertegenwoordigt de exemplaren van de klasse die door het model is voorspeld. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Wat doet geautomatiseerde ML met de verwarringsmatrix?
Voor classificatieproblemen biedt Azure Machine Learning automatisch een verwarringsmatrix voor elk model dat wordt gebouwd. Voor elke verwarringsmatrix toont geautomatiseerde ML de frequentie van elk voorspeld label (kolom) ten opzichte van het echte label (rij). Hoe donkerder de kleur, hoe hoger het aantal in dat specifieke deel van de matrix. 

#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
We vergelijken de werkelijke waarde van de gegevensset met de voorspelde waarden die het model gaf. Hierdoor hebben machine learning-modellen een hogere nauwkeurigheid als het model de meeste waarden langs de diagonaal heeft, wat betekent dat het model de juiste waarde voorspelde. Als een model klasse onbalans heeft, zal de verwarringsmatrix helpen om een bevooroordeeld model te detecteren.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Voorbeeld 1: Een classificatiemodel met een slechte nauwkeurigheid
![Een classificatiemodel met een slechte nauwkeurigheid](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Voorbeeld 2: Een classificatiemodel met hoge nauwkeurigheid 
![Een classificatiemodel met hoge nauwkeurigheid](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Voorbeeld 3: Een classificatiemodel met hoge nauwkeurigheid en hoge bias in modelvoorspellingen
![Een classificatiemodel met hoge nauwkeurigheid en hoge bias in modelvoorspellingen](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Precisie-recall grafiek
#### <a name="what-is-a-precision-recall-chart"></a>Wat is een precisie-recall grafiek?
De precisie-recall curve toont de relatie tussen precisie en terugroepen van een model. De term precisie vertegenwoordigt die mogelijkheid voor een model om alle instanties correct te labelen. Recall vertegenwoordigt de mogelijkheid voor een classificatie om alle exemplaren van een bepaald label te vinden.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Wat doet geautomatiseerde ML met de precisie-recall grafiek?

Met deze grafiek u de precisie-recall curven voor elk model vergelijken om te bepalen welk model een aanvaardbare relatie heeft tussen precisie en terugroepen voor uw specifieke bedrijfsprobleem. Deze grafiek toont Macro Average Precision-Recall, Micro Average Precision-Recall en de precisie-recall die is gekoppeld aan alle klassen voor een model. 

Macro-gemiddelde berekent de statistiek onafhankelijk van elke klasse en neemt vervolgens het gemiddelde, behandelen alle klassen gelijk. Het microgemiddelde verzamelt echter de bijdragen van alle klassen om het gemiddelde te berekenen. Micro-gemiddelde heeft de voorkeur als er klasse onbalans aanwezig is in de dataset.

#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
Afhankelijk van het doel van het bedrijfsprobleem kan de ideale precisie-terugroepcurve verschillen. Enkele voorbeelden worden hieronder gegeven

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Voorbeeld 1: Een classificatiemodel met lage precisie en lage terugroepactie
![Een classificatiemodel met lage precisie en lage terugroepactie](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Voorbeeld 2: Een classificatiemodel met ~ 100% precisie en ~ 100% terugroepen 
![Een classificatiemodel met hoge precisie en terugroepactie](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>ROC-diagram

#### <a name="what-is-a-roc-chart"></a>Wat is een ROC-kaart?
Ontvanger operating kenmerk (of ROC) is een plot van de correct geclassificeerde labels vs. de onjuist geclassificeerde labels voor een bepaald model. De ROC-curve kan minder informatief zijn bij het trainen van modellen op datasets met een hoge bias, omdat het niet de fout-positieve labels zal laten zien.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Wat doet geautomatiseerde ML met de ROC-grafiek?
Geautomatiseerde ML genereert Macro Average Precision-Recall, Micro Average Precision-Recall en de precisie-recall in verband met alle klassen voor een model. 

Macro-gemiddelde berekent de statistiek onafhankelijk van elke klasse en neemt vervolgens het gemiddelde, behandelen alle klassen gelijk. Het microgemiddelde verzamelt echter de bijdragen van alle klassen om het gemiddelde te berekenen. Micro-gemiddelde heeft de voorkeur als er klasse onbalans aanwezig is in de dataset.

#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
Idealiter zal het model dichter bij 100% true positive rate en dichter bij 0% vals positief tarief. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Voorbeeld 1: Een classificatiemodel met lage true labels en hoge valse labels
![Classificatiemodel met lage true labels en hoge valse labels](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Voorbeeld 2: Een classificatiemodel met hoge true labels en lage valse labels
![een classificatiemodel met hoge true labels en lage valse labels](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Liftdiagram
#### <a name="what-is-a-lift-chart"></a>Wat is een liftkaart?
Liftdiagrammen worden gebruikt om de prestaties van een classificatiemodel te evalueren. Het laat zien hoeveel beter je verwachten te doen met het gegenereerde model in vergelijking met zonder een model in termen van nauwkeurigheid.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Wat doet geautomatiseerde ML met de liftgrafiek?
U de lift van het model dat automatisch is gebouwd met Azure Machine Learning vergelijken met de basislijn om de waardewinst van dat specifieke model te bekijken.
#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Voorbeeld 1: Een classificatiemodel dat slechter doet dan een willekeurig selectiemodel
![Een classificatiemodel dat slechter doet dan een willekeurig selectiemodel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Voorbeeld 2: Een classificatiemodel dat beter presteert dan een willekeurig selectiemodel
![Een classificatiemodel dat beter presteert](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Winsten grafiek
#### <a name="what-is-a-gains-chart"></a>Wat is een winstgrafiek?

Een winstdiagram evalueert de prestaties van een classificatiemodel op elk gedeelte van de gegevens. Het toont voor elk percentiel van de gegevensset, hoeveel beter je verwachten uit te voeren in vergelijking met een willekeurig selectiemodel.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Wat doet geautomatiseerde ML met de winstgrafiek?
Gebruik de cumulatieve winstgrafiek om u te helpen bij het kiezen van de classificatiecutoff met behulp van een percentage dat overeenkomt met een gewenste winst uit het model. Deze informatie biedt een andere manier om te kijken naar de resultaten in de bijbehorende lift grafiek.

#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Voorbeeld 1: Een classificatiemodel met minimale winst
![een classificatiemodel met minimale winst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Voorbeeld 2: Een classificatiemodel met aanzienlijke winst
![Een classificatiemodel met aanzienlijke winst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Kalibratiediagram

#### <a name="what-is-a-calibration-chart"></a>Wat is een kalibratiegrafiek?
Een kalibratieplot wordt gebruikt om het vertrouwen van een voorspellend model weer te geven. Het doet dit door de relatie tussen de voorspelde waarschijnlijkheid en de werkelijke waarschijnlijkheid weer te geven, waarbij "waarschijnlijkheid" de waarschijnlijkheid vertegenwoordigt dat een bepaalde instantie onder een bepaald label thuishoort.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Wat doet geautomatiseerde ML met de kalibratiegrafiek?
Voor alle classificatieproblemen u de kalibratielijn voor microgemiddelde, macrogemiddelde en elke klasse in een bepaald voorspellend model bekijken.

Macro-gemiddelde berekent de statistiek onafhankelijk van elke klasse en neemt vervolgens het gemiddelde, behandelen alle klassen gelijk. Het microgemiddelde verzamelt echter de bijdragen van alle klassen om het gemiddelde te berekenen. 
#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
 Een goed gekalibreerd model sluit aan bij de y=x-lijn, waar het redelijk vertrouwen heeft in zijn voorspellingen. Een over-zelfverzekerd model komt overeen met de y=0-lijn, waarbij de voorspelde waarschijnlijkheid aanwezig is, maar er geen werkelijke waarschijnlijkheid is. 


##### <a name="example-1-a-well-calibrated-model"></a>Voorbeeld 1: Een goed gekalibreerd model
![ meer goed gekalibreerd model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Voorbeeld 2: Een over-zelfverzekerd model
![Een over-zelfverzekerd model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a>Regressieresultaten

De volgende statistieken en grafieken zijn beschikbaar voor elk regressiemodel dat u maakt met behulp van de geautomatiseerde machine learning-mogelijkheden van Azure Machine Learning

+ [Statistieken](#reg-metrics)
+ [Voorspeld vs. True](#pvt)
+ [Histogram van resten](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a>Regressiestatistieken

De volgende statistieken worden opgeslagen in elke run iteratie voor een regressie of forecasting taak.

|Gegevens|Beschrijving|Berekening|Extra Parameters
--|--|--|--|
explained_variance|Uitgelegde variantie is de verhouding waarmee een wiskundig model de variatie van een bepaalde gegevensset voorzijn. Het is de procentuele afname van de variantie van de oorspronkelijke gegevens tot de variantie van de fouten. Wanneer het gemiddelde van de fouten 0 is, is het gelijk aan uitgelegde variantie.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Geen|
r2_score|R2 is de bepalingscoëfficiënt of de procentuele vermindering van kwadraatfouten in vergelijking met een basislijnmodel dat het gemiddelde produceert. |[Berekening](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Geen|
spearman_correlation|Spearman correlatie is een niet-parametrische maat voor de monotonie van de relatie tussen twee datasets. In tegenstelling tot de Pearson correlatie, gaat de Spearman correlatie er niet van uit dat beide gegevenssets normaal gesproken worden gedistribueerd. Net als andere correlatiecoëfficiënten varieert deze tussen -1 en +1 met 0, wat geen correlatie impliceert. Correlaties van -1 of +1 impliceren een exacte monotoonrelatie. Positieve correlaties impliceren dat als x toeneemt, y dat ook doet. Negatieve correlaties impliceren dat als x toeneemt, y afneemt.|[Berekening](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Geen|
mean_absolute_error|Gemiddelde absolute fout is de verwachte waarde van de absolute waarde van het verschil tussen het doel en de voorspelling|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Geen|
normalized_mean_absolute_error|Genormaliseerde gemiddelde absolute fout is gemiddelde absolute fout gedeeld door het bereik van de gegevens|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Delen door bereik van de gegevens|
median_absolute_error|Mediane absolute fout is de mediaan van alle absolute verschillen tussen het doel en de voorspelling. Dit verlies is robuust voor uitschieters.|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Geen|
normalized_median_absolute_error|Genormaliseerde mediaan absolute fout is mediane absolute fout gedeeld door het bereik van de gegevens|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Delen door bereik van de gegevens|
root_mean_squared_error|Root gemiddelde kwadraat fout is de vierkantswortel van de verwachte kwadraat verschil tussen het doel en de voorspelling|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Geen|
normalized_root_mean_squared_error|Genormaliseerde wortelgemiddelde kwadraatfout is rootgemiddelde kwadraatfout gedeeld door het bereik van de gegevens|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Delen door bereik van de gegevens|
root_mean_squared_log_error|Root gemiddelde kwadraat log fout is de vierkantswortel van de verwachte kwadraat logaritmische fout|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Geen|
normalized_root_mean_squared_log_error|Genormaliseerde rootgemiddelde kwadraatlogboekfout is rootgemiddelde kwadraatlogboekfout gedeeld door het bereik van de gegevens|[Berekening](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Delen door bereik van de gegevens|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a>Voorspeld vs. True-grafiek
#### <a name="what-is-a-predicted-vs-true-chart"></a>Wat is een Voorspelde vs. True grafiek?
Predicted vs. True toont de relatie tussen een voorspelde waarde en de correlerende werkelijke waarde voor een regressieprobleem. Deze grafiek kan worden gebruikt om de prestaties van een model te meten, hoe dichter bij de y=x-lijn de voorspelde waarden zijn, hoe beter de nauwkeurigheid van een voorspellend model.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Wat doet geautomatiseerde ML met de Predicted vs. True-grafiek?
Na elke run ziet u een voorspelde versus echte grafiek voor elk regressiemodel. Om de privacy van gegevens te beschermen, worden waarden samen opgeslagen en wordt de grootte van elke opslaglocatie weergegeven als een staafgrafiek op het onderste gedeelte van het grafiekgebied. U het voorspellende model vergelijken met het lichtere schaduwgebied met foutmarges, tegen de ideale waarde van waar het model zou moeten zijn.

#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Voorbeeld 1: Een classificatiemodel met lage nauwkeurigheid
![Een regressiemodel met een lage nauwkeurigheid in voorspellingen](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Voorbeeld 2: Een regressiemodel met hoge nauwkeurigheid 
[![Een regressiemodel met hoge nauwkeurigheid in zijn voorspellingen](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a>Histogram van restwaardengrafiek
#### <a name="what-is-a-residuals-chart"></a>Wat is een restdiagram?
Een restwaarde vertegenwoordigt een waargenomen y – de voorspelde y. Om een foutmarge met een lage bias te laten zien, moet het histogram van resten worden gevormd als een belcurve, gecentreerd rond 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Wat doet geautomatiseerde ML met de restwaardegrafiek?
Geautomatiseerde ML biedt automatisch een restdiagram om de verdeling van fouten in de voorspellingen weer te geven.
#### <a name="what-does-a-good-model-look-like"></a>Hoe ziet een goed model eruit?
Een goed model zal meestal een bel curve of fouten rond nul.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Voorbeeld 1: Een regressiemodel met bias in zijn fouten
![SA regressie model met bias in zijn fouten](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Voorbeeld 2: Een regressiemodel met een gelijkmatigerverdeling van fouten
![Een regressiemodel met een gelijkmatigerverdeling van fouten](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a>Model interpreteerbaarheid en functiebelang
Geautomatiseerde ML biedt een machine learning interpretability dashboard voor uw uitvoeringen.
Zie voor meer informatie over het inschakelen van interpreteerbaarheidsfuncties de [how-to](how-to-machine-learning-interpretability-automl.md) over het inschakelen van interpreteerbaarheid in geautomatiseerde ML-experimenten.

## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over [geautomatiseerde ml](concept-automated-ml.md) in Azure Machine Learning.
+ Probeer de voorbeeldnotitieblokken [van de voorbeeldreeks van het geautomatiseerde machine learning-modeluitleg.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
