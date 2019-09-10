---
title: Wat is geautomatiseerde ML/automl
titleSuffix: Azure Machine Learning service
description: Meer informatie over hoe Azure Machine Learning service automatisch een algoritme voor u kan kiezen en een model kunt genereren om u tijd te besparen met behulp van de para meters en de criteria die u opgeeft om het beste algoritme voor uw model te selecteren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: 319871280b94f54b99f7a9957f671ec50122ebf3
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860908"
---
# <a name="what-is-automated-machine-learning"></a>Wat is geautomatiseerde machine learning?

Automatische machine learning, ook wel autoML genoemd, is het proces van het automatiseren van de tijdrovende, terugkerende taken van het ontwikkelen van machine learning modellen. Zo kunnen gegevens wetenschappers, analisten en ontwikkel aars ML-modellen bouwen met een hoge schaal, efficiëntie en productiviteit, terwijl de kwaliteit van het model goed wordt. Automatische ML is gebaseerd op een door braak van onze [micro soft research-afdeling](https://arxiv.org/abs/1705.05355).

De traditionele ontwikkeling van machine learning modellen is het bronnen-intensief, waardoor de kennis en tijd van het domein belang rijk zijn voor het produceren en vergelijken van tien tallen modellen. Pas automatische ML toe als u wilt dat Azure Machine Learning een model traint en afstemt met behulp van de doel metriek die u opgeeft. De service herhaalt vervolgens een combi natie van ML-algoritmen die zijn gekoppeld aan functie selecties, waarbij elke herhaling een model met een trainings Score produceert. Hoe hoger de score, hoe beter het model wordt beschouwd als uw gegevens.

Met automatische machine learning versnelt u de tijd die nodig is om productie-Ready ML-modellen te verkrijgen met een geweldig gemak en efficiëntie.

## <a name="when-to-use-automated-ml"></a>Wanneer gebruikt u automatische ML?

Automatische ML democratiseert, doordat het ontwikkel proces van het machine learning model en biedt gebruikers de mogelijkheid om een end-to-end machine learning-pijp lijn te identificeren voor een probleem.

Gegevens wetenschappers, analisten en ontwikkel aars in verschillende branches kunnen gebruikmaken van automatische MILLILITERs om:

+ machine learning oplossingen implementeren zonder uitgebreide programmeer kennis
+ Bespaar tijd en resources
+ Best practices voor data wetenschappen gebruiken
+ Flexibel probleem oplossing bieden-oplossen

## <a name="how-automated-ml-works"></a>Hoe geautomatiseerd ML werkt

Met behulp van **Azure machine learning-service**kunt u uw automatische ml-experimenten ontwerpen en uitvoeren met de volgende stappen:

1. **Bepaal welk ml-probleem** moet worden opgelost: classificatie, prognose of regressie

1. **Geef de bron en de indeling van de gelabelde trainings gegevens op**: Numpy-matrices of Panda-data frame

1. **Configureer het reken doel voor model training**, zoals uw [lokale computer, Azure machine learning reken processen, externe vm's of Azure Databricks](how-to-set-up-training-targets.md).  Meer informatie over geautomatiseerde training [op een externe bron](how-to-auto-train-remote.md).

1. **Configureer de para meters voor automatische machine learning** die bepalen hoeveel iteraties boven verschillende modellen, afstemming-instellingen, geavanceerde preverwerking/parametrisatie en welke metrische gegevens er moeten worden weer gegeven bij het bepalen van het beste model.  U kunt de instellingen voor automatische studie experiment configureren in [Azure Portal](how-to-create-portal-experiments.md), [de landings pagina voor de werk ruimte (preview)](https://ml.azure.com)of [met de SDK](how-to-configure-auto-train.md). 

1. **Verzend de trainings uitvoering.**

  ![Geautomatiseerde machine learning](./media/how-to-automated-ml/automl-concept-diagram2.png)

Tijdens de training maakt de Azure Machine Learning-service een aantal parallelle pijp lijnen die verschillende algoritmen en para meters proberen. Het wordt gestopt zodra de afsluit criteria die in het experiment zijn gedefinieerd, zijn gevonden.

U kunt ook de informatie over geregistreerde uitvoeringen controleren, die de [metrische gegevens bevat](how-to-understand-automated-ml.md) die tijdens de uitvoering zijn verzameld. De trainings uitvoering produceert een met python geserialiseerd object (`.pkl` bestand) dat het model en de voor verwerking van gegevens bevat.

Hoewel het bouwen van modellen geautomatiseerd is, kunt u ook [zien hoe belang rijke of relevante functies](how-to-configure-auto-train.md#explain) voor de gegenereerde modellen zijn.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Verwerking

In elk automatisch machine learning experiment worden uw gegevens voorverwerkt met behulp van de standaard methoden en optioneel via geavanceerde voor verwerking.

> [!NOTE]
> Automatische machine learning vooraf verwerkte stappen (functie normalisatie, het verwerken van ontbrekende gegevens, het converteren van tekst naar numerieke waarde, enzovoort) worden onderdeel van het onderliggende model. Wanneer u het model gebruikt voor voor spellingen, worden dezelfde vooraf verwerkings stappen die tijdens de training worden toegepast, automatisch toegepast op uw invoer gegevens.

### <a name="automatic-preprocessing-standard"></a>Automatische voor verwerking (standaard)

In elk automatisch machine learning experiment worden uw gegevens automatisch geschaald of genormaliseerd om de Help-algoritmen goed uit te voeren.  Tijdens de model training wordt een van de volgende schalen of normalisatie technieken toegepast op elk model.

|&nbsp;Normalisatieaanpassen&&nbsp;| Description |
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

+ Python-SDK: Opgeven `"preprocess": True` voor [ de`AutoMLConfig` klasse](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).


## <a name="time-series-forecasting"></a>Prognoses met tijdreeksen
Het maken van prognoses is een integraal onderdeel van een bedrijf, of het nu gaat om inkomsten, inventaris, verkoop of klant vraag. U kunt automatische ML gebruiken om technieken en benaderingen te combi neren en een aanbevolen prognose voor de time-series van hoge kwaliteit te krijgen.

Een geautomatiseerd experiment in de tijd reeks wordt behandeld als een multidimensionale regressie probleem. De waarden voor de laatste tijd reeksen zijn ' gedraaid ' om aanvullende dimensies te krijgen voor de regressor hierop samen met andere voor spellingen. Deze methode, in tegens telling tot de methoden van de klassieke tijd reeks, heeft een voor deel van het gebruik van natuurlijk meerdere contextuele variabelen en hun relatie met elkaar tijdens de training. Automatische ML leert een enkelvoudig, maar vaak intern vertakkings model voor alle items in de gegevensset en de voor spellingen Horizons. Er zijn meer gegevens beschikbaar voor het schatten van model parameters en generalisatie naar onzichtbaar serie.

Meer informatie en een voor beeld bekijken van [automatische machine learning voor tijdreeks prognoses](how-to-auto-train-forecast.md).

## <a name="ensemble"></a>Ensemble-modellen

Automatische machine learning ondersteunt ensemble-modellen die standaard zijn ingeschakeld. Ensemble Learning verbetert de machine learning resultaten en voorspellende prestaties door het combi neren van meerdere modellen, in tegens telling tot het gebruik van één model. De ensembles herhalingen worden weer gegeven als de laatste herhalingen van de uitvoering. Automatische machine learning maakt gebruik van zowel stem-als gestapelde ensemble-methoden voor het combi neren van modellen:

* **Stem**: voor speld op basis van het gewogen gemiddelde van voorspelde klasse-kansen (voor classificatie taken) of voorspelde regressie doelen (voor regressie taken).
* **Stapelen**: stacking combineert heterogene-modellen en treinen een meta model op basis van de uitvoer van de afzonderlijke modellen. De huidige standaard-META modellen zijn LogisticRegression voor classificatie taken en ElasticNet voor regressie-en prognose taken.

Het [selectie algoritme van Caruana ensemble](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) met gesorteerde ensemble-initialisatie wordt gebruikt om te bepalen welke modellen er moeten worden gebruikt in de ensemble. Op hoog niveau initialiseert dit algoritme de ensemble met Maxi maal 5 modellen met de beste afzonderlijke scores en verifieert dat deze modellen binnen 5% drempelwaarde van de beste score zijn om een slechte initiële ensemble te voor komen. Vervolgens wordt voor elke ensemble-iteratie een nieuw model toegevoegd aan de bestaande ensemble en wordt de resulterende score berekend. Als een nieuw model de bestaande ensemble-score heeft verbeterd, wordt de ensemble bijgewerkt met het nieuwe model.

Zie de [procedure](how-to-configure-auto-train.md#ensemble) voor het wijzigen van standaard-ensemble-instellingen in automatische machine learning.

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

+ Volg de [zelf studie: Een regressie model automatisch trainen met geautomatiseerde Machine Learning van Azure](tutorial-auto-train-models.md)

+ De instellingen voor automatische training-experiment configureren:
  + [Gebruik de volgende stappen](how-to-create-portal-experiments.md)in azure Portal interface of de lands pagina voor de werk ruimte (preview).
  + Gebruik de python-SDK om de [volgende stappen uit te voeren](how-to-configure-auto-train.md).

+ Meer informatie over het automatisch trainen van Time Series-gegevens met behulp van [deze stappen](how-to-auto-train-forecast.md).

+ [Jupyter notebook voor beelden uitproberen voor automatische machine learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
