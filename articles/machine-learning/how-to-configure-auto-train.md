---
title: Automatische ML experimenten maken
titleSuffix: Azure Machine Learning
description: Geautomatiseerde machine learning, kiest een algoritme voor u en genereert een model dat gereed is voor implementatie. Meer informatie over de opties die u kunt met geautomatiseerde machine learning-experimenten configureren.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: c1ebedcf93d66c01c80f7f40171a7aa27441488d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722149"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Automatische ML experimenten configureren in python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze hand leiding vindt u informatie over het definiëren van verschillende configuratie-instellingen van uw geautomatiseerde machine learning experimenten met de [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Geautomatiseerde machine learning, kiest een algoritme en hyperparameters voor u en genereert een model dat gereed is voor implementatie. Er zijn diverse opties, kunt u geautomatiseerde machine learning-experimenten configureren.

Zie [zelf studie: een classificatie model met geautomatiseerde machine learning](tutorial-auto-train-models.md) of [Train modellen met geautomatiseerde machine learning in de Cloud](how-to-auto-train-remote.md)om voor beelden van een geautomatiseerde machine learning experimenten weer te geven.

Configuratie-opties zijn beschikbaar in geautomatiseerde machine learning:

* Selecteer het type experiment: classificatie, regressie of time series-prognose
* De gegevensbron, indelingen en ophalen van gegevens
* Kies uw compute-doel: lokale of externe
* Geautomatiseerde machine learning-experiment-instellingen
* Een geautomatiseerde machine learning-experiment uitvoeren
* Model metrische gegevens verkennen
* Registreer en implementeer model

Als u liever geen code hebt, kunt u ook [uw geautomatiseerde machine learning experimenten maken in azure machine learning Studio](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Selecteer het type experiment

Voordat u uw experiment, moet u het type van machine learning probleem, u het oplossen van bepalen. Automatische machine learning ondersteunt de taak typen classificatie, regressie en prognose. Meer informatie over [taak typen](how-to-define-task-type.md).

Geautomatiseerde machine learning ondersteunt de volgende algoritmen tijdens de automatisering en het afstemmen van proces. Als een gebruiker is er niet nodig voor u het algoritme opgeven.

Classificatie | Regressie | Tijd reeks prognose
|-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastische Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastische Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Licht GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Licht GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Licht GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Versterking van kleurovergang](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Versterking van kleurovergang](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Versterking van kleurovergang](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Beslissingsstructuur](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Beslissingsstructuur](https://scikit-learn.org/stable/modules/tree.html#regression)|[Beslissingsstructuur](https://scikit-learn.org/stable/modules/tree.html#regression)
[K dichtstbijzijnde Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K dichtstbijzijnde Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K dichtstbijzijnde Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineaire SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Vector classificatie (SVC) ondersteunen](https://scikit-learn.org/stable/modules/svm.html#classification)|[De toolkit leren met stochastische Gradiëntdaling (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[De toolkit leren met stochastische Gradiëntdaling (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Willekeurige Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Willekeurige Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Willekeurige Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Zeer willekeurige structuren](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Zeer willekeurige structuren](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Zeer willekeurige structuren](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificatie DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor hierop](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor hierop](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Lineaire classificatie DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineaire Regressor hierop](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Lineaire Regressor hierop](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Snelle lineaire Regressor hierop](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Automatische ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[De toolkit leren met stochastische Gradiëntdaling (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Online verloop Daal Regressor hierop](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
|[Gemiddelde Perceptron-classificatie](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[Classificatie van lineaire SVM](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)||

Gebruik de para meter `task` in de `AutoMLConfig`-constructor om uw type experiment op te geven.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Gegevensbron en indeling

Geautomatiseerde machine learning biedt ondersteuning voor gegevens die zich bevinden op het lokale bureaublad of in de cloud zoals Azure Blob Storage. De gegevens kunnen worden gelezen in een **Panda data frame** of een **Azure machine learning TabularDataset**.  [Meer informatie over gegevens sets](how-to-create-register-datasets.md).

Vereisten voor trainings gegevens:
- Gegevens moeten in tabel vorm zijn.
- De waarde die u wilt voors pellen, doel kolom, moet in de gegevens zijn.

De volgende code voorbeelden laten zien hoe u de gegevens in deze indelingen opslaat.

* TabularDataset
  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Pandas dataframe

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split

    df = pd.read_csv("your-local-file.csv")
    train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
    label = "label-col-name"
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Ophalen van gegevens voor het experiment uitvoeren op externe compute

Voor uitvoering op afstand moeten de trainings gegevens toegankelijk zijn vanaf de externe compute. De [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) van de klasse van de SDK biedt de volgende functionaliteit:

* eenvoudig gegevens van statische bestanden of URL-bronnen overdragen naar uw werk ruimte
* uw gegevens beschikbaar maken voor trainings scripts wanneer ze worden uitgevoerd op Cloud Compute-resources

Zie de [procedures](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) voor een voor beeld van het gebruik van de klasse `Dataset` om gegevens te koppelen aan uw reken doel.

## <a name="train-and-validation-data"></a>Train en validatie

U kunt afzonderlijke treinen en validatie sets rechtstreeks in de `AutoMLConfig`-constructor opgeven.

### <a name="k-folds-cross-validation"></a>K-vouwen Kruisvalidatie

Gebruik `n_cross_validations` instelling om op te geven van het aantal cross-bevestigingen. De set trainingsgegevens wordt willekeurig worden gesplitst in `n_cross_validations` vouwen van gelijke grootte. Tijdens elke cross validatie ronde, een van de vouwen dat wordt gebruikt voor de validatie van het model is getraind op de resterende vouwen. Dit proces wordt herhaald voor `n_cross_validations` totdat elke vouwen één keer wordt gebruikt als validatie afgerond. De gemiddelde scores voor alle `n_cross_validations` Rondt worden gerapporteerd, en de bijbehorende model zal opnieuw worden getraind op de gehele training gegevensset.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo-Kruis validatie (herhaalde, wille keurige subsampling)

Gebruik `validation_size` om op te geven van het percentage van de training-gegevensset die moet worden gebruikt voor validatie en gebruik `n_cross_validations` om op te geven van het aantal cross-bevestigingen. Tijdens elke cross-validatie afgerond, een subset van de grootte van `validation_size` willekeurig worden geselecteerd voor de validatie van het model is getraind op de resterende gegevens. Ten slotte het gemiddelde voor alle beoordeelt `n_cross_validations` Rondt worden gerapporteerd, en de bijbehorende model zal opnieuw worden getraind op de gehele training gegevensset. Monte Carlo wordt niet ondersteund voor time series-prognoses.

### <a name="custom-validation-dataset"></a>Aangepaste validatie-gegevensset

Aangepaste validatie gegevensset gebruiken als wille keurige splitsing niet acceptabel is, meestal time series-gegevens of gegevens die niet in balans zijn. U kunt uw eigen gegevensset validatie opgeven. Het model wordt geëvalueerd op basis van de gegevensset validatie is opgegeven in plaats van willekeurige gegevensset.

## <a name="compute-to-run-experiment"></a>COMPUTE experiment uitvoeren

Vervolgens kunt u bepalen waar u het model wordt getraind. Een geautomatiseerde machine learning-trainingsexperiment kunt uitvoeren op de volgende compute-opties:
*   Uw lokale machine, zoals een lokale desktop of laptop – algemeen wanneer u kleine gegevensset hebt en u bent nog steeds in de fase verkennen.
*   Een externe computer in de cloud, [Azure Machine Learning-beheerd Compute](concept-compute-target.md#amlcompute) is een beheerde service die het mogelijk om te trainen op clusters virtuele machines van Azure machine learning-modellen.

    Bekijk deze [github-site](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) voor voor beelden van notitie blokken met lokale en externe Compute-doelen.

*   Een Azure Databricks cluster in uw Azure-abonnement. U kunt hier meer informatie vinden- [Setup Azure Databricks cluster voor automatische milliliters](how-to-configure-environment.md#azure-databricks)

    Bekijk deze [github-site](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) voor voor beelden van notitie blokken met Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Uw experiment-instellingen configureren

Er zijn diverse opties, kunt u uw geautomatiseerde machine learning-experiment configureren. Deze parameters zijn ingesteld door het instantiëren van een `AutoMLConfig` object. Zie de [klasse AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) voor een volledige lijst met para meters.

Voorbeelden zijn:

1.  Beoordelings experiment waarbij AUC wordt gewogen als primaire metriek met de time-out van het experiment, ingesteld op 30 minuten en 2 Kruis validatie vouwen.

    ```python
    automl_classifier=AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        blacklist_models='XGBoostClassifier',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=2)
    ```
2.  Hieronder ziet u een voor beeld van een regressie experiment dat is ingesteld om na 60 minuten te eindigen met vijf validatie-Kruis vouwen.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        experiment_timeout_minutes=60,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5)
    ```

De drie verschillende `task` parameter waarden (het derde taak type is `forecasting`en gebruikt een vergelijk bare algoritme groep als `regression` taken) om de lijst te bepalen van de modellen die moeten worden toegepast. Gebruik de para meters `whitelist` of `blacklist` om iteraties verder te wijzigen met de beschik bare modellen die moeten worden opgenomen of uitgesloten. De lijst met ondersteunde modellen vindt u in de [klasse SupportedModels](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels).

### <a name="primary-metric"></a>Primaire metrische gegevens
De primaire meet waarde bepaalt de metrische gegevens die moeten worden gebruikt tijdens de model training voor Optima Lise ring. De beschik bare metrische gegevens die u kunt selecteren, worden bepaald door het taak type dat u kiest, en in de volgende tabel worden geldige primaire metrische gegevens weer gegeven voor elk taak type.

|Classificatie | Regressie | Tijd reeks prognose
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Meer informatie over de specifieke definities van deze metrische gegevens in [inzicht in geautomatiseerde machine learning resultaten](how-to-understand-automated-ml.md).

### <a name="data-featurization"></a>Gegevens parametrisatie

In elk automatisch machine learning experiment worden uw gegevens [automatisch geschaald en genormaliseerd](concept-automated-ml.md#preprocess) om *bepaalde* algoritmen te helpen die gevoelig zijn voor functies die op verschillende schalen zijn.  U kunt echter ook extra parametrisatie inschakelen, zoals ontbrekende waarden, code ring en trans formaties. Meer [informatie over wat parametrisatie is inbegrepen](how-to-create-portal-experiments.md#preprocess).

Als u deze parametrisatie wilt inschakelen, geeft u `"featurization": 'auto'` op voor de [`AutoMLConfig` klasse](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

> [!NOTE]
> Automatische machine learning vooraf verwerkte stappen (functie normalisatie, het verwerken van ontbrekende gegevens, het converteren van tekst naar numerieke waarde, enzovoort) worden onderdeel van het onderliggende model. Wanneer u het model gebruikt voor voor spellingen, worden dezelfde vooraf verwerkings stappen die tijdens de training worden toegepast, automatisch toegepast op uw invoer gegevens.

### <a name="time-series-forecasting"></a>Tijd reeks prognose
De time series-`forecasting` taak vereist extra para meters in het configuratie object:

1. `time_column_name`: de vereiste para meter waarmee de naam van de kolom in uw trainings gegevens wordt gedefinieerd die een geldige time-reeks bevat.
1. `max_horizon`: definieert de tijds duur die u wilt voors pellen op basis van de periodiciteit van de trainings gegevens. Als u bijvoorbeeld trainings gegevens met dagelijkse tijd korrels hebt, definieert u hoe ver in dagen u het model wilt trainen.
1. `grain_column_names`: definieert de naam van kolommen die afzonderlijke tijdreeks gegevens bevatten in uw trainings gegevens. Als u bijvoorbeeld de verkoop van een bepaald merk per winkel wilt ramen, definieert u de kolommen Store en merk als korrel. Er worden afzonderlijke time-series en prognoses voor elke korrel/groepering gemaakt. 

Zie voor voor beelden van de onderstaande instellingen het voor [beeld-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

### <a name="ensemble"></a>Configuratie van ensemble

Ensemble-modellen zijn standaard ingeschakeld en worden weer gegeven als de laatste uitvoerings herhalingen in een automatische machine learning uitvoering. Momenteel worden op dit moment ondersteunde ensemble-methoden gestemd en gestapeld. Stemmen wordt geïmplementeerd als zachte stem met gewogen gemiddelden en de stacking-implementatie maakt gebruik van een implementatie met twee lagen, waarbij de eerste laag dezelfde modellen heeft als de naam van het stem-ensemble en het tweede laag model wordt gebruikt om de optimale combi natie van te vinden de modellen van de eerste laag. Als u gebruikmaakt van ONNX-modellen **of** als u model uitleg hebt ingeschakeld, wordt stacking uitgeschakeld en wordt alleen de stem gebruikt.

Er zijn meerdere standaard argumenten die kunnen worden verschaft als `kwargs` in een `AutoMLConfig`-object om het standaard gedrag van de stack-ensemble te wijzigen.

* `stack_meta_learner_type`: de meta-leerer is een model dat is getraind op de uitvoer van de afzonderlijke heterogene modellen. Standaard-meta-informatie is `LogisticRegression` voor classificatie taken (of `LogisticRegressionCV` als kruis validatie is ingeschakeld) en `ElasticNet` voor regressie/prognose taken (of `ElasticNetCV` als kruis validatie is ingeschakeld). Deze para meter kan een van de volgende teken reeksen zijn: `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor`of `LinearRegression`.
* `stack_meta_learner_train_percentage`: Hiermee geeft u het aandeel van de Trainingsset (bij het kiezen van trein en validatie type training) op die moet worden gereserveerd voor de training van de meta-informatieer. De standaard waarde is `0.2`.
* `stack_meta_learner_kwargs`: optionele para meters die moeten worden door gegeven aan de initialisatie functie van de meta-informatieer. Met deze para meters en parameter typen worden de para meters en parameter typen van de bijbehorende model-constructor gespiegeld en doorgestuurd naar de model-constructor.

De volgende code toont een voor beeld van het opgeven van een aangepast ensemble-gedrag in een `AutoMLConfig`-object.

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

Een ensemble-training is standaard ingeschakeld, maar kan worden uitgeschakeld met behulp van de `enable_voting_ensemble` en `enable_stack_ensemble` Booleaanse para meters.

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

## <a name="run-experiment"></a>Experiment uit te voeren

Voor automatische MILLILITERs maakt u een `Experiment`-object, een benoemd object in een `Workspace` gebruikt voor het uitvoeren van experimenten.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Het experiment uitvoeren en het genereren van een model verzenden. Geeft de `AutoMLConfig` naar de `submit` methode voor het genereren van het model.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Afhankelijkheden zijn geïnstalleerd op een nieuwe virtuele machine.  Het kan maximaal 10 minuten duren voordat uitvoer wordt weergegeven.
>Instellen van `show_output` naar `True` resulteert in de uitvoer wordt weergegeven op de console.

### <a name="exit-criteria"></a>Afsluit criteria
Er zijn enkele opties die u kunt definiëren om uw experiment te beëindigen.
1. Geen criteria: als u geen afsluit parameters definieert, wordt het experiment voortgezet totdat er geen verdere voortgang wordt gemaakt op uw primaire metriek.
1. Afsluiten na een periode: door `experiment_timeout_minutes` in uw instellingen te gebruiken, kunt u opgeven hoelang in minuten een experiment moet worden uitgevoerd.
1. Afsluiten nadat een score is bereikt: als u `experiment_exit_score` gebruikt, wordt het experiment voltooid nadat een primaire meet Score is bereikt.

### <a name="explore-model-metrics"></a>Model metrische gegevens verkennen

U kunt uw trainings resultaten weer geven in een widget of inline als u zich in een notebook bevindt. Zie [bijhouden en evalueren van modellen](how-to-track-experiments.md#view-run-details) voor meer informatie.

## <a name="understand-automated-ml-models"></a>Meer informatie over geautomatiseerde modellen van ML

Elk model dat is gemaakt met behulp van automatische ML omvat de volgende stappen:
+ Geautomatiseerde functie techniek (indien `"featurization": 'auto'`)
+ Schalen/normaliseren en algoritmen met afstemming-waarden

We maken het transparant om deze informatie op te halen uit de fitted_model uitvoer van automatische MILLILITERs.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Geautomatiseerde functie techniek

Zie de lijst met voor verwerkings-en [automatische functie techniek](concept-automated-ml.md#preprocess) die zich voordoen wanneer `"featurization": 'auto'`.

Bekijk dit voor beeld:
+ Er zijn vier invoer functies: A (numeriek), B (numeriek), C (numeriek), D (DateTime)
+ De numerieke functie C wordt verwijderd omdat deze een ID-kolom met alle unieke waarden bevat
+ De numerieke functies A en B bevatten ontbrekende waarden en worden daarom toegerekend aan het gemiddelde
+ Datum/tijd-functie D is featurized in 11 verschillende ontworpen functies

Gebruik deze 2 Api's voor de eerste stap van het model voor meer informatie.  Bekijk [dit voor beeld van een notitie blok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` retourneert een lijst met de namen van de functies van de functie.

  Gebruik:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Deze lijst bevat alle functie namen van technici.

  >[!Note]
  >Gebruik ' timeseriestransformer ' voor taak = ' prognose ', Else gebruik ' datatransformer ' voor de taak ' regressie ' of ' classificatie '.

+ API 2: `get_featurization_summary()` retourneert een samen vatting van parametrisatie voor alle invoer functies.

  Gebruik:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Gebruik ' timeseriestransformer ' voor taak = ' prognose ', Else gebruik ' datatransformer ' voor de taak ' regressie ' of ' classificatie '.

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
   |RawFeatureName|Invoer functie/kolom naam van de opgegeven gegevensset.|
   |TypeDetected|Het gegevens type van de invoer functie is gedetecteerd.|
   |Minder|Hiermee wordt aangegeven of de invoer functie is verwijderd of gebruikt.|
   |EngineeringFeatureCount|Aantal functies dat wordt gegenereerd via geautomatiseerde functie technische trans formaties.|
   |Transformaties|Lijst met trans formaties die zijn toegepast op de invoer functies voor het genereren van ontworpen functies.|
   
### <a name="customize-feature-engineering"></a>Functie techniek aanpassen
Als u functie techniek wilt aanpassen, geeft u `"feauturization":FeaturizationConfig`op.

Ondersteunde aanpassingen zijn onder andere:

|Aanpassing|Definitie|
|--|--|
|Update van het kolom doel|Het functie type voor de opgegeven kolom overschrijven.|
|Para meter bijwerken van trans formatie |De para meters voor de opgegeven transformator bijwerken. Biedt momenteel ondersteuning voor toerekening (gemiddelde, meest frequente & mediaan) en HashOneHotEncoder.|
|Kolommen neerzetten |Kolommen die moeten worden verwijderd uit de featurized.|
|Trans formaties blok keren| Blok keren dat trans formaties moeten worden gebruikt in het parametrisatie-proces.|

Het FeaturizationConfig-object maken met API-aanroepen:
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

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Schalen/normaliseren en algoritmen met afstemming-waarden:

Gebruik fitted_model om inzicht te krijgen in de waarden voor schalen/normalisatie en algoritme/afstemming voor een pijp lijn. Meer [informatie over schalen/normalisatie](concept-automated-ml.md#preprocess). Hier volgt een voorbeeld van uitvoer:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Gebruik deze Help-functie die wordt weer gegeven in dit voor beeld van een [notitie blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)voor meer informatie.

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

De volgende voorbeeld uitvoer is voor een pijp lijn met behulp van een specifieke algoritme (LogisticRegression met RobustScalar, in dit geval).

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

### <a name="predict-class-probability"></a>Klasse waarschijnlijkheid voors pellen

Modellen die zijn gemaakt met behulp van automatische ML, bevatten alle wrapper-objecten die de functionaliteit van de open-source klasse Origin spie gelen. De meeste wrapper-objecten die door automatische MILLILITERs worden geretourneerd, implementeren de `predict_proba()` functie, die een matrix-achtige of Sparse matrix gegevens van uw functies (X-waarden) accepteert en een n-dimensionale matrix van elk voor beeld en de bijbehorende klasse-kans retourneert.

Als u het beste uitvoeren en het model hebt opgehaald met dezelfde aanroepen van bovenstaande, kunt u `predict_proba()` rechtstreeks aanroepen vanuit het model, waarbij u een `X_test`-voor beeld in de juiste indeling levert, afhankelijk van het model type.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Als het onderliggende model de `predict_proba()` functie niet ondersteunt of als de indeling onjuist is, wordt een model klasse-specifieke uitzonde ring gegenereerd. Zie de documentatie voor [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) en [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) voor voor beelden van hoe deze functie voor verschillende model typen wordt geïmplementeerd.

<a name="explain"></a>

## <a name="model-interpretability"></a>Interpreteerbaarheid van modellen

Met de functie voor het interpreteren van modellen kunt u begrijpen waarom de voor spellingen en de onderliggende waarden van de functie belang rijk zijn. De SDK bevat verschillende pakketten voor het inschakelen van functies voor het interpreteren van modellen, zowel tijdens trainingen als voor het afleiden van tijd, voor lokale en geïmplementeerde modellen.

Raadpleeg de [procedures](how-to-machine-learning-interpretability-automl.md) voor code voorbeelden voor het inschakelen van de functies voor het door verwijzen van voorzieningen, specifiek binnen automatische machine learning experimenten.

Voor algemene informatie over hoe model toelichtingen en functie belang kunnen worden ingeschakeld op andere gebieden van de SDK buiten automatische machine learning, raadpleegt u het [concept](how-to-machine-learning-interpretability.md) artikel over de interpretatie mogelijkheden.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [hoe en waar u kunt een model implementeren](how-to-deploy-and-where.md).

Meer informatie over [het trainen van een regressie model met geautomatiseerde machine learning](tutorial-auto-train-models.md) of [hoe u het gebruik van geautomatiseerde machine learning op een externe bron kunt trainen](how-to-auto-train-remote.md).
