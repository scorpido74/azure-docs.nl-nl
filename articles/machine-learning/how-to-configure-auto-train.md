---
title: Geautomatiseerde ML-experimenten maken
titleSuffix: Azure Machine Learning
description: Geautomatiseerde machine learning kiest een algoritme voor u en genereert een model dat klaar is voor implementatie. Meer informatie over de opties die u gebruiken om geautomatiseerde machine learning-experimenten te configureren.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 03e1d4aa74d2f71ab2f32ac55f4ad3d46f672f5c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618539"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Geautomatiseerde ML-experimenten configureren in Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze handleiding vindt u informatie over het definiëren van verschillende configuratie-instellingen van uw geautomatiseerde machine learning-experimenten met de [Azure Machine Learning SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) Automated machine learning kiest een algoritme en hyperparameters voor u en genereert een model dat klaar is voor implementatie. Er zijn verschillende opties die u gebruiken om geautomatiseerde machine learning-experimenten te configureren.

Zie [Zelfstudie: Train een classificatiemodel met geautomatiseerde machine learning](tutorial-auto-train-models.md) of [Train-modellen met geautomatiseerde machine learning in de cloud](how-to-auto-train-remote.md)om voorbeelden van een geautomatiseerde machine learning-experimenten weer te geven.

Configuratieopties beschikbaar in geautomatiseerde machine learning:

* Selecteer het experimenttype: Classificatie, Regressie of Tijdreeksprognoses
* Gegevensbron, indelingen en ophalen van gegevens
* Kies uw rekendoel: lokaal of extern
* Geautomatiseerde machine learning-experimentinstellingen
* Een geautomatiseerd machine learning-experiment uitvoeren
* Modelstatistieken verkennen
* Model registreren en implementeren

Als u de voorkeur geeft aan een geen-code-ervaring, u ook [uw geautomatiseerde machine learning-experimenten maken in Azure Machine Learning-studio.](how-to-use-automated-ml-for-ml-models.md)

## <a name="select-your-experiment-type"></a>Het type experimenten selecteren

Voordat u begint met uw experiment, moet u bepalen wat voor soort machine learning probleem dat u oplost. Geautomatiseerde machine learning ondersteunt taaktypen classificatie, regressie en prognoses. Meer informatie over [taaktypen](how-to-define-task-type.md).

Geautomatiseerde machine learning ondersteunt de volgende algoritmen tijdens het automatiserings- en tuningproces. Als gebruiker hoeft u het algoritme niet op te geven.

Classificatie | Regressie | Tijdreeksvoorspelling
|-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Lichte GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lichte GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lichte GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Verloopboosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Verloopboosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Verloopboosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Beslissingsboom](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Beslissingsboom](https://scikit-learn.org/stable/modules/tree.html#regression)|[Beslissingsboom](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Dichtstbijzijnde buren](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Dichtstbijzijnde buren](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Dichtstbijzijnde buren](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineaire SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Ondersteuningsvectorclassificatie (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stochastische gradiënt (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stochastische gradiënt (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extreem gerandomiseerde bomen](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extreem gerandomiseerde bomen](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extreem gerandomiseerde bomen](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN Classifier](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN Lineaire classificatie](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineaire regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Lineaire regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Snelle lineaire regressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Stochastische gradiënt (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Online verloopafdaling regressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Profeet](https://facebook.github.io/prophet/docs/quick_start.html)
|[Gemiddelde Perceptron Classifier](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||PrognoseTCN
|[Lineaire SVM-classificatie](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)||

Gebruik `task` de parameter `AutoMLConfig` in de constructor om het experimenttype op te geven.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Gegevensbron en -indeling

Geautomatiseerde machine learning ondersteunt gegevens die zich op uw lokale bureaublad of in de cloud bevinden, zoals Azure Blob Storage. De gegevens kunnen worden uitgelezen in een **Pandas DataFrame** of een **Azure Machine Learning TabularDataset.**  [Meer informatie over gegevenssets](how-to-create-register-datasets.md).

Vereisten voor opleidingsgegevens:
- De gegevens moeten in tabelvorm zijn.
- De te voorspellen waarde, de doelkolom, moet in de gegevens staan.

In de volgende codevoorbeelden wordt uitgelegd hoe u de gegevens in deze indelingen opslaan.

* Tabeltabelgegevensset

  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Panda's dataframe

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Gegevens ophalen voor het uitvoeren van experiment op externe gegevens

Voor externe uitvoeringen moeten trainingsgegevens toegankelijk zijn via de externe gegevensberekening. De [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) klasse in de SDK stelt functionaliteit bloot aan:

* eenvoudig gegevens van statische bestanden of URL-bronnen naar uw werkruimte overbrengen
* uw gegevens beschikbaar maken voor trainingsscripts wanneer u op cloudcomputeresources wordt uitgevoerd

Zie de [how-to](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) voor een `Dataset` voorbeeld van het gebruik van de klasse om gegevens te monteren op uw compute target.

## <a name="train-and-validation-data"></a>Trein- en validatiegegevens

U afzonderlijke trein- en `AutoMLConfig` validatiesets rechtstreeks in de constructor opgeven.

### <a name="k-folds-cross-validation"></a>Kruisvalidatie voor K-Folds

Gebruik `n_cross_validations` de instelling om het aantal kruisvalidaties op te geven. De trainingsgegevensset wordt willekeurig `n_cross_validations` opgesplitst in plooien van gelijke grootte. Tijdens elke cross validatie ronde, zal een van de plooien worden gebruikt voor validatie van het model getraind op de resterende plooien. Dit proces wordt `n_cross_validations` herhaald voor rondes totdat elke vouw eenmaal wordt gebruikt als validatieset. De gemiddelde scores `n_cross_validations` over alle rondes worden gerapporteerd en het bijbehorende model wordt omgeschoold op de hele trainingsgegevensset.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo Cross Validatie (Herhaalde willekeurige sub-sampling)

Met `validation_size` deze neigen het percentage van de trainingsgegevensset `n_cross_validations` dat moet worden gebruikt voor validatie en u het aantal kruisvalidaties opgeven. Tijdens elke cross validatieronde wordt `validation_size` willekeurig een subset van grootte geselecteerd voor validatie van het model dat is getraind op de resterende gegevens. Ten slotte worden de `n_cross_validations` gemiddelde scores over alle rondes gerapporteerd en wordt het bijbehorende model omgeschoold op de hele trainingsgegevensset. Monte Carlo wordt niet ondersteund voor het voorspellen van tijdreeksen.

### <a name="custom-validation-dataset"></a>Aangepaste validatiegegevensset

Gebruik aangepaste validatiegegevensset als willekeurige splitsing niet acceptabel is, meestal tijdreeksgegevens of onevenwichtige gegevens. U uw eigen validatiegegevensset opgeven. Het model wordt geëvalueerd aan de hand van de validatiegegevensset die is opgegeven in plaats van de willekeurige gegevensset.

## <a name="compute-to-run-experiment"></a>Compute en uitvoering van het experiment instellen

Bepaal vervolgens waar het model zal worden opgeleid. Een geautomatiseerd machine learning-trainingsexperiment kan worden uitgevoerd op de volgende rekenopties:
* Uw lokale machine, zoals een lokale desktop of laptop - Over het algemeen wanneer u een kleine gegevensset hebt en u nog in de verkenningsfase bent.
* Een externe machine in de cloud – [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) is een beheerde service waarmee machine learning-modellen kunnen worden getraind op clusters van virtuele Azure-machines.

  Zie deze [GitHub-site](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) voor voorbeelden van notitieblokken met lokale en externe rekendoelen.

* Een Azure Databricks-cluster in uw Azure-abonnement. Meer informatie vindt u hier - [Azure Databricks-cluster instellen voor geautomatiseerde ML](how-to-configure-environment.md#azure-databricks)

  Zie deze [GitHub-site](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) voor voorbeelden van notitieblokken met Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>De experimentinstellingen configureren

Er zijn verschillende opties die u gebruiken om uw geautomatiseerde machine learning-experiment te configureren. Deze parameters worden ingesteld door `AutoMLConfig` een object te instantiëring. Zie de [klasse AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) voor een volledige lijst met parameters.

Voorbeelden zijn:

1. Classificatie-experiment met AUC gewogen als primaire statistiek met tijdstime-outminuten ingesteld op 30 minuten en 2 kruisvalidatievouwen.

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blacklist_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
2. Hieronder vindt u een voorbeeld van een regressieexperiment dat na 60 minuten eindigt met vijf validatiekruisplooien.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      whitelist_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```

De drie `task` verschillende parameterwaarden (het `forecasting`derde taaktype is en `regression` gebruikt een vergelijkbare algoritmegroep als taken) bepalen de lijst met modellen die moeten worden toegepast. Gebruik `whitelist` de `blacklist` or-parameters om iteraties met de beschikbare modellen verder te wijzigen om deze op te nemen of uit te sluiten. De lijst met ondersteunde modellen is te vinden op [SupportedModels Class](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) voor ([Classificatie,](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification) [Forecasting](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)en [Regressie](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)).

Om time-outfouten voor experimenten te voorkomen, moet `experiment_timeout_minutes` de validatieservice van Automated ML worden ingesteld op een minimum van 15 minuten of 60 minuten als uw rij per kolomgrootte meer dan 10 miljoen bedraagt.

### <a name="primary-metric"></a>Primaire statistiek
De primaire statistiek bepaalt de statistiek die moet worden gebruikt tijdens modeltraining voor optimalisatie. De beschikbare statistieken die u selecteren, worden bepaald door het taaktype dat u kiest en in de volgende tabel worden geldige primaire statistieken voor elk taaktype weergegeven.

|Classificatie | Regressie | Tijdreeksvoorspelling
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Meer informatie over de specifieke definities van deze statistieken in [Geautomatiseerde machine learning-resultaten begrijpen](how-to-understand-automated-ml.md).

### <a name="data-featurization"></a>Data featurization

In elk geautomatiseerd machine learning-experiment worden uw gegevens [automatisch geschaald en genormaliseerd](concept-automated-ml.md#preprocess) om *bepaalde* algoritmen te helpen die gevoelig zijn voor functies die zich op verschillende schalen bevinden.  U echter ook extra prestatieverbeteringen inschakelen, zoals toerekening ontbrekende waarden, codering en transformaties. [Meer informatie over wat featurization is opgenomen.](how-to-use-automated-ml-for-ml-models.md#featurization)

Wanneer u uw experimenten configureert, `featurization`u de geavanceerde instelling inschakelen. In de volgende tabel worden de geaccepteerde instellingen voor featurisatie in de [ `AutoMLConfig` klasse](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)weergegeven.

|Featurisatieconfiguratie | Beschrijving |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Hiermee geeft u aan dat de aangepaste prestatiestap moet worden gebruikt. [Meer informatie over het aanpassen van featurization.](how-to-configure-auto-train.md#customize-feature-engineering)|
|`"featurization": 'off'`| Geeft aan dat featurisatiestap niet automatisch moet worden uitgevoerd.|
|`"featurization": 'auto'`| Geeft aan dat als onderdeel van [voorbewerking, gegevensvangrails en featurisatiestappen](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options) automatisch worden uitgevoerd.|

> [!NOTE]
> Geautomatiseerde machine learning featurization stappen (functie normalisatie, omgaan met ontbrekende gegevens, het omzetten van tekst naar numerieke, enz.) deel uitmaken van het onderliggende model. Bij het gebruik van het model voor voorspellingen worden dezelfde featurisatiestappen die tijdens de training worden toegepast, automatisch toegepast op uw invoergegevens.

### <a name="time-series-forecasting"></a>Tijdreeksvoorspelling
De taak `forecasting` tijdreeksen vereist extra parameters in het configuratieobject:

1. `time_column_name`: Vereiste parameter die de naam van de kolom in uw trainingsgegevens definieert die een geldige tijdreeks bevatten.
1. `max_horizon`: Hiermee bepaalt u de tijdsduur die u wilt voorspellen op basis van de periodiciteit van de trainingsgegevens. Als u bijvoorbeeld trainingsgegevens met dagelijkse tijdkorrels hebt, bepaalt u hoe ver in dagen u het model wilt trainen.
1. `grain_column_names`: Hiermee definieert u de naam van kolommen die afzonderlijke tijdreeksgegevens in uw trainingsgegevens bevatten. Als u bijvoorbeeld de verkoop van een bepaald merk per winkel voorspelt, definieert u winkel- en merkkolommen als uw korrelkolommen. Voor elke korrel/groepering worden afzonderlijke tijdreeksen en prognoses gemaakt. 

Zie het [voorbeeldnotitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)voor voorbeelden van de onderstaande instellingen .

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
}

automl_config = AutoMLConfig(task = 'forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=20,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble-configuration"></a><a name="ensemble"></a>Ensembleconfiguratie

Ensemblemodellen zijn standaard ingeschakeld en worden weergegeven als de laatste uitvoeringiteraties in een geautomatiseerde machine learning-uitvoering. Momenteel ondersteund ensemble methoden zijn stemmen en stapelen. Stemmen wordt geïmplementeerd als soft-voting met behulp van gewogen gemiddelden, en de stapeling implementatie is met behulp van een twee laag implementatie, waar de eerste laag heeft dezelfde modellen als de stemming ensemble, en de tweede laag model wordt gebruikt om de optimale combinatie van de modellen uit de eerste laag te vinden. Als u ONNX-modellen gebruikt **of** modelexplainability hebt ingeschakeld, wordt stapelen uitgeschakeld en wordt alleen stemmen gebruikt.

Er zijn meerdere standaardargumenten die `kwargs` kunnen `AutoMLConfig` worden opgegeven als in een object om het standaardgedrag van het stackensemble te wijzigen.

* `stack_meta_learner_type`: de metaleerling is een model dat getraind is op de output van de individuele heterogene modellen. Standaardmeta-leerlingen zijn `LogisticRegression` voor classificatietaken `LogisticRegressionCV` (of als cross-validatie `ElasticNet` is ingeschakeld) en `ElasticNetCV` voor regressie/prognosetaken (of als cross-validatie is ingeschakeld). Deze parameter kan een van `LogisticRegression`de `LogisticRegressionCV` `LightGBMClassifier`volgende `ElasticNet` `ElasticNetCV`tekenreeksen zijn: , , , , `LightGBMRegressor`, of `LinearRegression`.
* `stack_meta_learner_train_percentage`: geeft het aandeel van de trainingsset (bij het kiezen van trein en validatietype training) aan dat moet worden gereserveerd voor de opleiding van de metaleerling. Standaardwaarde `0.2`is .
* `stack_meta_learner_kwargs`: optionele parameters om door te geven aan de initialisator van de meta-leerling. Deze parameters en parametertypen weerspiegelen de parameters en parametertypen van de bijbehorende modelconstructor en worden doorgestuurd naar de modelconstructeur.

In de volgende code wordt een voorbeeld `AutoMLConfig` weergegeven van het opgeven van aangepast ensemblegedrag in een object.

```python
ensemble_settings = {
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

Ensembletraining is standaard ingeschakeld, maar kan worden uitgeschakeld `enable_voting_ensemble` `enable_stack_ensemble` met behulp van de en booleaanse parameters.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Experiment uitvoeren

Voor geautomatiseerde ML `Experiment` maakt u een object, `Workspace` een benoemd object in een object dat wordt gebruikt om experimenten uit te voeren.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Dien het experiment in om een model uit te voeren en te genereren. Geef `AutoMLConfig` de `submit` methode door om het model te genereren.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Afhankelijkheden worden eerst geïnstalleerd op een nieuwe machine.  Het kan tot 10 minuten duren voordat de uitvoer wordt weergegeven.
>Als `show_output` `True` u de uitvoer instelt, wordt de uitvoer op de console weergegeven.

### <a name="exit-criteria"></a>Exitcriteria
Er zijn een paar opties die u definiëren om uw experiment te beëindigen.
1. Geen criteria: Als u geen exitparameters definieert, wordt het experiment voortgezet totdat er geen verdere vooruitgang is geboekt op uw primaire statistiek.
1. Afsluiten na een bepaalde `experiment_timeout_minutes` tijd: met behulp in uw instellingen u bepalen hoe lang in minuten moet een experiment blijven in run.
1. Afsluiten nadat een score is `experiment_exit_score` bereikt: Met het gebruik wordt het experiment voltooid nadat een primaire metrische score is bereikt.

### <a name="explore-model-metrics"></a>Modelstatistieken verkennen

U uw trainingsresultaten bekijken in een widget of inline als u zich in een notitieblok bevindt. Zie [Modellen volgen en evalueren](how-to-track-experiments.md#view-run-details) voor meer details.

## <a name="understand-automated-ml-models"></a>Inzicht in geautomatiseerde ML-modellen

Elk model dat wordt geproduceerd met behulp van geautomatiseerde ML bevat de volgende stappen:
+ Geautomatiseerde functie-engineering (indien) `"featurization": 'auto'`
+ Schalen/normaliseren en algoritme met hyperparameterwaarden

We maken het transparant om deze informatie te krijgen van de fitted_model output van geautomatiseerde ML.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Geautomatiseerde functie-engineering

Zie de lijst met voorbewerking en `"featurization": 'auto'` [geautomatiseerde functie-engineering](concept-automated-ml.md#preprocess) die plaatsvindt wanneer .

Neem dit voorbeeld:
+ Er zijn vier invoerfuncties: A (Numeriek), B (numeriek), C (Numeriek), D (DateTime)
+ Numerieke functie C wordt verwijderd omdat het een ID-kolom is met alle unieke waarden
+ Numerieke kenmerken A en B hebben ontbrekende waarden en worden dus toegerekend door het gemiddelde
+ DateTime-functie D is in 11 verschillende engineered functies opgenomen

Gebruik deze 2 API's op de eerste stap van het ingebouwde model om meer te begrijpen.  Zie [dit voorbeeldnotitieblok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` retourneert een lijst met ontworpen functienamen.

  Gebruik:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Deze lijst bevat alle ontworpen functienamen.

  >[!Note]
  >Gebruik 'timeseriestransformer' voor task='forecasting', anders gebruik je 'datatransformer' voor 'regressie' of 'classificatie'-taak.

+ API 2: `get_featurization_summary()` retourneert featurization samenvatting voor alle invoerfuncties.

  Gebruik:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Gebruik 'timeseriestransformer' voor task='forecasting', anders gebruik je 'datatransformer' voor 'regressie' of 'classificatie'-taak.

  Uitvoer:
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   Waar:

   |Uitvoer|Definitie|
   |----|--------|
   |RawFeatureName|Invoerfunctie/kolomnaam uit de meegeleverde gegevensset.|
   |Typegedetecteerd|Gedetecteerd gegevenstype van de invoerfunctie.|
   |Gedaald|Geeft aan of de invoerfunctie is verwijderd of gebruikt.|
   |EngineeringFeatureCount|Aantal functies gegenereerd door middel van geautomatiseerde feature engineering transformaties.|
   |Transformaties|Lijst met transformaties die worden toegepast op invoerfuncties om ontworpen functies te genereren.|
   
### <a name="customize-feature-engineering"></a>Functie-engineering aanpassen
Als u functie-engineering wilt aanpassen, geeft u op `"featurization": FeaturizationConfig`.

Ondersteunde aanpassing omvat:

|Aanpassing|Definitie|
|--|--|
|Update van kolomdoel|Functietype overschrijven voor de opgegeven kolom.|
|Parameterupdate transformator |Parameters bijwerken voor de opgegeven transformator. Ondersteunt momenteel Imputer (gemiddelde, meest voorkomende & mediaan) en HashOneHotEncoder.|
|Kolommen neerzetten |Kolommen te laten vallen van wordt featurized.|
|Bloktransformatoren| Bloktransformatoren te gebruiken op featurization proces.|

Maak het object FeaturizationConfig met API-aanroepen:
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Schalen/normaliseren en algoritme met hyperparameterwaarden:

Gebruik fitted_model.steps om inzicht te krijgen in de waarden schalen/normaliseren en algoritme/hyperparameter voor een pijplijn. [Meer informatie over schalen/normaliseren](concept-automated-ml.md#preprocess). Hier volgt een voorbeeld van uitvoer:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Voor meer informatie gebruikt u deze helperfunctie in [dit voorbeeldnotitieblok.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(fitted_model)
```

De volgende voorbeelduitvoer is voor een pijplijn met behulp van een specifiek algoritme (LogisticRegression met RobustScalar, in dit geval).

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>De kans op klasse voorspellen

Modellen die worden geproduceerd met behulp van geautomatiseerde ML hebben allemaal wrapper objecten die functionaliteit spiegelen van hun open-source origin klasse. De meeste classificatiemodelwrapperobjecten die `predict_proba()` worden geretourneerd door geautomatiseerde ML implementeren de functie, die een array-achtig of schaars matrixgegevensvoorbeeld van uw functies (X-waarden) accepteert en een n-dimensionale array van elk monster en de respectievelijke klassewaarschijnlijkheid retourneert.

Ervan uitgaande dat u het beste run en fitted model hebt `predict_proba()` opgehaald met dezelfde oproepen `X_test` van bovenaf, u rechtstreeks vanuit het gemonteerde model bellen en een monster in het juiste formaat leveren, afhankelijk van het modeltype.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Als het onderliggende model `predict_proba()` de functie niet ondersteunt of de indeling onjuist is, wordt een modelklassespecifieke uitzondering gegenereerd. Zie de [referentiedocumenten RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) en [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) voor voorbeelden van hoe deze functie voor verschillende modeltypen wordt geïmplementeerd.

<a name="explain"></a>

## <a name="model-interpretability"></a>Interpreteerbaarheid van modellen

Met modelinterpreteerbaarheid u begrijpen waarom uw modellen voorspellingen hebben gedaan en wat de onderliggende waarden voor het functiebelang zijn. De SDK bevat verschillende pakketten voor het inschakelen van modelinterpreteerbaarheidsfuncties, zowel op training als inference tijd, voor lokale en geïmplementeerde modellen.

Bekijk de [how-to](how-to-machine-learning-interpretability-automl.md) voor codevoorbeelden over hoe u interpreteerbaarheidsfuncties specifiek in schakelt in geautomatiseerde machine learning-experimenten.

Zie het [conceptartikel](how-to-machine-learning-interpretability.md) over interpreteerbaarheid voor algemene informatie over hoe modeluitleg en functiebelang op andere gebieden van de SDK kunnen worden ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [hoe en waar u een model implementeren.](how-to-deploy-and-where.md)

Meer informatie over het trainen van [een regressiemodel met Geautomatiseerde machine learning](tutorial-auto-train-models.md) of hoe u trainen met [geautomatiseerde machine learning op een externe bron.](how-to-auto-train-remote.md)
