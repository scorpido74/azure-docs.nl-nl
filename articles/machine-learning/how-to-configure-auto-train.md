---
title: Geautomatiseerde ML-experimenten maken
titleSuffix: Azure Machine Learning
description: Automatische machine learning kiest een algoritme voor u en genereert een model dat gereed is voor implementatie. Meer informatie over de opties die u kunt gebruiken voor het configureren van geautomatiseerde machine learning experimenten.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 05/20/2020
ms.custom: seodec18
ms.openlocfilehash: 5751a7c5eac9386ecd52e172b40aacbef5247dd1
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434619"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Geautomatiseerde ML-experimenten configureren in Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze hand leiding vindt u informatie over het definiëren van verschillende configuratie-instellingen van uw geautomatiseerde machine learning experimenten met de [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Automatische machine learning kiest een algoritme en Hyper parameters voor u en genereert een model dat gereed is voor implementatie. Er zijn verschillende opties die u kunt gebruiken voor het configureren van geautomatiseerde machine learning experimenten.

Zie [zelf studie: een classificatie model met geautomatiseerde machine learning](tutorial-auto-train-models.md) of [Train modellen met geautomatiseerde machine learning in de Cloud](how-to-auto-train-remote.md)om voor beelden van een geautomatiseerde machine learning experimenten weer te geven.

Configuratie opties die beschikbaar zijn in automatische machine learning:

* Selecteer het type experiment: classificatie, regressie of time series-prognose
* Gegevens bron, indelingen en gegevens ophalen
* Kies uw reken doel: lokaal of extern
* Instellingen voor automatisch machine learning experiment
* Een geautomatiseerd machine learning-experiment uitvoeren
* Model statistieken verkennen
* Model registreren en implementeren

Als u liever geen code hebt, kunt u ook [uw geautomatiseerde machine learning experimenten maken in azure machine learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="select-your-experiment-type"></a>Het type experimenten selecteren

Voordat u begint met het experiment, moet u het soort machine learning probleem bepalen dat u wilt oplossen. Automatische machine learning ondersteunt de taak typen classificatie, regressie en prognose. Meer informatie over [taak typen](how-to-define-task-type.md).

Automatische machine learning ondersteunt de volgende algoritmen tijdens het automatiserings-en afstemmings proces. Als gebruiker hoeft u het algoritme niet op te geven.

> [!NOTE]
> Als u van plan bent om uw automatisch gegenereerde modellen te exporteren naar een [ONNX-model](concept-onnx.md), kunnen alleen de algoritmen worden geconverteerd naar de ONNX-indeling. Meer informatie over [het converteren van modellen naar ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Opmerking: ONNX ondersteunt op dit moment alleen classificatie-en regressie taken. 

Classificatie | Regressie | Tijdreeksvoorspelling
|-- |-- |--
[Logistiek regressie](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastisch net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Licht GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Licht GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Licht GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Kleur overgang verhogen](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Kleur overgang verhogen](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Kleur overgang verhogen](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Beslissings structuur](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Beslissings structuur](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Beslissings structuur](https://scikit-learn.org/stable/modules/tree.html#regression)
[K dichtstbijzijnde neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K dichtstbijzijnde neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K dichtstbijzijnde neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineair-SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Vector classificatie (SVC) ondersteunen](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Stochastische kleur overgang Daal (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Stochastische kleur overgang Daal (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Wille keurig forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Wille keurig forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Zeer wille keurige structuren](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Zeer wille keurige structuren](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Zeer wille keurige structuren](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificatie DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier) |[DNN Regressor hierop](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor hierop](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Lineaire classificatie DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineaire Regressor hierop](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor) |[Lineaire Regressor hierop](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Snelle lineaire Regressor hierop](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Automatische ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Stochastische kleur overgang Daal (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* |[Online verloop Daal Regressor hierop](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
|[Gemiddelde Perceptron-classificatie](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[Classificatie van lineaire SVM](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)* ||

Gebruik de `task` para meter in de `AutoMLConfig` constructor om uw type experiment op te geven.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Gegevens bron en-indeling

Automatische machine learning ondersteunt gegevens die zich op uw lokale bureau blad of in de cloud bevinden, zoals Azure Blob Storage. De gegevens kunnen worden gelezen in een **Panda data frame** of een **Azure machine learning TabularDataset**.  [Meer informatie over gegevens sets](how-to-create-register-datasets.md).

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

* Panda data frame

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Gegevens ophalen voor het uitvoeren van experiment op externe compute

Voor uitvoering op afstand moeten de trainings gegevens toegankelijk zijn vanaf de externe compute. De-klasse [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) in de SDK biedt de volgende functionaliteit:

* eenvoudig gegevens van statische bestanden of URL-bronnen overdragen naar uw werk ruimte
* uw gegevens beschikbaar maken voor trainings scripts wanneer ze worden uitgevoerd op Cloud Compute-resources

Zie de [instructies](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) voor een voor beeld van het gebruik van de `Dataset` klasse om gegevens te koppelen aan uw reken doel.

## <a name="train-and-validation-data"></a>Train-en validatie gegevens

U kunt afzonderlijke treinen en validatie sets rechtstreeks in de `AutoMLConfig` constructor opgeven.

### <a name="k-folds-cross-validation"></a>Kruis validatie: K-vouwen

Gebruik `n_cross_validations` de instelling om het aantal Kruis validaties op te geven. De set met trainings gegevens wordt wille keurig gesplitst in `n_cross_validations` vouwen van gelijke grootte. Tijdens elke Kruis validatie ronde wordt een van de vouwen gebruikt voor de validatie van het model dat is getraind op de resterende vouwen. Dit proces wordt herhaald voor `n_cross_validations` afrondingen totdat elke vouw als validatieset wordt gebruikt. De gemiddelde scores `n_cross_validations` voor alle rondingen worden gerapporteerd en het bijbehorende model wordt opnieuw getraind op de hele set trainings gegevens.

Meer informatie over hoe autoML van toepassing is op Kruis validatie om te [voor komen dat modellen worden verouderd](concept-manage-ml-pitfalls.md#prevent-over-fitting).
### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo-Kruis validatie (herhaalde, wille keurige subsampling)

Gebruik `validation_size` om het percentage van de trainings gegevensset op te geven dat moet worden gebruikt voor validatie en `n_cross_validations` om het aantal Kruis validaties op te geven. Tijdens elke Kruis validatie ronde is een subset van `validation_size` de grootte wille keurig geselecteerd voor validatie van het model dat is getraind voor de resterende gegevens. Ten slotte worden de gemiddelde scores `n_cross_validations` voor alle afrondingen gerapporteerd en wordt het bijbehorende model opnieuw getraind op de hele set trainings gegevens. Monte Carlo wordt niet ondersteund voor time series-prognoses.

### <a name="custom-validation-dataset"></a>Gegevensset voor aangepaste validatie

Aangepaste validatie gegevensset gebruiken als wille keurige splitsing niet acceptabel is, meestal time series-gegevens of gegevens die niet in balans zijn. U kunt uw eigen validatie gegevensset opgeven. Het model wordt geëvalueerd op basis van de validatie gegevensset die is opgegeven in plaats van een wille keurige gegevensset.

## <a name="compute-to-run-experiment"></a>Compute en uitvoering van het experiment instellen

Bepaal vervolgens op welke locatie het model moet worden getraind. Een geautomatiseerd machine learning training-experiment kan worden uitgevoerd op de volgende reken opties:
* Uw lokale computer, zoals een lokaal bureau blad of laptop, in het algemeen wanneer u een kleine gegevensset hebt en u zich nog steeds in het exploratie stadium bevindt.
* Een externe computer in de Cloud – [Azure machine learning beheerde Compute](concept-compute-target.md#amlcompute) is een beheerde service waarmee u machine learning modellen kunt trainen op clusters van virtuele machines van Azure. 

  Bekijk deze [github-site](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) voor voor beelden van notitie blokken met lokale en externe Compute-doelen.

* Een Azure Databricks cluster in uw Azure-abonnement. U kunt hier meer informatie vinden- [Setup Azure Databricks cluster voor automatische milliliters](how-to-configure-environment.md#azure-databricks)

  Bekijk deze [github-site](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) voor voor beelden van notitie blokken met Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>De instellingen voor uw experiment configureren

Er zijn verschillende opties die u kunt gebruiken om uw geautomatiseerde machine learning-experiment te configureren. Deze para meters worden ingesteld door een object te instantiëren `AutoMLConfig` . Zie de [klasse AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) voor een volledige lijst met para meters.

Voorbeelden zijn:

1. Beoordelings experiment waarbij AUC wordt gewogen als primaire metriek met de time-out van het experiment, ingesteld op 30 minuten en 2 Kruis validatie vouwen.

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
2. Hieronder ziet u een voor beeld van een regressie experiment dat is ingesteld om na 60 minuten te eindigen met vijf validatie-Kruis vouwen.

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

De drie verschillende `task` parameter waarden (het derde taak type is `forecasting` en gebruikt een vergelijk bare algoritme groep als `regression` taken) bepalen de lijst met modellen die moeten worden toegepast. Gebruik de `whitelist` `blacklist` para meters of om iteraties verder te wijzigen met de beschik bare modellen die moeten worden opgenomen of uitgesloten. De lijst met ondersteunde modellen vindt u in de [SupportedModels-klasse](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) voor ([classificatie](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), [prognose](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)en [regressie](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)).

Om te voor komen dat time-outstoringen experimenteren, moet de validatie service voor automatische MILLILITERs `experiment_timeout_minutes` worden ingesteld op mini maal 15 minuten of 60 minuten als uw rij op kolom grootte groter is dan 10.000.000.

### <a name="primary-metric"></a>Primaire metriek
De primaire meet waarde bepaalt de metrische gegevens die moeten worden gebruikt tijdens de model training voor Optima Lise ring. De beschik bare metrische gegevens die u kunt selecteren, worden bepaald door het taak type dat u kiest, en in de volgende tabel worden geldige primaire metrische gegevens weer gegeven voor elk taak type.

|Classificatie | Regressie | Tijdreeksvoorspelling
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Meer informatie over de specifieke definities van deze metrische gegevens in [inzicht in geautomatiseerde machine learning resultaten](how-to-understand-automated-ml.md).

### <a name="data-featurization"></a>Gegevens parametrisatie

In elk automatisch machine learning experiment worden uw gegevens [automatisch geschaald en genormaliseerd](how-to-configure-auto-features.md#) om *bepaalde* algoritmen te helpen die gevoelig zijn voor functies die op verschillende schalen zijn.  U kunt echter ook extra parametrisatie inschakelen, zoals ontbrekende waarden, code ring en trans formaties.

Wanneer u uw experimenten in uw `AutoMLConfig` object configureert, kunt u de instelling in-of uitschakelen `featurization` . De volgende tabel bevat de geaccepteerde instellingen voor parametrisatie in de [klasse AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

|Parametrisatie-configuratie | Beschrijving |
| ------------- | ------------- |
|`"featurization": 'auto'`| Geeft aan dat als onderdeel van de voor verwerking, [gegevens Guardrails en parametrisatie-stappen](how-to-configure-auto-features.md#featurization) automatisch worden uitgevoerd. **Standaardinstelling**|
|`"featurization": 'off'`| Hiermee wordt aangegeven dat de parametrisatie-stap niet automatisch moet worden uitgevoerd.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Hiermee wordt aangegeven dat er een aangepaste parametrisatie-stap moet worden gebruikt. [Meer informatie over het aanpassen van parametrisatie](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Automatische machine learning parametrisatie stappen (functie normalisatie, het verwerken van ontbrekende gegevens, het converteren van tekst naar numerieke waarde, enzovoort) worden onderdeel van het onderliggende model. Wanneer u het model gebruikt voor voor spellingen, worden dezelfde parametrisatie-stappen die tijdens de training worden toegepast, automatisch toegepast op de invoer gegevens.

### <a name="time-series-forecasting"></a>Tijdreeksvoorspelling
De time series- `forecasting` taak vereist extra para meters in het configuratie object:

1. `time_column_name`: De vereiste para meter voor het definiëren van de naam van de kolom in uw trainings gegevens met een geldige time-reeks.
1. `max_horizon`: Hiermee definieert u de tijds duur die u wilt voors pellen op basis van de periodiciteit van de trainings gegevens. Als u bijvoorbeeld trainings gegevens met dagelijkse tijd korrels hebt, definieert u hoe ver in dagen u het model wilt trainen.
1. `grain_column_names`: Hiermee definieert u de naam van kolommen die afzonderlijke time series-gegevens bevatten in uw trainings gegevens. Als u bijvoorbeeld de verkoop van een bepaald merk per winkel wilt ramen, definieert u de kolommen Store en merk als korrel. Er worden afzonderlijke time-series en prognoses voor elke korrel/groepering gemaakt. 

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

### <a name="ensemble-configuration"></a><a name="ensemble"></a>Configuratie van ensemble

Ensemble-modellen zijn standaard ingeschakeld en worden weer gegeven als de laatste uitvoerings herhalingen in een automatische machine learning uitvoering. Momenteel worden op dit moment ondersteunde ensemble-methoden gestemd en gestapeld. Stemmen wordt geïmplementeerd als zachte stem met gewogen gemiddelden en de stacking-implementatie maakt gebruik van een implementatie met twee lagen, waarbij de eerste laag dezelfde modellen heeft als de naam van de stem ensemble en het tweede laag model wordt gebruikt om de optimale combi natie van modellen van de eerste laag te vinden. Als u gebruikmaakt van ONNX-modellen **of** als u model uitleg hebt ingeschakeld, wordt stacking uitgeschakeld en wordt alleen de stem gebruikt.

Er zijn meerdere standaard argumenten die kunnen worden gegeven als `kwargs` in een- `AutoMLConfig` object om het standaard ensemble-gedrag te wijzigen.

* `ensemble_download_models_timeout_sec`: Tijdens het genereren van het **VotingEnsemble** -en **StackEnsemble** -model worden er meerdere modellen van de vorige onderliggende uitvoeringen gedownload. Als deze fout optreedt: `AutoMLEnsembleException: Could not find any models for running ensembling` , moet u mogelijk meer tijd opgeven om de modellen te downloaden. De standaard waarde is 300 seconden voor het gelijktijdig downloaden van deze modellen en er is geen maximale time-outlimiet. Configureer deze para meter met een hogere waarde dan 300 seconden, als er meer tijd nodig is. 

  > [!NOTE]
  >  Als de time-out is bereikt en er modellen zijn gedownload, wordt de ensembling-out voortgezet met zo veel modellen die zijn gedownload. Het is niet vereist dat alle modellen moeten worden gedownload om binnen die time-out te volt ooien.

De volgende para meters zijn alleen van toepassing op **StackEnsemble** -modellen: 

* `stack_meta_learner_type`: de meta-informatieer is een model dat is getraind op de uitvoer van de afzonderlijke heterogene modellen. Standaard-meta informatie is `LogisticRegression` voor classificatie taken (of `LogisticRegressionCV` als kruis validatie is ingeschakeld) en `ElasticNet` voor regressie-en prognose taken (of `ElasticNetCV` als kruis validatie is ingeschakeld). Deze para meter kan een van de volgende teken reeksen zijn:,,,,, `LogisticRegression` `LogisticRegressionCV` `LightGBMClassifier` `ElasticNet` `ElasticNetCV` `LightGBMRegressor` , of `LinearRegression` .

* `stack_meta_learner_train_percentage`: Hiermee geeft u het aandeel van de Trainingsset (bij het kiezen van trein en validatie type training) op dat moet worden gereserveerd voor de training van de meta-informatieer. De standaard waarde is `0.2` . 

* `stack_meta_learner_kwargs`: optionele para meters die moeten worden door gegeven aan de initialisatie functie van de meta-informatieer. Met deze para meters en parameter typen worden de para meters en parameter typen van de bijbehorende model-constructor gespiegeld en doorgestuurd naar de model-constructor.

De volgende code toont een voor beeld van het opgeven van een aangepast ensemble-gedrag in een `AutoMLConfig` object.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
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

Een ensemble-training is standaard ingeschakeld, maar kan worden uitgeschakeld met behulp van de `enable_voting_ensemble` `enable_stack_ensemble` para meters en Booleaanse waarden.

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

Voor automatische MILLILITERs maakt u een `Experiment` -object dat een benoemd object is in een `Workspace` gebruikt om experimenten uit te voeren.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Dien het experiment in om een model uit te voeren en te genereren. Geef de `AutoMLConfig` methode door aan `submit` om het model te genereren.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Afhankelijkheden worden eerst op een nieuwe computer geïnstalleerd.  Het kan tot tien minuten duren voordat uitvoer wordt weer gegeven.
>Instelling `show_output` `True` van resultaten die in uitvoer wordt weer gegeven op de-console.

### <a name="exit-criteria"></a><a name="exit"></a>Afsluit criteria

Er zijn enkele opties die u kunt definiëren om uw experiment te beëindigen.
1. Geen criteria: als u geen afsluit parameters definieert, wordt het experiment voortgezet totdat er geen verdere voortgang wordt gemaakt op uw primaire metriek.
1. Afsluiten na een periode: `experiment_timeout_minutes` Als u in uw instellingen gebruikt, kunt u opgeven hoelang in minuten een experiment moet worden uitgevoerd.
1. Afsluiten nadat een score is bereikt: met `experiment_exit_score` wordt het experiment voltooid nadat een primaire meet Score is bereikt.

### <a name="explore-model-metrics"></a>Model statistieken verkennen

U kunt uw trainings resultaten weer geven in een widget of inline als u zich in een notebook bevindt. Zie [modellen volgen en evalueren](how-to-track-experiments.md#view-run-details) voor meer informatie.

## <a name="understand-automated-ml-models"></a>Meer informatie over geautomatiseerde modellen van ML

Elk model dat is gemaakt met behulp van automatische ML omvat de volgende stappen:
+ Geautomatiseerde functie techniek (indien `"featurization": 'auto'` )
+ Schalen/normaliseren en algoritmen met afstemming-waarden

We maken het transparant om deze informatie op te halen uit de fitted_model uitvoer van automatische MILLILITERs.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Geautomatiseerde functie techniek

Bekijk de lijst met voor verwerkings-en [automatische functie techniek]() die zich voordoet wanneer `"featurization": 'auto'` .

Bekijk dit voor beeld:
+ Er zijn vier invoer functies: A (numeriek), B (numeriek), C (numeriek), D (DateTime)
+ De numerieke functie C wordt verwijderd omdat deze een ID-kolom met alle unieke waarden bevat
+ De numerieke functies A en B bevatten ontbrekende waarden en worden daarom toegerekend aan het gemiddelde
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

   |Uitvoer|Definitie|
   |----|--------|
   |RawFeatureName|Invoer functie/kolom naam van de opgegeven gegevensset.|
   |TypeDetected|Het gegevens type van de invoer functie is gedetecteerd.|
   |Minder|Hiermee wordt aangegeven of de invoer functie is verwijderd of gebruikt.|
   |EngineeringFeatureCount|Aantal functies dat wordt gegenereerd via geautomatiseerde functie technische trans formaties.|
   |Transformaties|Lijst met trans formaties die zijn toegepast op de invoer functies voor het genereren van ontworpen functies.|
### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Schalen/normaliseren en algoritmen met afstemming-waarden:

Gebruik fitted_model om inzicht te krijgen in de waarden voor schalen/normalisatie en algoritme/afstemming voor een pijp lijn. Meer [informatie over schalen/normalisatie](). Hier volgt een voorbeeld van uitvoer:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Voor meer informatie gebruikt u deze hulp functie: 

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


print_model(model)
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

Modellen die zijn gemaakt met behulp van automatische ML, bevatten alle wrapper-objecten die de functionaliteit van de open-source klasse Origin spie gelen. De meeste wrapper-objecten die worden geretourneerd door automatische MILLILITERs implementeren de `predict_proba()` functie, die een matrix-achtige of Sparse matrix gegevens voorbeeld van uw functies (X-waarden) accepteert en een n-dimensionale matrix van elk voor beeld en de bijbehorende klasse-kans retourneert.

Als u het beste uitvoeren en het model hebt opgehaald met behulp van de bovenstaande oproepen, kunt u `predict_proba()` rechtstreeks vanuit het ingebouwde model aanroepen, waarbij u een `X_test` voor beeld in de juiste indeling levert, afhankelijk van het model type.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Als het onderliggende model de functie niet ondersteunt `predict_proba()` of als de indeling onjuist is, wordt een model klasse-specifieke uitzonde ring gegenereerd. Zie de documentatie voor [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) en [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) voor voor beelden van hoe deze functie voor verschillende model typen wordt geïmplementeerd.

<a name="explain"></a>

## <a name="model-interpretability"></a>Interpreteerbaarheid van modellen

Met de functie voor het interpreteren van modellen kunt u begrijpen waarom de voor spellingen en de onderliggende waarden van de functie belang rijk zijn. De SDK bevat verschillende pakketten voor het inschakelen van functies voor het interpreteren van modellen, zowel tijdens trainingen als voor het afleiden van tijd, voor lokale en geïmplementeerde modellen.

Raadpleeg de [procedures](how-to-machine-learning-interpretability-automl.md) voor code voorbeelden voor het inschakelen van de functies voor het door verwijzen van voorzieningen, specifiek binnen automatische machine learning experimenten.

Voor algemene informatie over hoe model toelichtingen en functie belang kunnen worden ingeschakeld op andere gebieden van de SDK buiten automatische machine learning, raadpleegt u het [concept](how-to-machine-learning-interpretability.md) artikel over de interpretatie mogelijkheden.

## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over [hoe en waar een model moet worden geïmplementeerd](how-to-deploy-and-where.md).

+ Meer informatie over [het trainen van een regressie model met geautomatiseerde machine learning](tutorial-auto-train-models.md) of [hoe u het gebruik van geautomatiseerde machine learning op een externe bron kunt trainen](how-to-auto-train-remote.md).
+ Meer informatie over het trainen van meerdere modellen met autoML in de [vele modellen oplossings versneller](https://aka.ms/many-models).
