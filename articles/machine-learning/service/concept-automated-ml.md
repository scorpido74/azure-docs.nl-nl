---
title: Wat is geautomatiseerde ML/automl
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe Azure Machine Learning automatisch een algoritme voor u kunt kiezen, en hoe u hiermee een model genereert om u tijd te besparen met behulp van de para meters en de criteria die u opgeeft om het beste algoritme voor uw model te selecteren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: e9bc0cf3ab0d168a55ab04913bdc71eed5c5fced
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001228"
---
# <a name="what-is-automated-machine-learning"></a>Wat is geautomatiseerde machine learning?

Automatische machine learning, ook wel automatische ML genoemd, is het proces van het automatiseren van de tijdrovende, terugkerende taken van het ontwikkelen van machine learning modellen. Zo kunnen gegevens wetenschappers, analisten en ontwikkel aars ML-modellen bouwen met een hoge schaal, efficiëntie en productiviteit, terwijl de kwaliteit van het model goed wordt. Automatische ML is gebaseerd op een door braak van onze [micro soft research-afdeling](https://arxiv.org/abs/1705.05355).

De traditionele ontwikkeling van machine learning modellen is het bronnen-intensief, waardoor de kennis en tijd van het domein belang rijk zijn voor het produceren en vergelijken van tien tallen modellen. Pas automatische ML toe als u wilt dat Azure Machine Learning een model traint en afstemt met behulp van de doel metriek die u opgeeft. De service herhaalt vervolgens een combi natie van ML-algoritmen die zijn gekoppeld aan functie selecties, waarbij elke herhaling een model met een trainings Score produceert. Hoe hoger de score, hoe beter het model wordt beschouwd als uw gegevens.

Met automatische machine learning versnelt u de tijd die nodig is om productie-Ready ML-modellen te verkrijgen met een geweldig gemak en efficiëntie.

## <a name="when-to-use-automated-ml"></a>Wanneer gebruikt u automatische ML?

Automatische ML democratiseert, doordat het ontwikkel proces van het machine learning model en biedt gebruikers de mogelijkheid om een end-to-end machine learning-pijp lijn te identificeren voor een probleem.

Gegevens wetenschappers, analisten en ontwikkel aars in verschillende branches kunnen gebruikmaken van automatische MILLILITERs om:

+ machine learning oplossingen implementeren zonder uitgebreide programmeer kennis
+ Bespaar tijd en resources
+ Best practices voor data wetenschappen gebruiken
+ Flexibel probleem oplossing bieden-oplossen

Veelvoorkomende cases voor automatische MILLILITERs zijn:
+ [Fraude detectie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)
+ [Marketing voorspelling](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing/auto-ml-classification-bank-marketing.ipynb)
+ [Vraag prognose](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
+ [Verkoop prognose](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)
+ [Prestaties voors pellen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance/auto-ml-regression-hardware-performance.ipynb)
+ [Materiaal duurzaamheid voor spelling](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-concrete-strength/auto-ml-regression-concrete-strength.ipynb)

## <a name="how-automated-ml-works"></a>Hoe geautomatiseerd ML werkt

Met **Azure machine learning**kunt u uw automatische ml-experimenten ontwerpen en uitvoeren met de volgende stappen:

1. **Bepaal welk ml-probleem** moet worden opgelost: classificatie, prognose of regressie

1. **Geef de bron en de indeling van de gelabelde trainings gegevens op**: Numpy-matrices of Panda-data frame

1. **Configureer het reken doel voor model training**, zoals uw [lokale computer, Azure machine learning reken processen, externe vm's of Azure Databricks](how-to-set-up-training-targets.md).  Meer informatie over geautomatiseerde training [op een externe bron](how-to-auto-train-remote.md).

1. **Configureer de para meters voor automatische machine learning** die bepalen hoeveel iteraties boven verschillende modellen, afstemming-instellingen, geavanceerde preverwerking/parametrisatie en welke metrische gegevens er moeten worden weer gegeven bij het bepalen van het beste model.  U kunt de instellingen voor automatische studie experiment configureren in [Azure Portal](how-to-create-portal-experiments.md), [de landings pagina voor de werk ruimte (preview)](https://ml.azure.com)of [met de SDK](how-to-configure-auto-train.md). 

1. **Verzend de trainings uitvoering.**

  ![Geautomatiseerde machine learning](./media/how-to-automated-ml/automl-concept-diagram2.png)

Tijdens de training maakt Azure Machine Learning een aantal parallelle pijp lijnen die verschillende algoritmen en para meters proberen. Het wordt gestopt zodra de afsluit criteria die in het experiment zijn gedefinieerd, zijn gevonden.

U kunt ook de informatie over geregistreerde uitvoeringen controleren, die de [metrische gegevens bevat](how-to-understand-automated-ml.md) die tijdens de uitvoering zijn verzameld. De training-uitvoering produceert een met python geserialiseerd object (`.pkl`-bestand) dat het model en de voor verwerking van gegevens bevat.

Hoewel het bouwen van modellen geautomatiseerd is, kunt u ook [zien hoe belang rijke of relevante functies](how-to-configure-auto-train.md#explain) voor de gegenereerde modellen zijn.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Verwerking

In elk automatisch machine learning experiment worden uw gegevens voorverwerkt met behulp van de standaard methoden en optioneel via geavanceerde voor verwerking.

> [!NOTE]
> Automatische machine learning vooraf verwerkte stappen (functie normalisatie, het verwerken van ontbrekende gegevens, het converteren van tekst naar numerieke waarde, enzovoort) worden onderdeel van het onderliggende model. Wanneer u het model gebruikt voor voor spellingen, worden dezelfde vooraf verwerkings stappen die tijdens de training worden toegepast, automatisch toegepast op uw invoer gegevens.

### <a name="automatic-preprocessing-standard"></a>Automatische voor verwerking (standaard)

In elk automatisch machine learning experiment worden uw gegevens automatisch geschaald of genormaliseerd om de Help-algoritmen goed uit te voeren.  Tijdens de model training wordt een van de volgende schalen of normalisatie technieken toegepast op elk model.

|Schalen met @ no__t-0 @ no__t-1 @ no__t-2normalization| Description |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Functies standaardiseren door het gemiddelde en de schaal aanpassing te verwijderen voor eenheids variantie  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformeert functies door elke functie te schalen op basis van het minimum en maximum van die kolom  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Elke functie schalen met de Maxi maal absolute waarde |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Deze schaal functies op basis van hun quantile bereik |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Lineaire dimensionaliteit door de gegevens uit de enkelvoudige waarde te desamen stellen om deze te projecteren in een gereduceerde ruimte |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Deze transformator voert lineaire dimensionaliteit uit met behulp van een afgekapte enkelvouds waarde (SVD). In tegens telling tot PCA worden de gegevens door deze Estimator niet gecentreerd voordat de enkelvoudige waarde wordt uitgevouwen. Dit betekent dat het kan worden gebruikt met scipy. sparse-matrices |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Elk voor beeld (dat wil zeggen, elke rij van de gegevens matrix) met ten minste één niet-nul onderdeel wordt opnieuw geschaald, onafhankelijk van andere steek proeven, zodat de norm (L1 of L2) gelijk is aan 1 |

### <a name="advanced-preprocessing-optional-featurization"></a>Geavanceerde voor verwerking: optionele parametrisatie

Er zijn ook aanvullende geavanceerde preverwerkings-en parametrisatie beschikbaar, zoals ontbrekende waarden, code ring en trans formaties. Meer [informatie over wat parametrisatie is inbegrepen](how-to-create-portal-experiments.md#preprocess). Schakel deze instelling in met:

+ Azure Portal: Selecteer het selectie vakje **preprocess** in de **Geavanceerde instellingen** [met de volgende stappen](how-to-create-portal-experiments.md).

+ Python-SDK: @No__t-0 opgeven voor de [`AutoMLConfig`-klasse](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).


## <a name="time-series-forecasting"></a>Prognoses met tijdreeksen
Het maken van prognoses is een integraal onderdeel van een bedrijf, of het nu gaat om inkomsten, inventaris, verkoop of klant vraag. U kunt automatische ML gebruiken om technieken en benaderingen te combi neren en een aanbevolen prognose voor de time-series van hoge kwaliteit te krijgen.

Een geautomatiseerd experiment in de tijd reeks wordt behandeld als een multidimensionale regressie probleem. De waarden voor de laatste tijd reeksen zijn ' gedraaid ' om aanvullende dimensies te krijgen voor de regressor hierop samen met andere voor spellingen. Deze methode, in tegens telling tot de methoden van de klassieke tijd reeks, heeft een voor deel van het gebruik van natuurlijk meerdere contextuele variabelen en hun relatie met elkaar tijdens de training. Automatische ML leert een enkelvoudig, maar vaak intern vertakkings model voor alle items in de gegevensset en de voor spellingen Horizons. Er zijn meer gegevens beschikbaar voor het schatten van model parameters en generalisatie naar onzichtbaar serie.

Meer informatie en een voor beeld bekijken van [automatische machine learning voor tijdreeks prognoses](how-to-auto-train-forecast.md). U kunt ook de [Energy demand notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) raadplegen voor gedetailleerde code voorbeelden van de geavanceerde prognose configuratie, waaronder:

* Kerst detectie en parametrisatie
* Cross-Origin-Kruis validatie
* Configureer bare lags
* statistische functies van het Rolling venster

## <a name="ensemble"></a>Ensemble-modellen

Automatische machine learning ondersteunt ensemble-modellen die standaard zijn ingeschakeld. Ensemble Learning verbetert de machine learning resultaten en voorspellende prestaties door het combi neren van meerdere modellen, in tegens telling tot het gebruik van één model. De ensembles herhalingen worden weer gegeven als de laatste herhalingen van de uitvoering. Automatische machine learning maakt gebruik van zowel stem-als gestapelde ensemble-methoden voor het combi neren van modellen:

* **Stem**: voor speld op basis van het gewogen gemiddelde van voorspelde klasse-kansen (voor classificatie taken) of voorspelde regressie doelen (voor regressie taken).
* **Stapelen**: stacking combineert heterogene-modellen en treinen een meta model op basis van de uitvoer van de afzonderlijke modellen. De huidige standaard-META modellen zijn LogisticRegression voor classificatie taken en ElasticNet voor regressie-en prognose taken.

Het [selectie algoritme van Caruana ensemble](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) met gesorteerde ensemble-initialisatie wordt gebruikt om te bepalen welke modellen er moeten worden gebruikt in de ensemble. Op hoog niveau initialiseert dit algoritme de ensemble met Maxi maal 5 modellen met de beste afzonderlijke scores en verifieert dat deze modellen binnen 5% drempelwaarde van de beste score zijn om een slechte initiële ensemble te voor komen. Vervolgens wordt voor elke ensemble-iteratie een nieuw model toegevoegd aan de bestaande ensemble en wordt de resulterende score berekend. Als een nieuw model de bestaande ensemble-score heeft verbeterd, wordt de ensemble bijgewerkt met het nieuwe model.

Zie de [procedure](how-to-configure-auto-train.md#ensemble) voor het wijzigen van standaard-ensemble-instellingen in automatische machine learning.

## <a name="imbalance"></a>Gegevens in het gebalanceerd

Gegevens die in het algemeen worden gebalanceerd, worden doorgaans gevonden in gegevens voor machine learning classificatie scenario's en verwijzen naar gegevens die een onevenredige verhouding van waarnemingen in elke klasse bevatten. Dit onevenwichtige kan leiden tot een onverantwoord waargenomen positief effect van de nauw keurigheid van een model, omdat de invoer gegevens van invloed zijn op één klasse, wat resulteert in het getrainde model om die afwijking te simuleren. 

Als onderdeel van het vereenvoudigen van de machine learning-werk stroom, heeft geautomatiseerd ML ingebouwde mogelijkheden voor het oplossen van niet-sluitende gegevens zoals, 

- Een **kolom**met het gewicht: automatische milliliters biedt ondersteuning voor een gewogen kolom als invoer, waardoor rijen in de gegevens naar boven of beneden worden gewogen, waardoor een klasse meer of minder ' belang rijk ' kan maken. Bekijk dit [voor beeld van een notitie blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/sample-weight/auto-ml-sample-weight.ipynb) 

- De algoritmen die door automatische ML worden gebruikt, kunnen de onevenwichtigheid van Maxi maal 20:1 afhandelen, wat betekent dat de meest voorkomende klasse 20 keer meer rijen in de gegevens kan hebben dan de kleinste gemeen schappelijke klasse.

### <a name="identify-models-with-imbalanced-data"></a>Modellen met niet-sluitende gegevens identificeren

Aangezien classificatie algoritmen doorgaans worden geëvalueerd door nauw keurigheid, is het controleren van de nauw keurigheid van een model een goede manier om te bepalen of dit van invloed is op gegevens die niet in balans zijn. Had het een zeer hoge nauw keurigheid of een zeer lage nauw keurigheid voor bepaalde klassen?

Daarnaast worden automatisch de volgende grafieken gegenereerd, die u kunnen helpen inzicht te krijgen in de juistheid van de classificaties van uw model en modellen te identificeren die mogelijk van invloed zijn op gegevens die niet in evenwicht zijn.

Grafiek| Description
---|---
[Verwar ring matrix](how-to-understand-automated-ml.md#confusion-matrix)| Hiermee worden de juist geclassificeerde labels geëvalueerd op basis van de daad werkelijke labels van de gegevens. 
[Precisie-intrekken](how-to-understand-automated-ml.md#precision-recall-chart)| Evalueert de verhouding van de juiste labels op basis van de verhouding van gevonden label exemplaren van de gegevens 
[ROC curven](how-to-understand-automated-ml.md#roc)| Evalueert de verhouding van de juiste labels op basis van de verhouding van ONWAAR-positieve labels.

### <a name="handle-imbalanced-data"></a>Niet-sluitende gegevens verwerken 

De volgende technieken zijn aanvullende opties voor het afhandelen van niet-sluitende gegevens buiten de automatische MILLILITERs. 

- Hersampling naar zelfs het onevenwicht van de klasse, hetzij door de kleinere klassen te bemonsteren of de grotere klassen te verlagen. Deze methoden vereisen expertise om te verwerken en te analyseren.

- Gebruik prestatie metrieken die beter werken met gegevens die niet in balans zijn. De F1-Score is bijvoorbeeld een gewogen gemiddelde van de precisie en intrekken. Precisie meet de nauw keurigheid van een classificatie--lage precisie geeft aan dat er een groot aantal fout-positieven is--, terwijl intrekken meet waarden voor de volledigheid van een classificatie, een hoog aantal onwaare negatieven aangeeft. 

## <a name="use-with-onnx-in-c-apps"></a>Gebruiken met ONNX in C# apps

Met Azure Machine Learning kunt u automatische ML gebruiken om een python-model samen te stellen en dit te converteren naar de ONNX-indeling. De ONNX-runtime C#ondersteunt, zodat u het model dat automatisch in uw C# apps is gebouwd, kunt gebruiken zonder dat u hoeft te coderen of een van de netwerk latenties die rest-eind punten introduceren. Probeer een voor beeld van deze stroom [in dit Jupyter-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatische MILLILITERs in micro soft

Automatische ML is ook beschikbaar in andere micro soft-oplossingen, zoals:

|Integraties|Description|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Automatische model selectie en-training in .NET-Apps met Visual Studio en Visual Studio code met ML.NET Automated ML (preview).|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Schaal uw geautomatiseerde ML-trainings taken op Spark in HDInsight-clusters parallel.|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Machine learning modellen rechtstreeks aanroepen in Power BI (preview).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Maak nieuwe machine learning modellen over uw gegevens in SQL Server 2019 big data-clusters.|

## <a name="next-steps"></a>Volgende stappen

Bekijk voor beelden en leer hoe u modellen bouwt met geautomatiseerde machine learning:

+ Volg de [Tutorial: Een regressie model automatisch trainen met automatische Machine Learning van Azure @ no__t-0

+ De instellingen voor automatische training-experiment configureren:
  + [Gebruik de volgende stappen](how-to-create-portal-experiments.md)in azure Portal interface of de lands pagina voor de werk ruimte (preview).
  + Gebruik de python-SDK om de [volgende stappen uit te voeren](how-to-configure-auto-train.md).

+ Meer informatie over het automatisch trainen van Time Series-gegevens met behulp van [deze stappen](how-to-auto-train-forecast.md).

+ [Jupyter notebook voor beelden uitproberen voor automatische machine learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
