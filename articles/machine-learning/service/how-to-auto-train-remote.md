---
title: Automated ML externe Compute-doelen
titleSuffix: Azure Machine Learning
description: Meer informatie over het bouwen van modellen met behulp van geautomatiseerde machine learning op een Azure Machine Learning extern Compute-doel met Azure Machine Learning
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4276a713e62f96cc5340fc7be0e8391939d32342
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497316"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Modellen trainen met automatische machine learning in de Cloud

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In Azure Machine Learning traint u uw model op verschillende soorten reken bronnen die u beheert. Het Compute-doel kan een lokale computer of een bron in de Cloud zijn.

U kunt uw machine learning experiment eenvoudig opschalen of uitschalen door extra reken doelen toe te voegen, zoals Azure Machine Learning Compute (AmlCompute). AmlCompute is een infra structuur voor beheerde berekeningen waarmee u eenvoudig een enkele of meerdere knoop punten kunt maken.

In dit artikel leert u hoe u een model bouwt met behulp van geautomatiseerde MILLILITERs met AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>Hoe verschilt extern van lokaal?

In de zelf studie "[een classificatie model trainen met geautomatiseerde machine learning](tutorial-auto-train-models.md)" leert u hoe u een lokale computer kunt gebruiken om een model met automatische ml te trainen. De werk stroom wanneer training lokaal ook van toepassing is op externe doelen. Met externe Compute worden automatisch experimenten van het experiment asynchroon uitgevoerd. Met deze functie kunt u een bepaalde herhaling annuleren, de status van de uitvoering bekijken of door gaan met het werken aan andere cellen in het Jupyter-notitie blok. Als u op afstand wilt trainen, maakt u eerst een extern Compute-doel zoals AmlCompute. Vervolgens configureert u de externe resource en verzendt u de code daar.

In dit artikel worden de extra stappen beschreven die nodig zijn voor het uitvoeren van een geautomatiseerd experiment op een extern AmlCompute-doel. Het werkruimte object, `ws`, uit de zelf studie wordt in de hele code gebruikt.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Resource maken

Maak het doel van de AmlCompute in uw werk ruimte (`ws`) als deze nog niet bestaat.

**Geschatte tijd**: het maken van het AmlCompute-doel duurt ongeveer 5 minuten.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

amlcompute_cluster_name = "automlcl"  # Name your cluster
provisioning_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            # for GPU, use "STANDARD_NC6"
                                                            # vm_priority = 'lowpriority', # optional
                                                            max_nodes=6)
compute_target = ComputeTarget.create(
    ws, amlcompute_cluster_name, provisioning_config)

# Can poll for a minimum number of nodes and for a specific timeout.
# If no min_node_count is provided, it will use the scale settings for the cluster.
compute_target.wait_for_completion(
    show_output=True, min_node_count=None, timeout_in_minutes=20)
```

U kunt nu het `compute_target`-object gebruiken als het externe Compute-doel.

De beperkingen voor de cluster naam zijn onder andere:
+ Moet korter zijn dan 64 tekens.
+ Kan geen van de volgende tekens bevatten: `\` ~! @ # $% ^ & * () = + _ [] {} \\\\ |; : \' \\", < >/?. `

## <a name="access-data-using-tabulardataset-function"></a>Toegang tot gegevens met de functie TabularDataset

X en y gedefinieerd als `TabularDataset`s, die worden door gegeven aan geautomatiseerd ML in de AutoMLConfig. `from_delimited_files` standaard wordt de `infer_column_types` ingesteld op True, waardoor het kolommen type automatisch wordt afleiden. 

Als u de kolom typen hand matig wilt instellen, kunt u het argument `set_column_types` instellen om het type van elke kolom hand matig in te stellen. In het volgende code voorbeeld zijn de gegevens afkomstig uit het sklearn-pakket.

```python
# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd

data_train = datasets.load_digits()

pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

X = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/X_train.csv'))
y = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/y_train.csv'))

```

## <a name="create-run-configuration"></a>Configuratie voor uitvoeren maken

Als u afhankelijkheden beschikbaar wilt maken voor het script get_data. py, definieert u een `RunConfiguration`-object met gedefinieerde `CondaDependencies`. Gebruik dit object voor de para meter `run_configuration` in `AutoMLConfig`.

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config = RunConfiguration(framework="python")
run_config.target = compute_target
run_config.environment.docker.enabled = True
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

dependencies = CondaDependencies.create(
    pip_packages=["scikit-learn", "scipy", "numpy"])
run_config.environment.python.conda_dependencies = dependencies
```

Bekijk dit [voorbeeld notitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) voor een extra voor beeld van dit ontwerp patroon.

## <a name="configure-experiment"></a>Experiment configureren
Geef de instellingen voor `AutoMLConfig`op.  (Zie een [volledige lijst met para meters](how-to-configure-auto-train.md#configure-experiment) en de bijbehorende mogelijke waarden.)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             )
```

### <a name="enable-model-explanations"></a>Model uitleg inschakelen

Stel de optionele para meter `model_explainability` in de `AutoMLConfig`-constructor in. Daarnaast moet een validatie data frame-object worden door gegeven als een para meter `X_valid` voor het gebruik van de functie voor model uitleg.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             model_explainability=True,
                             X_valid=X_test
                             )
```

## <a name="submit-training-experiment"></a>Trainings experiment verzenden

Verzend de configuratie nu om automatisch het algoritme te selecteren, de Hyper-para meters en het model te trainen.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

De uitvoer ziet er ongeveer uit zoals in het volgende voor beeld:

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************

     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>Resultaten verkennen

U kunt dezelfde Jupyter- [widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) gebruiken zoals weer gegeven in [de trainings zelfstudie](tutorial-auto-train-models.md#explore-the-results) om een grafiek en een tabel met resultaten weer te geven.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Hier ziet u een statische afbeelding van de widget.  In het notitie blok kunt u klikken op een wille keurige regel in de tabel om uitvoerings eigenschappen en uitvoer logboeken voor die uitvoering weer te geven.   U kunt ook de vervolg keuzelijst boven de grafiek gebruiken om een grafiek van elke beschik bare metrische gegevens voor elke iteratie weer te geven.

![tabel van widget](./media/how-to-auto-train-remote/table.png)
![grafiek van widget](./media/how-to-auto-train-remote/plot.png)

Met de widget wordt een URL weer gegeven die u kunt gebruiken om de afzonderlijke details van de uitvoering te bekijken en te verkennen.  

Als u zich niet in een Jupyter-notebook bevindt, kunt u de URL van de uitvoeringsrun zelf weer geven:

```
remote_run.get_portal_url()
```

Dezelfde informatie is beschikbaar in uw werk ruimte.  Zie [inzicht in geautomatiseerde machine learning resultaten](how-to-understand-automated-ml.md)voor meer informatie over deze resultaten.

### <a name="view-logs"></a>Logboeken weergeven

Zoek naar Logboeken op de DSVM onder `/tmp/azureml_run/{iterationid}/azureml-logs`.

## <a name="explain"></a>Best mogelijke model uitleg

Door model uitleg gegevens op te halen, kunt u gedetailleerde informatie over de modellen weer geven om de transparantie van de back-end te verg Roten. In dit voor beeld voert u alleen model verklaringen uit voor het beste model. Als u voor alle modellen in de pijp lijn uitvoert, resulteert dit in een aanzienlijke uitvoerings tijd. Informatie over model uitleg:

* shap_values: de uitleg informatie die is gegenereerd door Shap lib.
* expected_values: de verwachte waarde van het model dat wordt toegepast op set met X_train-gegevens.
* overall_summary: de belang rijke waarden van de functie op model niveau in aflopende volg orde gesorteerd.
* overall_imp: de functie namen worden gesorteerd in dezelfde volg orde als in overall_summary.
* per_class_summary: de belang rijke waarden voor functie niveau zijn in aflopende volg orde gesorteerd. Alleen beschikbaar voor de classificatie case.
* per_class_imp: de functie namen worden gesorteerd in dezelfde volg orde als in per_class_summary. Alleen beschikbaar voor de classificatie case.

Gebruik de volgende code om de beste pijp lijn te selecteren uit uw iteraties. De `get_output`-methode retourneert het beste uitvoeren en het model dat het meest geschikt is voor het aanroepen van de laatste.

```python
best_run, fitted_model = remote_run.get_output()
```

Importeer de functie `retrieve_model_explanation` en voer deze uit op het beste model.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

Resultaten afdrukken voor de `best_run` uitleg variabelen die u wilt weer geven.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

Het afdrukken van de samenvattings variabelen van de `best_run`-uitleg resulteert in de volgende uitvoer.

![Uitvoer van de console voor de uitleg van modellen](./media/how-to-auto-train-remote/expl-print.png)

U kunt het belang van de functie ook visualiseren via de gebruikers interface van de widget of in uw werk ruimte in [Azure machine learning Studio](https://ml.azure.com). 

![Gebruikers interface voor model uitleg](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>Voorbeeld

In de notebook [How-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-Remote-amlcompute. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) worden concepten in dit artikel gedemonstreerd.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie [over het configureren van instellingen voor automatische training](how-to-configure-auto-train.md).
