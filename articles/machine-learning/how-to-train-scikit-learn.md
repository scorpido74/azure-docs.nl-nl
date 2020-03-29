---
title: Train scikit-learn machine learning-modellen
titleSuffix: Azure Machine Learning
description: Lees hoe u uw scikit-learn-trainingsscripts op bedrijfsschaal uitvoeren met de azure machine learning schatter-schatterklasse. De voorbeeldscripts classificeren irisbloemafbeeldingen om een machine learning-model te bouwen op basis van de irisgegevensset van scikit-learn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: bdd2cc400c3df75742689258caea8cb87ee8ccc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942257"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Scikit-learn-modellen op schaal bouwen met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u uw scikit-learn-trainingsscripts op bedrijfsschaal uitvoeren met behulp van de azure machine learning [SKlearn-schatterklasse.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) 

De voorbeeldscripts in dit artikel worden gebruikt om irisbloemafbeeldingen te classificeren om een machine learning-model te bouwen op basis van de [irisgegevensset](https://archive.ics.uci.edu/ml/datasets/iris)van scikit-learn.

Of u nu een model voor machine learning-leren vanaf de basis traint of een bestaand model in de cloud brengt, u Azure Machine Learning gebruiken om open-source trainingstaken uit te schalen met behulp van elastische cloudcomputeresources. U productiemodellen bouwen, implementeren, implementeren en bewaken met Azure Machine Learning.

## <a name="prerequisites"></a>Vereisten

Voer deze code uit op een van deze omgevingen:
 - Azure Machine Learning rekeninstantie - geen downloads of installatie nodig

    - De [zelfstudie: installatieomgeving en werkruimte](tutorial-1st-experiment-sdk-setup.md) voltooien om een speciale notebookserver te maken die vooraf is geladen met de SDK en de voorbeeldopslagplaats.
    - Zoek in de trainingsmap voor voorbeelden op de notebookserver een voltooid en uitgebreid notitieblok door naar deze map te navigeren: **how-to-use-azureml > ml-frameworks > scikit-learn > training > trein-hyperparameter-tune-deploy-with-sklearn** map.

 - Uw eigen Jupyter Notebook-server

    - [Installeer de Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Een configuratiebestand voor werkruimtes maken](how-to-configure-environment.md#workspace).
    - De gegevensset en het voorbeeldscriptbestand downloaden 
        - [irisgegevensset](https://archive.ics.uci.edu/ml/datasets/iris)
        - [train_iris.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - U ook een voltooide [Jupyter Notebook-versie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) van deze handleiding vinden op de pagina GitHub-voorbeelden. De notebook bevat een uitgebreide sectie over intelligente hyperparameter tuning en het ophalen van het beste model door primaire statistieken.

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie wordt het trainingsexperiment ingesteld door de vereiste python-pakketten te laden, een werkruimte te initialiseren, een experiment te maken en de trainingsgegevens en trainingsscripts te uploaden.

### <a name="import-packages"></a>Pakketten importeren

Importeer eerst de benodigde Python-bibliotheken.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Een werkruimte initialiseren

De [Azure Machine Learning-werkruimte](concept-workspace.md) is de bron op het hoogste niveau voor de service. Het biedt u een centrale plek om te werken met alle artefacten die u maakt. In de Python SDK hebt u toegang tot [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) de artefacten van de werkruimte door een object te maken.

Maak een werkruimteobject `config.json` uit het bestand dat is gemaakt in de [sectie Vereistevoorwaarden](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Een machine learning-experiment maken

Maak een experiment en een map om uw trainingsscripts vast te houden. Maak in dit voorbeeld een experiment met de naam "sklearn-iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Trainingsscript voorbereiden

In deze tutorial is het trainingsscript **train_iris.py** al voor u beschikbaar. In de praktijk moet u elk aangepast trainingsscript kunnen nemen zoals het is en het kunnen uitvoeren met Azure ML zonder dat u uw code hoeft te wijzigen.

Als u de azure ML-tracking- en metrische mogelijkheden wilt gebruiken, voegt u een kleine hoeveelheid Azure ML-code toe in uw trainingsscript.  Het trainingsscript **train_iris.py** laat zien hoe u bepaalde `Run` statistieken registreren bij uw Azure ML-run met behulp van het object in het script.

Het meegeleverde trainingsscript maakt `iris = datasets.load_iris()` gebruik van voorbeeldgegevens van de functie.  Voor uw eigen gegevens moet u mogelijk stappen zoals [Gegevensset uploaden en scripts](how-to-train-keras.md#data-upload) gebruiken om gegevens beschikbaar te maken tijdens de training.

Kopieer het trainingsscript **train_iris.py** naar uw projectmap.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Een rekendoel maken of krijgen

Maak een rekendoel voor uw scikit-learn-taak om op uit te voeren. Scikit-learn ondersteunt alleen single node, CPU computing.

Met de volgende code wordt een Azure Machine Learning managed compute (AmlCompute) gemaakt voor uw compute resource voor externe training. Het maken van AmlCompute duurt ongeveer 5 minuten. Als de AmlCompute met die naam zich al in uw werkruimte bevindt, slaat deze code het creatieproces over.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Zie het [artikel wat een compute target is](concept-compute-target.md) voor meer informatie over rekendoelen.

## <a name="create-a-scikit-learn-estimator"></a>Maak een scikit-learn schatter

De [scikit-learn schatter](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) biedt een eenvoudige manier om een scikit-learn trainingstaak te starten op een compute target. Het wordt geïmplementeerd [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) via de klasse, die kan worden gebruikt ter ondersteuning van single-node CPU-training.

Als uw trainingsscript extra pip- of conda-pakketten nodig heeft om uit te voeren, kunt `pip_packages` `conda_packages` u de pakketten op de resulterende dockerafbeelding laten installeren door hun namen door te geven via de en argumenten.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```


Zie [Omgevingen maken en beheren voor training en implementatie voor](how-to-use-environments.md)meer informatie over het aanpassen van uw Python-omgeving. 

## <a name="submit-a-run"></a>Een run verzenden

Het [object Uitvoeren](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biedt de interface naar de rungeschiedenis terwijl de taak wordt uitgevoerd en nadat deze is voltooid.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Als de run wordt uitgevoerd, gaat het door de volgende fasen:

- **Voorbereiden**: Er wordt een dockerafbeelding gemaakt volgens de TensorFlow-schatter. De afbeelding wordt geüpload naar het containerregister van de werkruimte en in de cache opgeslagen voor latere uitvoeringen. Logboeken worden ook gestreamd naar de rungeschiedenis en kunnen worden bekeken om de voortgang te controleren.

- **Schalen:** het cluster probeert op te schalen als het AI-cluster batch meer knooppunten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar is.

- **Actief:** Alle scripts in de scriptmap worden geüpload naar het compute-doel, gegevensarchieven worden gemonteerd of gekopieerd en de entry_script wordt uitgevoerd. Uitvoer van stdout en de map ./logs worden gestreamd naar de rungeschiedenis en kunnen worden gebruikt om de run te controleren.

- **Nabewerking:** de map ./uitvoer van de run wordt gekopieerd naar de rungeschiedenis.

## <a name="save-and-register-the-model"></a>Het model opslaan en registreren

Zodra u het model hebt getraind, u het opslaan en registreren op uw werkruimte. Met modelregistratie u uw modellen opslaan en in uw werkruimte gebruiken om [het modelbeheer en de implementatie](concept-model-management-and-deployment.md)te vereenvoudigen.

Voeg de volgende code toe aan uw trainingsscript, train_iris.py, om het model op te slaan. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registreer het model in uw werkruimte met de volgende code. Door de parameters `model_framework` `model_framework_version`op `resource_configuration`te geven en wordt de implementatie van no-code-modellen beschikbaar. Hiermee u uw model rechtstreeks implementeren als een webservice vanuit het geregistreerde model en definieert het object de [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) compute resource voor de webservice.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Implementatie

Het model dat u zojuist hebt geregistreerd, kan op exact dezelfde manier worden geïmplementeerd als elk ander geregistreerd model in Azure Machine Learning, ongeacht welke schatter u hebt gebruikt voor training. De implementatiehow-to bevat een sectie over het registreren van modellen, maar u rechtstreeks overgaan naar [het maken van een compute target](how-to-deploy-and-where.md#choose-a-compute-target) voor implementatie, omdat u al een geregistreerd model hebt.

### <a name="preview-no-code-model-deployment"></a>(Preview) Implementatie van no-code-model

In plaats van de traditionele implementatieroute u ook de functie voor implementatie zonder code (voorbeeld) gebruiken voor scikit-learn. No-code model implementatie wordt ondersteund voor alle ingebouwde scikit-learn modeltypes. Door uw model te registreren `model_framework` `model_framework_version`zoals `resource_configuration` hierboven weergegeven met de [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) ,, en parameters, u gewoon gebruik maken van de statische functie om uw model te implementeren.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

OPMERKING: Deze afhankelijkheden zijn opgenomen in de vooraf gebouwde scikit-learn inference container.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

De volledige [how-to](how-to-deploy-and-where.md) dekt implementatie in Azure Machine Learning in grotere diepte.


## <a name="next-steps"></a>Volgende stappen

In dit artikel trainde en registreerde u een scikit-learn-model en leerde u over implementatieopties. Bekijk deze andere artikelen voor meer informatie over Azure Machine Learning.

* [Hardloopstatistieken bijhouden tijdens de training](how-to-track-experiments.md)
* [Hyperparameters afstemmen](how-to-tune-hyperparameters.md)
* [Referentiearchitectuur voor gedistribueerde deep learning-training in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
