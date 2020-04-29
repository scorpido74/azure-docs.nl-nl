---
title: Wat is geautomatiseerde ML/AutoML
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe Azure Machine Learning automatisch een algoritme voor u kunt kiezen, en hoe u hiermee een model genereert om u tijd te besparen met behulp van de para meters en de criteria die u opgeeft om het beste algoritme voor uw model te selecteren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 04/22/2020
ms.openlocfilehash: f592a7f5a4af38988bcf433f0adc89d9be7579cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82082006"
---
# <a name="what-is-automated-machine-learning-automl"></a>Wat is geautomatiseerde machine learning (AutoML)?

Automatische machine learning, ook wel geautomatiseerd ML of AutoML genoemd, is het proces van het automatiseren van de tijdrovende, terugkerende taken van het ontwikkelen van machine learning modellen. Zo kunnen gegevens wetenschappers, analisten en ontwikkel aars ML-modellen bouwen met een hoge schaal, efficiëntie en productiviteit, terwijl de kwaliteit van het model goed wordt. Automatische ML is gebaseerd op een door braak van onze [micro soft research-afdeling](https://arxiv.org/abs/1705.05355).

De traditionele ontwikkeling van machine learning modellen is het bronnen-intensief, waardoor de kennis en tijd van het domein belang rijk zijn voor het produceren en vergelijken van tien tallen modellen. Met automatische machine learning versnelt u de tijd die nodig is om productie-Ready ML-modellen te verkrijgen met een geweldig gemak en efficiëntie.

## <a name="when-to-use-automl-classify-regression--forecast"></a>Wanneer gebruikt u AutoML: classificeren, regressie, & prognose

Pas automatische ML toe als u wilt dat Azure Machine Learning een model traint en afstemt met behulp van de doel metriek die u opgeeft. Automatische ML democratiseert, doordat het ontwikkel proces van het machine learning model en biedt gebruikers de mogelijkheid om een end-to-end machine learning-pijp lijn te identificeren voor een probleem.

Gegevens wetenschappers, analisten en ontwikkel aars in verschillende branches kunnen gebruikmaken van automatische MILLILITERs:
+ Oplossingen voor ML implementeren zonder uitgebreide programmeer kennis
+ Bespaar tijd en resources
+ Best practices voor data wetenschappen gebruiken
+ Flexibel probleem oplossing bieden-oplossen

### <a name="classification"></a>Classificatie

Classificatie is een algemene machine learning taak. Classificatie is een soort gecontroleerde lessen waarin modellen informatie leren met behulp van trainings gegevens en deze informatie kan Toep assen op nieuwe gegevens. Azure Machine Learning biedt featurizations specifiek voor deze taken, zoals diepe Neural featurizers voor de classificatie van netwerk tekst. Meer informatie over [parametrisatie-opties](how-to-use-automated-ml-for-ml-models.md#featurization). 

Het belangrijkste doel van classificatie modellen is om te voors pellen welke categorieën nieuwe gegevens zijn op basis van de informatie van de trainings gegevens. Voor beelden van veelvoorkomende classificaties zijn fraude detectie, handschrift herkenning en object detectie.  Meer informatie en een voor beeld van [classificatie met automatische machine learning](tutorial-train-models-with-aml.md).

Zie voor beelden van classificatie en geautomatiseerde machine learning in deze python-notebooks: [fraude detectie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb), [marketing voorspelling](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)en [gegevens classificatie van nieuws groep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)

### <a name="regression"></a>Regressie
Net als bij de classificatie zijn regressie taken ook een veelvoorkomende taak onder toezicht. Azure Machine Learning biedt [featurizations specifiek voor deze taken](how-to-use-automated-ml-for-ml-models.md#featurization).

Anders dan classificatie waarbij voorspelde uitvoer waarden categorische zijn, hebben regressie modellen voor speld numerieke uitvoer waarden op basis van onafhankelijke voor spellingen. In regressie is het doel om te helpen bij het vaststellen van de relatie tussen deze onafhankelijke Voorspellings variabelen door te schatten hoe de ene variabele invloed heeft op de andere. Bijvoorbeeld prijs voor auto Mobile op basis van functies zoals het beslagen van gas, veiligheids classificatie, enzovoort. Lees meer informatie en Bekijk een voor beeld van [regressie met automatische machine learning](tutorial-auto-train-models.md).

Bekijk voor beelden van regressie en automatische machine learning voor voor spellingen in deze python-notebooks: CPU-prestaties voors [pellen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb), 

### <a name="time-series-forecasting"></a>Prognose van Time-Series

Het maken van prognoses is een integraal onderdeel van een bedrijf, of het nu gaat om inkomsten, inventaris, verkoop of klant vraag. U kunt automatische ML gebruiken om technieken en benaderingen te combi neren en een aanbevolen prognose voor de time-series van hoge kwaliteit te krijgen. Meer informatie over deze procedure: [geautomatiseerde machine learning voor time series-prognoses](how-to-auto-train-forecast.md). 

Een geautomatiseerd experiment in de tijd reeks wordt behandeld als een multidimensionale regressie probleem. De waarden voor de laatste tijd reeksen zijn ' gedraaid ' om aanvullende dimensies te krijgen voor de regressor hierop samen met andere voor spellingen. Deze methode, in tegens telling tot de methoden van de klassieke tijd reeks, heeft een voor deel van het gebruik van natuurlijk meerdere contextuele variabelen en hun relatie met elkaar tijdens de training. Automatische ML leert een enkelvoudig, maar vaak intern vertakkings model voor alle items in de gegevensset en de voor spellingen Horizons. Er zijn meer gegevens beschikbaar voor het schatten van model parameters en generalisatie naar onzichtbaar serie.

De geavanceerde prognose configuratie bevat:
* Kerst detectie en parametrisatie
* time-series-en DNN-leerers (automatisch ARIMA, Prophet, ForecastTCN)
* veel modellen bieden ondersteuning via groepering
* Cross-Origin-Kruis validatie
* Configureer bare lags
* statistische functies van het Rolling venster


Bekijk voor beelden van regressie en automatische machine learning voor voor spellingen in deze python-notebooks: [verkoop prognose](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb), [vraag prognose](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)en [drank productie prognose](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

## <a name="how-automl-works"></a>Hoe AutoML werkt

Tijdens de training maakt Azure Machine Learning een aantal pijp lijnen parallel die verschillende algoritmen en para meters voor u proberen. De service herhaalt een combi natie van ML-algoritmen die zijn gekoppeld aan functie selecties, waarbij elke herhaling een model met een trainings Score produceert. Hoe hoger de score, hoe beter het model wordt beschouwd als uw gegevens.  Het wordt gestopt zodra de afsluit criteria die in het experiment zijn gedefinieerd, zijn gevonden. 

Met **Azure machine learning**kunt u uw automatische ml-experimenten ontwerpen en uitvoeren met de volgende stappen:

1. **Bepaal welk ml-probleem** moet worden opgelost: classificatie, prognose of regressie

1. **Kies of u de python-SDK of de web-ervaring van Studio wilt gebruiken**: meer informatie over de pariteit tussen de [python-SDK en Studio Web Experience](#parity).

   * Voor een beperkte of geen code-ervaring kunt u de Azure Machine Learning Studio Web Experience op[https://ml.azure.com](https://ml.azure.com/)  
   * Bekijk voor python-ontwikkel aars de [Azure machine learning PYTHON SDK](how-to-configure-auto-train.md) 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]  
    
1. **Geef de bron en de indeling van de gelabelde trainings gegevens op**: numpy-matrices of Panda-data frame

1. **Configureer het reken doel voor model training**, zoals uw [lokale computer, Azure machine learning reken processen, externe vm's of Azure Databricks](how-to-set-up-training-targets.md).  Meer informatie over geautomatiseerde training [op een externe bron](how-to-auto-train-remote.md).

1. **Configureer de para meters voor automatische machine learning** die bepalen hoeveel iteraties boven verschillende modellen, afstemming-instellingen, geavanceerde preverwerking/parametrisatie en welke metrische gegevens er moeten worden weer gegeven bij het bepalen van het beste model.  
1. **Verzend de trainings uitvoering.**

1. **De resultaten bekijken** 

In het volgende diagram ziet u dit proces. 
![Geautomatiseerde machine learning](./media/concept-automated-ml/automl-concept-diagram2.png)


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

|&nbsp;&Normalisatie&nbsp;aanpassen| Beschrijving |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Functies standaardiseren door het gemiddelde en de schaal aanpassing te verwijderen voor eenheids variantie  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformeert functies door elke functie te schalen op basis van het minimum en maximum van die kolom  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Elke functie schalen met de Maxi maal absolute waarde |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Deze schaal functies op basis van hun quantile bereik |
| [ASSISTENT](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Lineaire dimensionaliteit door de gegevens uit de enkelvoudige waarde te desamen stellen om deze te projecteren in een gereduceerde ruimte |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Deze transformator voert lineaire dimensionaliteit uit met behulp van een afgekapte enkelvouds waarde (SVD). In tegens telling tot PCA worden met deze Estimator de gegevens niet gecentreerd voordat de enkelvoudige waarde wordt uitgevouwen. Dit betekent dat deze kan werken met scipy. sparse-matrices |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Elk voor beeld (dat wil zeggen, elke rij van de gegevens matrix) met ten minste één niet-nul onderdeel, wordt afzonderlijk van andere steek proeven opnieuw geschaald, zodat de norm (L1 of L2) gelijk is aan 1 |

### <a name="advanced-preprocessing--featurization"></a>Geavanceerde voor verwerking van & parametrisatie

Er zijn ook aanvullende geavanceerde preverwerkings-en parametrisatie beschikbaar, zoals gegevens Guardrails, code ring en trans formaties. Meer [informatie over wat parametrisatie is inbegrepen](how-to-use-automated-ml-for-ml-models.md#featurization). Schakel deze instelling in met:

+ Azure Machine Learning studio: **automatische parametrisatie** inschakelen in de sectie **aanvullende configuratie weer geven** [met de volgende stappen](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

+ Python-SDK: `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` opgeven voor de [ `AutoMLConfig` klasse](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

<a name="parity"></a>

## <a name="the-studio-vs-sdk"></a>De Studio VS SDK

Meer informatie over de pariteit en verschillen tussen de functies voor automatische Beschik baarheid op hoog niveau die beschikbaar zijn via de python-SDK en de studio in Azure Machine Learning. 

### <a name="experiment-settings"></a>Instellingen voor experimenteren 

Met de volgende instellingen kunt u uw geautomatiseerde ML experiment configureren. 

| |De python-SDK|De web-ervaring van Studio|
----|:----:|:----:
Gegevens in Train/validatie sets splitsen| ✓|✓
Ondersteunt ML-taken: classificatie, regressie en prognose| ✓| ✓
Optimaliseert op basis van primaire metriek| ✓| ✓
Ondersteunt AML Compute als Compute-doel | ✓|✓
Prognose horizon, target lags & Rolling venster configureren|✓|✓
Afsluit criteria instellen |✓|✓ 
Gelijktijdige herhalingen instellen| ✓|✓
Kolommen neerzetten| ✓|✓
Blok algoritmen|✓|✓
Kruis validatie |✓|✓
Ondersteunt training op Azure Databricks clusters| ✓|
Functie namen van ontworpen weer geven|✓|
Parametrisatie-samen vatting| ✓|
Parametrisatie voor feest dagen|✓|
Uitgebreide niveaus van het logboek bestand| ✓|

### <a name="model-settings"></a>Model instellingen

Deze instellingen kunnen worden toegepast op het beste model als resultaat van uw geautomatiseerde ML-experiment.

| |De python-SDK|De web-ervaring van Studio|
|----|:----:|:----:|
|Beste model registratie, implementatie, uitleg| ✓|✓|
|Stem ensemble inschakelen & stack-ensembles modellen| ✓|✓|
|Beste model weer geven op basis van niet-primaire metriek|✓||
|Compatibiliteit met ONNX-modellen in-of uitschakelen|✓||
|Het model testen | ✓| |

### <a name="run-control-settings"></a>Instellingen voor beheer uitvoeren

Met deze instellingen kunt u de uitvoeringen van uw experiment en de onderliggende uitvoeringen controleren en beheren. 

| |De python-SDK|De web-ervaring van Studio|
|----|:----:|:----:|
|Samenvattings tabel uitvoeren| ✓|✓|
|Uitvoeringen annuleren & onderliggende uitvoeringen| ✓|✓|
|Guardrails ophalen| ✓|✓|
|Uitvoeringen onderbreken & hervatten| ✓| |

## <a name="ensemble-models"></a><a name="ensemble"></a>Ensemble-modellen

Automatische machine learning ondersteunt ensemble-modellen die standaard zijn ingeschakeld. Ensemble Learning verbetert de machine learning resultaten en voorspellende prestaties door het combi neren van meerdere modellen, in tegens telling tot het gebruik van één model. De ensembles herhalingen worden weer gegeven als de laatste herhalingen van de uitvoering. Automatische machine learning maakt gebruik van zowel stem-als gestapelde ensemble-methoden voor het combi neren van modellen:

* **Stem**: voor speld op basis van het gewogen gemiddelde van voorspelde klasse-kansen (voor classificatie taken) of voorspelde regressie doelen (voor regressie taken).
* **Stapelen**: stacking combineert heterogene-modellen en treinen een meta model op basis van de uitvoer van de afzonderlijke modellen. De huidige standaard-META modellen zijn LogisticRegression voor classificatie taken en ElasticNet voor regressie-en prognose taken.

Het [selectie algoritme van Caruana ensemble](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) met gesorteerde ensemble-initialisatie wordt gebruikt om te bepalen welke modellen er moeten worden gebruikt in de ensemble. Op hoog niveau initialiseert dit algoritme de ensemble met Maxi maal vijf modellen met de beste afzonderlijke scores en verifieert dat deze modellen binnen 5% drempelwaarde van de beste score zijn om een slechte initiële ensemble te voor komen. Vervolgens wordt voor elke ensemble-iteratie een nieuw model toegevoegd aan de bestaande ensemble en wordt de resulterende score berekend. Als een nieuw model de bestaande ensemble-score heeft verbeterd, wordt de ensemble bijgewerkt met het nieuwe model.

Zie de [procedure](how-to-configure-auto-train.md#ensemble) voor het wijzigen van standaard-ensemble-instellingen in automatische machine learning.

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Met Azure Machine Learning kunt u automatische ML gebruiken om een python-model samen te stellen en dit te converteren naar de ONNX-indeling. Zodra de modellen de ONNX-indeling hebben, kunnen ze op verschillende platforms en apparaten worden uitgevoerd. Meer informatie over het [versnellen van ml-modellen met ONNX](concept-onnx.md).

Lees hoe u kunt converteren naar ONNX-indeling [in dit Jupyter notebook-voor beeld](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Meer informatie over de [algoritmen die worden ondersteund in ONNX](how-to-configure-auto-train.md#select-your-experiment-type).

De ONNX-runtime biedt ook ondersteuning voor C#, zodat u het model dat automatisch in uw C#-apps is gebouwd, kunt gebruiken zonder dat u hoeft te coderen of een van de netwerk latenties die REST-eind punten introduceren. Meer informatie over [het AFONNXen van modellen met de ONNX runtime C# API](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md). 



## <a name="next-steps"></a>Volgende stappen

Bekijk voor beelden en leer hoe u modellen bouwt met geautomatiseerde machine learning:

+ Volg de [zelf studie: automatisch een regressie model trainen met Azure machine learning](tutorial-auto-train-models.md)

+ De instellingen voor automatische training-experiment configureren:
  + In Azure Machine Learning Studio [gaat u](how-to-use-automated-ml-for-ml-models.md)als volgt te werk.
  + Gebruik de python-SDK om de [volgende stappen uit te voeren](how-to-configure-auto-train.md).

+ Meer informatie over het automatisch trainen van Time Series-gegevens met behulp van [deze stappen](how-to-auto-train-forecast.md).

+ [Jupyter notebook voor beelden uitproberen voor automatische machine learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

* Automatische ML is ook beschikbaar in andere micro soft-oplossingen, zoals [ml.net](https://docs.microsoft.com/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-automated) en [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
