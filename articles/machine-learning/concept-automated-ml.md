---
title: Wat is geautomatiseerd ML / AutoML
titleSuffix: Azure Machine Learning
description: Ontdek hoe Azure Machine Learning automatisch een algoritme voor u kan kiezen en er een model van kan genereren om u tijd te besparen met behulp van de parameters en criteria die u opgeeft om het beste algoritme voor uw model te selecteren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 04/22/2020
ms.openlocfilehash: f592a7f5a4af38988bcf433f0adc89d9be7579cb
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082006"
---
# <a name="what-is-automated-machine-learning-automl"></a>Wat is geautomatiseerde machine learning (AutoML)?

Geautomatiseerde machine learning, ook wel aangeduid als geautomatiseerde ML of AutoML, is het proces van het automatiseren van de tijdrovende, iteratieve taken van machine learning model ontwikkeling. Het stelt gegevenswetenschappers, analisten en ontwikkelaars in staat om ML-modellen te bouwen met een hoge schaal, efficiëntie en productiviteit, terwijl de modelkwaliteit behouden blijft. Automated ML is gebaseerd op een doorbraak van onze [Microsoft Research-divisie.](https://arxiv.org/abs/1705.05355)

Traditionele machine learning model ontwikkeling is resource-intensief, die aanzienlijke domeinkennis en tijd om te produceren en te vergelijken tientallen modellen. Met geautomatiseerde machine learning versnelt u de tijd die nodig is om ml-modellen met veel gemak en efficiëntie op productieniveau te krijgen.

## <a name="when-to-use-automl-classify-regression--forecast"></a>Wanneer automl te gebruiken: classificeren, regressie, & voorspelling

Gebruik geautomatiseerde ML wanneer u wilt dat Azure Machine Learning een model voor u traint en afstemt met behulp van de doelstatistiek die u opgeeft. Automated ML democratiseert het machine learning-modelontwikkelingsproces en stelt gebruikers, ongeacht hun expertise op het gebied van data science, in staat om een end-to-end machine learning-pijplijn voor elk probleem te identificeren.

Gegevenswetenschappers, analisten en ontwikkelaars in verschillende bedrijfstakken kunnen geautomatiseerde ML gebruiken om:
+ Mioplossingen implementeren zonder uitgebreide programmeerkennis
+ Bespaar tijd en middelen
+ Gebruik maken van best practices voor data science
+ Zorg voor een wendbaar probleemoplossend probleem

### <a name="classification"></a>Classificatie

Classificatie is een veelvoorkomende machine learning-taak. Classificatie is een soort leeronder toezicht waarbij modellen leren met behulp van trainingsgegevens en die leerlessen toepassen op nieuwe gegevens. Azure Machine Learning biedt prestatieverbeteringen speciaal voor deze taken, zoals diepe neurale netwerktekst featurizers voor classificatie. Meer informatie over [featurisatie-opties](how-to-use-automated-ml-for-ml-models.md#featurization). 

Het belangrijkste doel van classificatiemodellen is om te voorspellen in welke categorieën nieuwe gegevens zullen vallen op basis van learnings uit de trainingsgegevens. Voorbeelden van algemene classificatiezijn fraudedetectie, handschriftherkenning en objectdetectie.  Meer informatie en zie een voorbeeld van [classificatie met geautomatiseerde machine learning](tutorial-train-models-with-aml.md).

Zie voorbeelden van classificatie en geautomatiseerde machine learning in deze Python-notitieblokken: [Fraudedetectie,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) [Marketingvoorspelling](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)en [gegevensclassificatie van nieuwsgroepen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)

### <a name="regression"></a>Regressie
Net als bij classificatie zijn regressietaken ook een veelvoorkomende begeleide leertaak. Azure Machine Learning biedt [prestatieverbeteringen speciaal voor deze taken.](how-to-use-automated-ml-for-ml-models.md#featurization)

Anders dan classificatie waarbij voorspelde uitvoerwaarden categorisch zijn, voorspellen regressiemodellen numerieke uitvoerwaarden op basis van onafhankelijke voorspellers. In regressie is het doel om de relatie tussen die onafhankelijke voorspellervariabelen te helpen vaststellen door te schatten hoe de ene variabele de andere beïnvloedt. Bijvoorbeeld, auto prijs op basis van functies zoals, gas kilometers, veiligheid rating, enz. Meer informatie en zie een voorbeeld van [regressie met geautomatiseerde machine learning](tutorial-auto-train-models.md).

Zie voorbeelden van regressie en geautomatiseerde machine learning voor voorspellingen in deze Python-notebooks: [CPU-prestatievoorspelling](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb), 

### <a name="time-series-forecasting"></a>Tijdreeksprognoses

Bouwprognoses zijn een integraal onderdeel van elk bedrijf, of het nu gaat om omzet, voorraad, verkoop of vraag van klanten. U geautomatiseerde ML gebruiken om technieken en benaderingen te combineren en een aanbevolen tijdreeksprognose van hoge kwaliteit te krijgen. Lees meer met deze how-to: [automated machine learning voor tijdreeksen voorspelling](how-to-auto-train-forecast.md). 

Een geautomatiseerd tijdserieexperiment wordt behandeld als een multivariate regressieprobleem. Waarden uit het verleden worden "gedraaid" om extra dimensies te worden voor de regressor samen met andere voorspellers. Deze aanpak, in tegenstelling tot klassieke tijdreeksen methoden, heeft een voordeel van het natuurlijk opnemen van meerdere contextuele variabelen en hun relatie met elkaar tijdens de training. Geautomatiseerde ML leert een enkel, maar vaak intern vertakt model voor alle items in de dataset en voorspelling horizonten. Meer gegevens zijn dus beschikbaar om modelparameters te schatten en generalisatie naar onzichtbare reeksen wordt mogelijk.

Geavanceerde prognoseconfiguratie omvat:
* vakantiedetectie en featurization
* tijdreeksen en DNN-leerlingen (Auto-ARIMA, Prophet, ForecastTCN)
* veel modellen ondersteunen door het groeperen
* crossvalidatie van rolling-origine
* configureerbare vertragingen
* functies voor rollend vensteraggregaten


Zie voorbeelden van regressie en geautomatiseerde machine learning voor voorspellingen in deze Python-notitieblokken: [Verkoopprognoses,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb) [Vraagprognose](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)en [Drankproductieprognose](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

## <a name="how-automl-works"></a>Hoe AutoML werkt

Tijdens de training maakt Azure Machine Learning een aantal pijplijnen parallel die verschillende algoritmen en parameters voor u proberen. De service wordt ondersteund door ML-algoritmen in combinatie met functieselecties, waarbij elke iteratie een model met een trainingsscore produceert. Hoe hoger de score, hoe beter het model wordt beschouwd als "fit" uw gegevens.  Het zal stoppen zodra het raakt de exit criteria gedefinieerd in het experiment. 

Met **Azure Machine Learning**u uw geautomatiseerde ML-trainingsexperimenten met de volgende stappen ontwerpen en uitvoeren:

1. Identificeer het op te lossen **ML-probleem:** classificatie, prognoses of regressie

1. **Kies of u de Python SDK of de studiowebervaring wilt gebruiken:** Meer informatie over de pariteit tussen de [Python SDK en de studiowebervaring.](#parity)

   * Probeer de webervaring van Azure Machine Learning studio op voor beperkte of geen code-ervaring[https://ml.azure.com](https://ml.azure.com/)  
   * Bekijk de Azure Machine [Learning Python SDK](how-to-configure-auto-train.md) voor Python-ontwikkelaars 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]  
    
1. **De bron en de indeling van de gelabelde trainingsgegevens opgeven:** Numpy-arrays of Panda's-gegevensframe

1. **Configureer het rekendoel voor modeltraining,** zoals uw [lokale computer, Azure Machine Learning Computes, externe VM's of Azure Databricks.](how-to-set-up-training-targets.md)  Meer informatie over geautomatiseerde training [op een externe bron.](how-to-auto-train-remote.md)

1. **Configureer de geautomatiseerde machine learning-parameters** die bepalen hoeveel iteraties over verschillende modellen, hyperparameter-instellingen, geavanceerde voorbewerking/featurisatie en welke statistieken u moet bekijken bij het bepalen van het beste model.  
1. **Stuur de trainingsloop in.**

1. **De resultaten bekijken** 

Het volgende diagram illustreert dit proces. 
![Geautomatiseerde machine learning](./media/concept-automated-ml/automl-concept-diagram2.png)


U ook de geregistreerde runinformatie bekijken, die [statistieken bevat](how-to-understand-automated-ml.md) die tijdens de run zijn verzameld. De trainingsrun produceert een Python`.pkl` serialized object (bestand) dat het model en de voorbewerking van gegevens bevat.

Terwijl modelbuilding geautomatiseerd is, u ook [leren hoe belangrijk of relevant functies zijn](how-to-configure-auto-train.md#explain) voor de gegenereerde modellen.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Preprocessing

In elk geautomatiseerd machine learning-experiment worden uw gegevens vooraf verwerkt met behulp van de standaardmethoden en optioneel via geavanceerde voorbewerking.

> [!NOTE]
> Geautomatiseerde stappen voor machine learning voor verwerking (functienormalisatie, omgaan met ontbrekende gegevens, converteren van tekst naar numeriek, enz.) worden onderdeel van het onderliggende model. Bij het gebruik van het model voor voorspellingen worden de tijdens de training toegepaste preverwerkingsstappen automatisch toegepast op uw invoergegevens.

### <a name="automatic-preprocessing-standard"></a>Automatische voorbewerking (standaard)

In elk geautomatiseerd machine learning-experiment worden uw gegevens automatisch geschaald of genormaliseerd om algoritmen te helpen goed te presteren.  Tijdens de modeltraining wordt op elk model een van de volgende schalings- of normalisatietechnieken toegepast.

|Normalisatie&nbsp;&&nbsp;schalen| Beschrijving |
| ------------- | ------------- |
| [StandardScaleWrapper (StandardScaleWrapper)](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Functies standaardiseren door het gemiddelde te verwijderen en te schalen naar variantie van eenheid  |
| [MinMaxScalar MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformeert functies door elke functie te schalen naar het minimum en maximum van die kolom  |
| [MaxAbsScaler MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Schaal elke functie op de maximale absolute waarde |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Deze Scaler beschikt over hun quantile range |
| [Pca](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Lineaire dimensionaliteitsreductie met enkelvoudwaardedecompositie van de gegevens om deze naar een lagere dimensionale ruimte te projecteren |
| [AfgekaptSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Deze transformator voert lineaire dimensionaliteitsreductie uit door middel van afgekapte enkelvoudwaardedecompositie (SVD). In tegenstelling tot PCA, deze schatter niet centreren van de gegevens voor het berekenen van de bijzondere waarde ontbinding, wat betekent dat het kan werken met scipy.sparse matrices efficiënt |
| [SchaarsNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Elk monster (dat wil zeggen elke rij van de gegevensmatrix) met ten minste één niet-nulcomponent wordt onafhankelijk van andere monsters opnieuw geschaald, zodat de norm (l1 of l2) gelijk is aan één |

### <a name="advanced-preprocessing--featurization"></a>Geavanceerde voorbewerking & featurization

Aanvullende geavanceerde voorbewerking en featurization zijn ook beschikbaar, zoals gegevensvangrails, codering en transformaties. [Meer informatie over wat featurization is opgenomen.](how-to-use-automated-ml-for-ml-models.md#featurization) Schakel deze instelling in met:

+ Azure Machine Learning-studio: schakel **automatische prestatiebeheer** mogelijk in de sectie **Extra configuratie weergeven** [met deze stappen](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

+ Python SDK: `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` opgeven voor de [ `AutoMLConfig` klasse](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

<a name="parity"></a>

## <a name="the-studio-vs-sdk"></a>De studio vs SDK

Meer informatie over de pariteit en verschillen tussen de geautomatiseerde ML-mogelijkheden op hoog niveau die beschikbaar zijn via de Python SDK en de studio in Azure Machine Learning. 

### <a name="experiment-settings"></a>Experiment-instellingen 

Met de volgende instellingen u uw geautomatiseerde ML-experiment configureren. 

| |De Python SDK|De studio web ervaring|
----|:----:|:----:
Gegevens splitsen in trein-/validatiesets| ✓|✓
Ondersteunt ML-taken: classificatie, regressie en prognoses| ✓| ✓
Optimaliseert op basis van primaire statistiek| ✓| ✓
Ondersteunt AML compute als compute target | ✓|✓
Prognosehorizon configureren, doelvertragingen & rollend venster|✓|✓
Exitcriteria instellen |✓|✓ 
Gelijktijdige iteraties instellen| ✓|✓
Kolommen neerzetten| ✓|✓
Algoritmen blokkeren|✓|✓
Kruisvalidatie |✓|✓
Ondersteunt training op Azure Databricks-clusters| ✓|
Engineered feature names weergeven|✓|
Featurization samenvatting| ✓|
Featurization voor vakantie|✓|
Verbosity-niveaus van logboekbestanden| ✓|

### <a name="model-settings"></a>Modelinstellingen

Deze instellingen kunnen worden toegepast op het beste model als gevolg van uw geautomatiseerde ML-experiment.

| |De Python SDK|De studio web ervaring|
|----|:----:|:----:|
|Beste modelregistratie, implementatie, uitlegbaarheid| ✓|✓|
|Stemensemble inschakelen & ensemblemodellen stapelen| ✓|✓|
|Het beste model weergeven op basis van niet-primaire statistiek|✓||
|Compatibiliteit met ONNX-model inschakelen/uitschakelen|✓||
|Het model testen | ✓| |

### <a name="run-control-settings"></a>Besturingselementinstellingen uitvoeren

Met deze instellingen u uw experiment uitvoeren en het onderliggende experiment controleren en beheren. 

| |De Python SDK|De studio web ervaring|
|----|:----:|:----:|
|Overzichtstabel uitvoeren| ✓|✓|
|Runs annuleren & onderliggende uitvoeringen| ✓|✓|
|Krijg vangrails| ✓|✓|
|& hervatten wordt onderbroken| ✓| |

## <a name="ensemble-models"></a><a name="ensemble"></a>Ensemblemodellen

Geautomatiseerde machine learning ondersteunt ensemblemodellen, die standaard zijn ingeschakeld. Ensemble learning verbetert machine learning-resultaten en voorspellende prestaties door meerdere modellen te combineren in plaats van afzonderlijke modellen te gebruiken. De ensemble iteraties verschijnen als de laatste iteraties van uw run. Geautomatiseerde machine learning maakt gebruik van zowel stem- als stapelensemblemethoden voor het combineren van modellen:

* **Stemmen**: voorspelt op basis van het gewogen gemiddelde van voorspelde klassewaarschijnlijkheden (voor classificatietaken) of voorspelde regressiedoelen (voor regressietaken).
* **Stapelen**: stapelen combineert heterogene modellen en traint een metamodel op basis van de output van de afzonderlijke modellen. De huidige standaardmetamodellen zijn LogisticRegression voor classificatietaken en ElasticNet voor regressie/prognosetaken.

Het [Caruana ensemble selectie algoritme](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) met gesorteerde ensemble initialisatie wordt gebruikt om te beslissen welke modellen te gebruiken binnen het ensemble. Op een hoog niveau initialiseert dit algoritme het ensemble met maximaal vijf modellen met de beste individuele scores en controleert of deze modellen binnen de drempel van 5% van de beste score liggen om een slecht initieel ensemble te voorkomen. Vervolgens wordt voor elke ensembleiteratie een nieuw model toegevoegd aan het bestaande ensemble en wordt de resulterende score berekend. Als een nieuw model de bestaande ensemblescore heeft verbeterd, wordt het ensemble bijgewerkt met het nieuwe model.

Bekijk de [how-to](how-to-configure-auto-train.md#ensemble) voor het wijzigen van standaardinstellingen voor ensemble's in geautomatiseerde machine learning.

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Met Azure Machine Learning u geautomatiseerde ML gebruiken om een Python-model te bouwen en te laten converteren naar de ONNX-indeling. Zodra de modellen in het ONNX-formaat zijn, kunnen ze op verschillende platforms en apparaten worden uitgevoerd. Meer informatie over [het versnellen van ML-modellen met ONNX](concept-onnx.md).

Zie hoe u converteren naar ONNX-indeling [in dit voorbeeld van Jupyter-notitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Ontdek welke [algoritmen worden ondersteund in ONNX](how-to-configure-auto-train.md#select-your-experiment-type).

De ONNX-runtime ondersteunt ook C#, zodat u het model dat automatisch in uw C#-apps is gebouwd, gebruiken zonder dat u opnieuw hoeft te coderen of een van de netwerklatencies die REST-eindpunten introduceren. Meer informatie over [het inferencen van ONNX-modellen met de ONNX runtime C#API](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md). 



## <a name="next-steps"></a>Volgende stappen

Bekijk voorbeelden en leer hoe u modellen bouwen met behulp van geautomatiseerde machine learning:

+ Volg de [zelfstudie: train automatisch een regressiemodel met Azure Machine Learning](tutorial-auto-train-models.md)

+ De instellingen voor automatisch trainingsexperiment configureren:
  + Gebruik deze stappen [in](how-to-use-automated-ml-for-ml-models.md)Azure Machine Learning-studio.
  + Gebruik bij de Python SDK [deze stappen.](how-to-configure-auto-train.md)

+ Lees hoe u automatisch trainen met behulp van tijdreeksgegevens, [gebruikt u deze stappen](how-to-auto-train-forecast.md).

+ Probeer [Jupyter Notebook-voorbeelden uit voor geautomatiseerde machine learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

* Geautomatiseerde ML is ook beschikbaar in andere Microsoft-oplossingen [zoals, ML.NET,](https://docs.microsoft.com/dotnet/machine-learning/automl-overview) [HDInsight,](../hdinsight/spark/apache-spark-run-machine-learning-automl.md) [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated) en [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
