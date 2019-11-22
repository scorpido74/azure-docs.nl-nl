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
ms.openlocfilehash: 8c50a1ba79fc07f62e319c6dceb75c32a9e8609f
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287128"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Trainen van modellen met geautomatiseerde machine learning in de cloud

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In Azure Machine Learning, door uw model op verschillende soorten compute-resources die u beheert te trainen. Het Compute-doel kan een lokale computer of een bron in de Cloud zijn.

U kunt uw machine learning experiment eenvoudig opschalen of uitschalen door extra reken doelen toe te voegen, zoals Azure Machine Learning Compute (AmlCompute). AmlCompute is een infra structuur voor beheerde berekeningen waarmee u eenvoudig een enkele of meerdere knoop punten kunt maken.

In dit artikel leert u hoe u een model bouwt met behulp van geautomatiseerde MILLILITERs met AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>Hoe verschilt afstand van lokale?

In de zelf studie "[een classificatie model trainen met geautomatiseerde machine learning](tutorial-auto-train-models.md)" leert u hoe u een lokale computer kunt gebruiken om een model met automatische ml te trainen. De werkstroom bij het trainen van lokaal ook van toepassing is ook externe doelen. Echter met externe compute geautomatiseerde ML-iteraties uitgevoerd asynchroon. Deze functie kunt u een bepaalde iteratie annuleren, bekijk de status van de uitvoering of blijven werken van andere cellen in de Jupyter-notebook. Als u op afstand wilt trainen, maakt u eerst een extern Compute-doel zoals AmlCompute. Vervolgens de externe bron te configureren en verzenden van uw code er.

In dit artikel worden de extra stappen beschreven die nodig zijn voor het uitvoeren van een geautomatiseerd experiment op een extern AmlCompute-doel. Een object in de werkruimte `ws`, uit de zelfstudie wordt gebruikt in de code hier.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Bron maken

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

U kunt nu de `compute_target` object als de externe compute-doel.

De beperkingen voor de cluster naam zijn onder andere:
+ Moet korter zijn dan 64 tekens lang zijn.
+ De volgende tekens niet bevatten: `\` ~! @ # $ % ^ & * () = + [] {} van _ \\ \\ |;: \' \\', in combinatie /?. `

## <a name="access-data-using-tabulardataset-function"></a>Toegang tot gegevens met de functie TabularDataset

Gedefinieerd training_data als `TabularDataset`en het label, dat wordt door gegeven aan geautomatiseerd ML in de AutoMLConfig. `from_delimited_files` standaard wordt de `infer_column_types` ingesteld op True, waardoor het kolommen type automatisch wordt afleiden. 

Als u de kolom typen hand matig wilt instellen, kunt u het argument `set_column_types` instellen om het type van elke kolom hand matig in te stellen. In het volgende codevoorbeeld wordt de gegevens zijn afkomstig uit het pakket sklearn.

```python
from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd
import os

# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

X = pd.DataFrame(data_train.data[100:,:])
y = pd.DataFrame(data_train.target[100:])

# merge X and y
label = "digit"
X[label] = y

training_data = X

training_data.to_csv('data/digits.csv')
ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

training_data = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/digits.csv'))
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
    pip_packages=["scikit-learn", "scipy", "numpy==1.16.2"])
run_config.environment.python.conda_dependencies = dependencies
```

Bekijk dit [voorbeeld notitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) voor een extra voor beeld van dit ontwerp patroon.

## <a name="configure-experiment"></a>Configureren van experiment
Geef de instellingen voor `AutoMLConfig`.  (Zie een [volledige lijst met parameters](how-to-configure-auto-train.md#configure-experiment) en hun mogelijke waarden.)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "experiment_timeout_minutes" : 20,
    "enable_early_stopping" : True,
    "iteration_timeout_minutes": 10,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "max_concurrent_iterations": 10,
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             training_data=training_data,
                             label_column_name=label,
                             **automl_settings,
                             )
```

## <a name="submit-training-experiment"></a>Trainingsexperiment verzenden

Nu de configuratie voor het automatisch selecteren van het algoritme, de hyper-parameters indienen en het model te trainen.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

Hier ziet u uitvoer die vergelijkbaar is met het volgende voorbeeld:

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

Hier ziet u een statische afbeelding van de widget.  In het notitieblok, kunt u klikken op elke regel in de tabel om te zien van de eigenschappen voor de uitvoerbewerking en uitvoer van Logboeken voor die worden uitgevoerd.   U kunt ook de vervolgkeuzelijst boven de grafiek gebruiken om een grafiek van elke beschikbare metrische gegevens voor elke herhaling van weer te geven.

![tabel van widget](./media/how-to-auto-train-remote/table.png)
![grafiek van widget](./media/how-to-auto-train-remote/plot.png)

De widget wordt weergegeven een URL die u gebruiken kunt om te zien en de details uitvoering van afzonderlijke verkennen.  

Als u zich niet in een Jupyter-notebook bevindt, kunt u de URL van de uitvoeringsrun zelf weer geven:

```
remote_run.get_portal_url()
```

Dezelfde informatie is beschikbaar in uw werk ruimte.  Zie [inzicht in geautomatiseerde machine learning resultaten](how-to-understand-automated-ml.md)voor meer informatie over deze resultaten.

## <a name="example"></a>Voorbeeld

In het volgende [notitie blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb) worden concepten in dit artikel gedemonstreerd.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

* Informatie over [over het configureren van instellingen voor automatische training](how-to-configure-auto-train.md).
* Bekijk de [functie voor het inschakelen](how-to-machine-learning-interpretability-automl.md) van model interpret functies in geautomatiseerde ml experimenten.
