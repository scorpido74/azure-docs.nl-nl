---
title: Een diepe Learning Neural-netwerk trainen met PyTorch
titleSuffix: Azure Machine Learning
description: Meer informatie over het uitvoeren van uw PyTorch-trainings scripts op ENTER prise Scale met behulp van de PyTorch Estimator-klasse van Azure Machine Learning.  De voorbeeld scripts classificeren de installatie kopieën van kippen en Turkije om een diep gaande Neural-netwerk te bouwen op basis van de zelf studie over de overdracht van PyTorch.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 0c3157d3ff020fd8c4974bf694b9a96d98e83c58
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489453"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Pytorch diepe Learning-modellen op schaal trainen met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u uw [PyTorch](https://pytorch.org/) -trainings scripts kunt uitvoeren op ENTER prise Scale met behulp van de [PyTorch Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) -klasse van Azure machine learning.  

De voorbeeld scripts in dit artikel worden gebruikt voor het classificeren van kippen en Turkije installatie kopieën om een diep gaande Neural-netwerk te bouwen op basis van de [zelf studie](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)over de overdracht van PyTorch. 

Of u nu een diep Learning PyTorch-model traint of een bestaand model in de Cloud brengt, u kunt Azure Machine Learning gebruiken om open-source trainings taken te schalen met behulp van elastische Cloud Compute-resources. U kunt modellen voor productie kwaliteit bouwen, implementeren, versie en bewaken met Azure Machine Learning. 

Meer informatie over [uitgebreide kennis en machine learning](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Vereisten

Voer deze code uit in een van de volgende omgevingen:

 - Azure Machine Learning Compute-instantie-geen down loads of installatie vereist

    - Voltooi de [zelf studie: installatie omgeving en werk ruimte](tutorial-1st-experiment-sdk-setup.md) om een toegewezen notebook server te maken vooraf geladen met de SDK en de voor beeld-opslag plaats.
    - Zoek in de map met uitgebreide trainingen op de notebook server een volledig en uitgebreid notitie blok door naar deze map te navigeren: **instructies-to-use-azureml > training-met-diepe learning > Train-afstemming-Tune-Deploy-with-pytorch** -map. 
 
 - Uw eigen Jupyter Notebook-server

    - [Installeer de Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Maak een configuratie bestand voor de werk ruimte](how-to-configure-environment.md#workspace).
    - [De voorbeeld script bestanden downloaden](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    U kunt ook een voltooide [Jupyter notebook versie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) van deze hand leiding vinden op de pagina met voor beelden van github. Het notitie blok bevat uitgebreide secties die betrekking hebben op intelligent afstemming tuning, model implementatie en notebook widgets.

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie wordt het trainings experiment opgesteld door het laden van de vereiste Python-pakketten, het initialiseren van een werk ruimte, het maken van een experiment en het uploaden van de trainings gegevens en trainings scripts.

### <a name="import-packages"></a>Pakketten importeren

Importeer eerst de benodigde python-bibliotheken.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>Een werk ruimte initialiseren

De [Azure machine learning werk ruimte](concept-workspace.md) is de resource op het hoogste niveau voor de service. Het biedt u een centrale locatie voor het werken met alle artefacten die u maakt. In de python-SDK hebt u toegang tot de werkruimte artefacten door een [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) -object te maken.

Maak een werkruimte object vanuit het `config.json` bestand dat in de [sectie vereisten](#prerequisites)is gemaakt.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Een diep leer experiment maken

Maak een experiment en een map om uw trainings scripts te bewaren. In dit voor beeld maakt u een experiment met de naam ' pytorch-vogels '.

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>De gegevens ophalen

De gegevensset bestaat uit ongeveer 120 trainings afbeeldingen voor kalk oenen en kuikens, met 100-validatie kopieën voor elke klasse. We zullen de gegevensset downloaden en uitpakken als onderdeel van het trainings script `pytorch_train.py`. De installatie kopieën zijn een subset van de [Open Image V5-gegevensset](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Trainings scripts voorbereiden

In deze zelf studie is het trainings script, `pytorch_train.py`, al opgenomen. In de praktijk kunt u een aangepast trainings script uitvoeren, zoals dat is, en dit met Azure Machine Learning.

Upload het Pytorch-trainings script, `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Als u echter de mogelijkheden Azure Machine Learning bijhouden en metrische gegevens wilt gebruiken, moet u een kleine code in uw trainings script toevoegen. Voor beelden van het bijhouden van metrische gegevens vindt u in `pytorch_train.py`.

## <a name="create-a-compute-target"></a>Een reken doel maken

Maak een compute-doel voor uw PyTorch-taak om uit te voeren. In dit voor beeld maakt u een Azure Machine Learning Compute-cluster waarvoor GPU is ingeschakeld.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Zie voor meer informatie over Compute-doelen het artikel [Wat is een reken doel](concept-compute-target.md) .

## <a name="create-a-pytorch-estimator"></a>Een PyTorch-Estimator maken

De [PyTorch Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) biedt een eenvoudige manier om een PyTorch-trainings taak te starten op een compute-doel.

De PyTorch Estimator wordt geïmplementeerd via de algemene [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klasse, die kan worden gebruikt ter ondersteuning van een Framework. Voor meer informatie over trainings modellen die gebruikmaken van de algemene Estimator, raadpleegt [u modellen met Azure machine learning met behulp van Estimator](how-to-train-ml-models.md)

Als uw trainings script extra PIP-of Conda-pakketten nodig heeft om uit te voeren, kunt u de pakketten op de resulterende docker-installatie kopie installeren door hun namen door te geven via de argumenten `pip_packages` en `conda_packages`.

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

## <a name="submit-a-run"></a>Een run verzenden

Het [object run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biedt de interface voor de uitvoerings geschiedenis terwijl de taak wordt uitgevoerd en nadat deze is voltooid.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Wanneer de uitvoering wordt uitgevoerd, worden de volgende fasen door lopen:

- **Voorbereiden**: een docker-installatie kopie wordt gemaakt op basis van de PyTorch-Estimator. De afbeelding wordt geüpload naar het container register van de werk ruimte en opgeslagen in de cache voor latere uitvoeringen. Logboeken worden ook gestreamd naar de uitvoerings geschiedenis en kunnen worden weer gegeven om de voortgang te bewaken.

- **Schalen**: het cluster probeert omhoog te schalen als het batch AI-cluster meer knoop punten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar zijn.

- **Uitvoeren**: alle scripts in de map script worden geüpload naar het Compute-doel, gegevens archieven worden gekoppeld of gekopieerd en de entry_script wordt uitgevoerd. Uitvoer van stdout en de map./logs worden gestreamd naar de uitvoerings geschiedenis en kunnen worden gebruikt om de uitvoering te bewaken.

- **Na de verwerking**: de map./outputs van de uitvoering wordt gekopieerd naar de uitvoerings geschiedenis.

## <a name="register-or-download-a-model"></a>Een model registreren of downloaden

Zodra u het model hebt getraind, kunt u het registreren in uw werk ruimte. Met model registratie kunt u uw modellen in uw werk ruimte opslaan en versieren om het [model beheer en de implementatie](concept-model-management-and-deployment.md)te vereenvoudigen.

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

U kunt ook een lokale kopie van het model downloaden met behulp van het object run. In het trainings script `pytorch_train.py`een PyTorch-object opslaan het model persistent maken naar een lokale map (lokaal naar het Compute-doel). U kunt het object run gebruiken om een kopie te downloaden.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Gedistribueerde training

De [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) Estimator biedt ook ondersteuning voor gedistribueerde training over CPU-en GPU-clusters. U kunt eenvoudig gedistribueerde PyTorch-taken uitvoeren en de indeling wordt door Azure Machine Learning beheerd.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) is een open source-, alle verlaagde structuur voor gedistribueerde training ontwikkeld door uber. Het biedt een eenvoudig pad naar gedistribueerde GPU PyTorch-taken.

Als u Horovod wilt gebruiken, geeft u een [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) -object op voor de `distributed_training`-para meter in de PyTorch-constructor. Deze para meter zorgt ervoor dat de Horovod-bibliotheek wordt geïnstalleerd zodat u deze kunt gebruiken in uw trainings script.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod en de bijbehorende afhankelijkheden worden voor u geïnstalleerd. u kunt deze als volgt importeren in uw trainings script `train.py`:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Exporteren naar ONNX

Converteer uw getrainde PyTorch-model naar de ONNX-indeling om de deinterferentie te optimaliseren met de [ONNX-runtime](concept-onnx.md). Defactorion of model Score is de fase waarin het geïmplementeerde model wordt gebruikt voor de voor spelling, meestal op productie gegevens. Raadpleeg de [zelf studie](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) voor een voor beeld.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een diep gaande Neural-netwerk getraind en geregistreerd met behulp van PyTorch in Azure Machine Learning. Ga verder met ons model implementatie artikel voor meer informatie over het implementeren van een model.

> [!div class="nextstepaction"]
> [Hoe en waar modellen moeten worden geïmplementeerd](how-to-deploy-and-where.md)
* [Metrische uitvoerings gegevens tijdens de training volgen](how-to-track-experiments.md)
* [Hyper parameters afstemmen](how-to-tune-hyperparameters.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)
* [Referentie architectuur voor gedistribueerde training van diep gaande lessen in azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
