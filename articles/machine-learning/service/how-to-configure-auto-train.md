---
title: Automatische ML experimenten maken
titleSuffix: Azure Machine Learning
description: Geautomatiseerde machine learning, kiest een algoritme voor u en genereert een model dat gereed is voor implementatie. Meer informatie over de opties die u kunt met geautomatiseerde machine learning-experimenten configureren.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 2ef501af63628b47bc52d416930c90057569b5de
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035031"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Automatische ML experimenten configureren in python

In deze hand leiding vindt u informatie over het definiëren van verschillende configuratie-instellingen van uw geautomatiseerde machine learning experimenten met de [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Geautomatiseerde machine learning, kiest een algoritme en hyperparameters voor u en genereert een model dat gereed is voor implementatie. Er zijn diverse opties, kunt u geautomatiseerde machine learning-experimenten configureren.

Zie [zelf studie: een voor beeld van een geautomatiseerde machine learning experimenten. Train een classificatie model met geautomatiseerde machine learning](tutorial-auto-train-models.md) of [Train modellen met geautomatiseerde machine learning in de Cloud](how-to-auto-train-remote.md).

Configuratie-opties zijn beschikbaar in geautomatiseerde machine learning:

* Selecteer het type experiment: Classificatie, regressie of time series-prognose
* De gegevensbron, indelingen en ophalen van gegevens
* Kies uw compute-doel: lokale of externe
* Geautomatiseerde machine learning-experiment-instellingen
* Een geautomatiseerde machine learning-experiment uitvoeren
* Model metrische gegevens verkennen
* Registreer en implementeer model

Als u liever geen code hebt, kunt u ook [uw geautomatiseerde machine learning experimenten in azure portal maken](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Selecteer het type experiment

Voordat u uw experiment, moet u het type van machine learning probleem, u het oplossen van bepalen. Geautomatiseerde machine learning ondersteunt taaktypen classificatie, regressie en prognose.

Geautomatiseerde machine learning ondersteunt de volgende algoritmen tijdens de automatisering en het afstemmen van proces. Als een gebruiker is er niet nodig voor u het algoritme opgeven.

Classificatie | Regressie | Tijd reeks prognose
|-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastische Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastische Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Licht GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Licht GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Licht GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Versterking van kleurovergang](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Versterking van kleurovergang](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Versterking van kleurovergang](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Beslissingsstructuur](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Beslissingsstructuur](https://scikit-learn.org/stable/modules/tree.html#regression)|[Beslissingsstructuur](https://scikit-learn.org/stable/modules/tree.html#regression)
[K dichtstbijzijnde Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K dichtstbijzijnde Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K dichtstbijzijnde Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineaire SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C-Support Vector classificatie (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[De toolkit leren met stochastische Gradiëntdaling (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[De toolkit leren met stochastische Gradiëntdaling (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Willekeurige Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Willekeurige Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Willekeurige Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Zeer willekeurige structuren](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Zeer willekeurige structuren](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Zeer willekeurige structuren](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificatie DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor hierop](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor hierop](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Lineaire classificatie DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineaire Regressor hierop](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Lineaire Regressor hierop](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[De toolkit leren met stochastische Gradiëntdaling (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

Gebruik de `task` para meter in `AutoMLConfig` de constructor om uw type experiment op te geven.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>Gegevensbron en indeling
Geautomatiseerde machine learning biedt ondersteuning voor gegevens die zich bevinden op het lokale bureaublad of in de cloud zoals Azure Blob Storage. De gegevens kunnen worden gelezen in scikit-informatie over ondersteunde gegevensindelingen. U kunt de gegevens in lezen:
* Numpy matrices X (kenmerken)- en y (doelvariabele of ook wel bekend als label)
* Pandas dataframe

>[!Important]
> Vereisten voor trainings gegevens:
>* Gegevens moeten in tabel vorm zijn.
>* De waarde die u wilt voors pellen (doel kolom), moet aanwezig zijn in de gegevens.

Voorbeelden:

*   Numpy matrices

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data
    y_digits = digits.target
    ```

*   Pandas dataframe

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    y = df["Label"]
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Ophalen van gegevens voor het experiment uitvoeren op externe compute

Voor externe uitvoeringen moet u de gegevens toegankelijk maken vanaf de externe compute. U kunt dit doen door de gegevens naar de Data Store te uploaden.

Hier volgt een voor beeld van `datastore`het gebruik van:

```python
    import pandas as pd
    from sklearn import datasets

    data_train = datasets.load_digits()

    pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
    pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)
```

### <a name="define-dprep-references"></a>Dprep-verwijzingen definiëren

Definieer X en y als referentie voor dprep, die wordt door gegeven aan een `AutoMLConfig` automatisch machine learning object dat er ongeveer als volgt uitziet:

```python

    X = dprep.auto_read_file(path=ds.path('digitsdata/X_train.csv'))
    y = dprep.auto_read_file(path=ds.path('digitsdata/y_train.csv'))


    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 path = project_folder,
                                 run_configuration=conda_run_config,
                                 X = X,
                                 y = y,
                                 **automl_settings
                                )
```

## <a name="train-and-validation-data"></a>Train en validatie

U kunt afzonderlijke treinen en validatie sets rechtstreeks in de `AutoMLConfig` -methode opgeven.

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

Bekijk de [github-site](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) voor bijvoorbeeld notebooks met lokale en externe Compute-doelen.

*   Een Azure Databricks cluster in uw Azure-abonnement. U kunt hier meer informatie vinden- [Setup Azure Databricks cluster voor automatische milliliters](how-to-configure-environment.md#azure-databricks)

Bekijk de [github-site](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) voor bijvoorbeeld notebooks met Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Uw experiment-instellingen configureren

Er zijn diverse opties, kunt u uw geautomatiseerde machine learning-experiment configureren. Deze parameters zijn ingesteld door het instantiëren van een `AutoMLConfig` object. Zie de [klasse AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) voor een volledige lijst met para meters.

Voorbeelden zijn:

1.  Classificatie-experiment met behulp van AUC gewogen als de primaire metrische gegevens met een maximale tijd van 12.000 seconden per herhaling, met het experiment te beëindigen nadat u hebt 50 iteraties en 2 cross validatie vouwen.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        X=X,
        y=y,
        n_cross_validations=2)
    ```
2.  Hieronder volgt een voorbeeld van een set regressie-experiment na 100 herhalingen, elke herhaling duurt maximaal 600 seconden met 5 validatie kruislings vouwen.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

De drie verschillende `task` parameter waarden bepalen de lijst met algoritmen die moeten worden toegepast.  Gebruik de `whitelist` para `blacklist` meters of om iteraties verder te wijzigen met de beschik bare algoritmen die moeten worden opgenomen of uitgesloten. De lijst met ondersteunde modellen vindt u in de [klasse SupportedAlgorithms](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Primaire metrische gegevens
Primaire metriek; zoals wordt weer gegeven in de bovenstaande voor beelden, bepaalt u de metrische gegevens die moeten worden gebruikt tijdens de model training voor Optima Lise ring. De primaire metriek die u kunt selecteren, wordt bepaald door het taak type dat u kiest. Hieronder vindt u een lijst met beschik bare metrische gegevens.

Meer informatie over de specifieke definities van deze in [inzicht in geautomatiseerde machine learning resultaten](how-to-understand-automated-ml.md).

|Classificatie | Regressie | Tijd reeks prognose
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>& Voor verwerking van gegevens parametrisatie

In elk automatisch machine learning experiment worden uw gegevens [automatisch geschaald en genormaliseerd](concept-automated-ml.md#preprocess) om de Help-algoritmen goed uit te voeren.  U kunt echter ook aanvullende voor verwerking/parametrisatie inschakelen, zoals ontbrekende waarden, code ring en trans formaties. Meer [informatie over wat parametrisatie is inbegrepen](how-to-create-portal-experiments.md#preprocess).

Als u deze parametrisatie wilt inschakelen `"preprocess": True` , geeft u voor de [ `AutoMLConfig` klasse](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)op.

> [!NOTE]
> Automatische machine learning vooraf verwerkte stappen (functie normalisatie, het verwerken van ontbrekende gegevens, het converteren van tekst naar numerieke waarde, enzovoort) worden onderdeel van het onderliggende model. Wanneer u het model gebruikt voor voor spellingen, worden dezelfde vooraf verwerkings stappen die tijdens de training worden toegepast, automatisch toegepast op uw invoer gegevens.

### <a name="time-series-forecasting"></a>Tijd reeks prognose
Voor tijdreeks prognose taak type hebt u aanvullende para meters die u kunt definiëren.
1. time_column_name: dit is een vereiste para meter waarmee de naam van de kolom in uw trainings gegevens wordt gedefinieerd die de datum/tijd-reeks bevat.
1. max_horizon: Hiermee definieert u de tijds duur die u wilt voors pellen op basis van de periodiciteit van de trainings gegevens. Als u bijvoorbeeld trainings gegevens met dagelijkse tijd korrels hebt, definieert u hoe ver in dagen u het model wilt trainen.
1. grain_column_names: Hiermee definieert u de naam van kolommen die afzonderlijke tijdreeks gegevens bevatten in uw trainings gegevens. Als u bijvoorbeeld de verkoop van een bepaald merk per winkel wilt ramen, definieert u de kolommen Store en merk als korrel.

Bekijk een voor beeld van deze instellingen die hieronder worden gebruikt. voor beeld van notebook is [hier](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)beschikbaar.

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

automl_config = AutoMLConfig(task='forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble"></a>Configuratie van ensemble

Ensemble-modellen zijn standaard ingeschakeld en worden weer gegeven als de laatste uitvoerings herhalingen in een automatische machine learning uitvoering. Momenteel worden op dit moment ondersteunde ensemble-methoden gestemd en gestapeld. Stemmen wordt geïmplementeerd als zachte stem met gewogen gemiddelden en de stacking-implementatie maakt gebruik van een implementatie met twee lagen, waarbij de eerste laag dezelfde modellen heeft als de naam van de stem-ensemble en het tweede laag model wordt gebruikt om de optimale combi natie van de modellen van de eerste laag. Als u gebruikmaakt van ONNX-modellen **of** als u model uitleg hebt ingeschakeld, wordt stacking uitgeschakeld en wordt alleen de stem gebruikt.

Er zijn meerdere standaard argumenten die kunnen worden gegeven als `kwargs` in een `AutoMLConfig` -object om het standaard gedrag van de stack-ensemble te wijzigen.

* `stack_meta_learner_type`: de meta-informatieer is een model dat is getraind op de uitvoer van de afzonderlijke heterogene modellen. `LogisticRegression` Standaard-meta informatie is voor classificatie taken (of `LogisticRegressionCV` als kruis validatie is ingeschakeld) en voor regressie `ElasticNet` -en prognose taken (of `ElasticNetCV` als kruis validatie is ingeschakeld). Deze para meter kan een van de volgende teken reeksen `LogisticRegression`zijn `LogisticRegressionCV`: `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor`,, `LinearRegression`, of.
* `stack_meta_learner_train_percentage`: Hiermee geeft u het aandeel van de Trainingsset (bij het kiezen van trein en validatie type training) op dat moet worden gereserveerd voor de training van de meta-informatieer. De standaard waarde `0.2`is.
* `stack_meta_learner_kwargs`: optionele para meters die moeten worden door gegeven aan de initialisatie functie van de meta-informatieer. Deze para meters en parameter typen spie gelen die van de bijbehorende model-constructor en worden doorgestuurd naar de model-constructor.

De volgende code toont een voor beeld van het opgeven van een aangepast `AutoMLConfig` ensemble-gedrag in een object.

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
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        **ensemble_settings
        )
```

Een ensemble-training is standaard ingeschakeld, maar kan worden uitgeschakeld met behulp `enable_voting_ensemble` van `enable_stack_ensemble` de para meters en Booleaanse waarden.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Experiment uit te voeren

Voor automatische milliliters maakt u `Experiment` een object, een benoemd object in een `Workspace` gebruikt om experimenten uit te voeren.

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
Er zijn enkele opties die u kunt definiëren om uw experiment te volt ooien.
1. Geen criteria: als u geen afsluit parameters definieert, wordt het experiment voortgezet totdat er geen verdere voortgang wordt gemaakt op uw primaire metriek.
1. Aantal herhalingen: u definieert het aantal iteraties voor het uitvoeren van het experiment. U kunt optioneel iteration_timeout_minutes toevoegen om een tijds limiet in minuten per iteratie te definiëren.
1. Sluit af na een tijds duur: door experiment_timeout_minutes in uw instellingen te gebruiken, kunt u definiëren hoe lang in minuten een experiment moet worden uitgevoerd.
1. Afsluiten nadat een score is bereikt: met experiment_exit_score kunt u ervoor kiezen het experiment uit te voeren nadat een score op basis van uw primaire meet waarde is bereikt.

### <a name="explore-model-metrics"></a>Model metrische gegevens verkennen

U kunt uw trainings resultaten weer geven in een widget of inline als u zich in een notebook bevindt. Zie [bijhouden en evalueren van modellen](how-to-track-experiments.md#view-run-details) voor meer informatie.

## <a name="understand-automated-ml-models"></a>Meer informatie over geautomatiseerde modellen van ML

Elk model dat is gemaakt met behulp van automatische ML omvat de volgende stappen:
+ Geautomatiseerde functie techniek (indien preprocess = True)
+ Schalen/normaliseren en algoritmen met hypermeter-waarden

We maken het transparant om deze informatie op te halen uit de fitted_model-uitvoer van automatische MILLILITERs.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Geautomatiseerde functie techniek

Zie de lijst met voor verwerkings-en [automatische functie techniek](concept-automated-ml.md#preprocess) die zich voordoen als preprocess = True.

Bekijk dit voor beeld:
+ Er zijn vier invoer functies: A (Numeric), B (Numeric), C (Numeric), D (DateTime)
+ De numerieke functie C wordt verwijderd omdat deze een ID-kolom met alle unieke waarden bevat
+ De numerieke functies A en B bevatten ontbrekende waarden en worden daarom toegerekend door middel van gemiddelde
+ Datum/tijd-functie D is featurized in 11 verschillende ontworpen functies

Gebruik deze 2 Api's voor de eerste stap van het model voor meer informatie.  Bekijk [dit voor beeld van een notitie blok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` retourneert een lijst met functie namen van het Engineer.

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

   |Output|Definitie|
   |----|--------|
   |RawFeatureName|Invoer functie/kolom naam van de opgegeven gegevensset.|
   |TypeDetected|Het gegevens type van de invoer functie is gedetecteerd.|
   |Minder|Hiermee wordt aangegeven of de invoer functie is verwijderd of gebruikt.|
   |EngineeringFeatureCount|Aantal functies dat wordt gegenereerd via geautomatiseerde functie technische trans formaties.|
   |Transformaties|Lijst met trans formaties die zijn toegepast op de invoer functies voor het genereren van ontworpen functies.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Schalen/normaliseren en algoritmen met hypermeter-waarden:

Als u inzicht wilt krijgen in de waarden voor schalen/normalisatie en algoritme/afstemming voor een pijp lijn, gebruikt u fitted_model. Steps. Meer [informatie over schalen/normalisatie](concept-automated-ml.md#preprocess). Hier volgt een voorbeeld van uitvoer:

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

Hier volgt een voor beeld van uitvoer voor een pijp lijn met behulp van een specifiek algoritme (LogisticRegression met RobustScalar, in dit geval).

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

<a name="explain"></a>

## <a name="explain-the-model-interpretability"></a>Het model uitleggen (interpretion-service)

Geautomatiseerde machine learning kunt u inzicht krijgt in de functie belang.  Tijdens de training krijgt u de algemene functie van belang voor het model.  U kunt ook op klasseniveau functie belang ophalen voor de classificatie-scenario's.  U moet een validatie-gegevensset (X_valid) om op te halen van de functie urgentie opgeven.

Er zijn twee manieren voor het genereren van functie belang.

*   Als een experiment voltooid is, kunt u `explain_model` methode bij elke iteratie.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

*   Als u wilt bekijken functie belang voor alle iteraties, `model_explainability` markering `True` in AutoMLConfig.

    ```python
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train,
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    Zodra u klaar bent, kunt u retrieve_model_explanation methode om op te halen van belang van de functie voor een specifieke versie.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

De URL weer geven om de functie urgentie te bekijken met het object uitvoeren:

```
automl_run.get_portal_url()
```

U kunt het grafiek onderdeel urgentie in uw werk ruimte visualiseren in de Azure Portal of op de [landings pagina van uw werk ruimte (preview)](https://ml.azure.com). De grafiek wordt ook weer gegeven wanneer u `RunDetails` de [Jupyter-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) in een notitie blok gebruikt. Zie voor meer informatie over de grafieken [automatische machine learning resultaten](how-to-understand-automated-ml.md).

```Python
from azureml.widgets import RunDetails
RunDetails(automl_run).show()
```

![functie belang graph](./media/how-to-configure-auto-train/feature-importance.png)

Zie voor meer informatie over hoe model toelichtingen en functie belang kunnen worden ingeschakeld op andere gebieden van de SDK buiten automatische machine learning, het [concept](machine-learning-interpretability-explainability.md) artikel over de interpretatie mogelijkheden.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [hoe en waar u kunt een model implementeren](how-to-deploy-and-where.md).

Meer informatie over [het trainen van een regressie model met geautomatiseerde machine learning](tutorial-auto-train-models.md) of [hoe u het gebruik van geautomatiseerde machine learning op een externe bron kunt trainen](how-to-auto-train-remote.md).
