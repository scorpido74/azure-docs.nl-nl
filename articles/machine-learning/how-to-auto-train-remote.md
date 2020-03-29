---
title: Geautomatiseerde ML remote compute targets
titleSuffix: Azure Machine Learning
description: Meer informatie over het bouwen van modellen met behulp van geautomatiseerde machine learning op een Azure Machine Learning remote compute target met Azure Machine Learning
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 9e499d609a3f78dc5f422b9ed90df09be30f2e7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080408"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Modellen trainen met geautomatiseerde machine learning in de cloud

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In Azure Machine Learning traint u uw model op verschillende typen rekenresources die u beheert. Het rekendoel kan een lokale computer of een bron in de cloud zijn.

U uw machine learning-experiment eenvoudig opschalen of schalen door extra rekendoelen toe te voegen, zoals Azure Machine Learning Compute (AmlCompute). AmlCompute is een infrastructuur voor beheerde berekeningen waarmee u eenvoudig een berekening met één of meerdere knooppunten kunt maken.

In dit artikel leert u hoe u een model bouwen met behulp van geautomatiseerde ML met AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>Hoe verschilt de afstandsbediening van lokaal?

De tutorial[Train een classificatiemodel met geautomatiseerde machine learning](tutorial-auto-train-models.md)leert je hoe je een lokale computer gebruiken om een model te trainen met geautomatiseerde ML. De workflow bij het lokaal trainen geldt ook voor externe doelen. Echter, met remote compute, geautomatiseerde ML experiment iteraties worden asynchroon uitgevoerd. Met deze functionaliteit u een bepaalde iteratie annuleren, de status van de uitvoering bekijken of blijven werken aan andere cellen in het Jupyter-notitieblok. Als u op afstand wilt trainen, maakt u eerst een extern rekendoel zoals AmlCompute. Vervolgens configureert u de externe bron en dient u daar uw code in.

In dit artikel ziet u de extra stappen die nodig zijn om een geautomatiseerd ML-experiment uit te voeren op een extern AmlCompute-doel. Het werkruimteobject `ws`van de zelfstudie wordt hier in de hele code gebruikt.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Bron maken

Maak [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29?view=azure-ml-py) het doel in`ws`uw werkruimte ( ) als het nog niet bestaat.

**Tijdsschatting**: Het maken van het AmlCompute-doel duurt ongeveer 5 minuten.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

U het `compute_target` object nu gebruiken als het externe rekendoel.

Clusternaambeperkingen zijn onder meer:
+ Moet korter zijn dan 64 tekens.
+ Kan geen van de `\` volgende tekens bevatten: ~ ! @ # $ % ^ & * ( \\ \\ ) = [ ] [ ] } | ; : \' \\" , < > / ?. `

## <a name="access-data-using-tabulardataset-function"></a>Toegang tot gegevens met de functie TabularDataset

Gedefinieerd training_data [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) als en het etiket, die worden [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py)doorgegeven aan Automated ML in de . De `TabularDataset` `from_delimited_files`methode stelt standaard `infer_column_types` het true in, waardoor het kolommentype automatisch wordt ingesteld. 

Als u de kolomtypen handmatig wilt instellen, `set_column_types` kunt u het argument instellen om het type van elke kolom handmatig in te stellen. In het volgende codevoorbeeld komen de gegevens uit het sklearn-pakket.

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
    
if not os.path.exists('project_folder'):
    os.makedirs('project_folder')

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

## <a name="configure-experiment"></a>Experiment configureren
Geef de `AutoMLConfig`instellingen op voor .  (Zie een [volledige lijst met parameters](how-to-configure-auto-train.md#configure-experiment) en hun mogelijke waarden.)

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

Dien nu de configuratie in om automatisch het algoritme, hyperparameters te selecteren en het model te trainen.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

U ziet uitvoer vergelijkbaar met het volgende voorbeeld:

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


## <a name="explore-results"></a>Bekijk de resultaten

U dezelfde [Jupyter-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) gebruiken zoals in [de trainingszelfstudie](tutorial-auto-train-models.md#explore-the-results) wordt weergegeven om een grafiek en een tabel met resultaten te zien.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Hier ziet u een statische afbeelding van de widget.  In het notitieblok u op elke regel in de tabel klikken om uitvoereigenschappen en uitvoerlogboeken voor die run te bekijken.   U de vervolgkeuzelijst boven de grafiek ook gebruiken om een grafiek van elke beschikbare statistiek voor elke iteratie weer te geven.

![tabel van widget](./media/how-to-auto-train-remote/table.png)
![grafiek van widget](./media/how-to-auto-train-remote/plot.png)

De widget geeft een URL weer die u gebruiken om de afzonderlijke rundetails te bekijken en te verkennen.  

Als u zich niet in een Jupyter-notitieblok bevindt, u de URL van de run zelf weergeven:

```
remote_run.get_portal_url()
```

Dezelfde informatie is beschikbaar in uw werkruimte.  Zie [Geautomatiseerde machine learning-resultaten begrijpen](how-to-understand-automated-ml.md)voor meer informatie over deze resultaten.

## <a name="example"></a>Voorbeeld

Het volgende [notitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb) toont concepten in dit artikel.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het configureren van instellingen voor automatische training](how-to-configure-auto-train.md).
* Bekijk de [how-to](how-to-machine-learning-interpretability-automl.md) on inschakelen van modelinterpreteerbaarheidsfuncties in geautomatiseerde ML-experimenten.
