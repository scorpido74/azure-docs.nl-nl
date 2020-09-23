---
title: Een trainings uitvoering naar een reken doel verzenden
titleSuffix: Azure Machine Learning
description: Train uw machine learning model op verschillende trainings omgevingen (reken doelen). U kunt eenvoudig overschakelen tussen trainings omgevingen. Start training lokaal. Als u wilt uitschalen, gaat u naar een compute-doel op basis van de Cloud.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 8b07d19ca88a2d680a4f9efbb85fcf60b895a2b3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907601"
---
# <a name="submit-a-training-run-to-a-compute-target"></a>Een trainings uitvoering naar een reken doel verzenden

In dit artikel leert u hoe u verschillende trainings omgevingen ([Compute-doelen](concept-compute-target.md)) kunt gebruiken om uw machine learning model te trainen.

Bij het trainen is het gebruikelijk om te beginnen op de lokale computer en dat trainings script later uit te voeren op een ander reken doel. Met Azure Machine Learning kunt u uw script uitvoeren op verschillende reken doelen zonder dat u uw trainings script hoeft te wijzigen.

Het enige wat u hoeft te doen, is het definiëren van de omgeving voor elk reken doel in een **script configuratie uitvoeren**.  Wanneer u uw trainings experiment wilt uitvoeren op een ander Compute-doel, geeft u de uitvoerings configuratie op voor die reken kracht.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maak dan een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree)
* De [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md), `ws`
* Een reken doel, `my_compute_target` .  Een compute-doel maken met:
  * [Python SDK](how-to-create-attach-compute-sdk.md) 
  * [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Wat is een script voor het uitvoeren van een configuratie?

U verzendt uw trainings experiment met een [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) -object.  Dit object bevat:

* **source_directory**: de bron directory die uw trainings script bevat
* **script**: het trainings script identificeren
* **run_config**: de [configuratie](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true)van de uitvoering, die op zijn beurt bepaalt waar de training plaatsvindt. In de `run_config` kunt u het berekenings doel en de omgeving opgeven die moeten worden gebruikt bij het uitvoeren van het trainings script.  

## <a name="whats-an-environment"></a>Wat is een omgeving?

Azure Machine Learning [omgevingen](concept-environments.md) zijn een inkapseling van de omgeving waarin uw machine learning-training zich voordoet. Hiermee worden de Python-pakketten, omgevings variabelen en software-instellingen voor uw trainings-en Score scripts opgegeven. Ze specificeren ook uitvoerings tijden (python, Spark of docker).  

Omgevingen worden opgegeven in het  `run_config` object binnen een `ScriptRunConfig` .

## <a name="train-your-model"></a><a id="submit"></a>Uw model trainen

Het code patroon voor het verzenden van een trainings uitvoering is hetzelfde voor alle typen reken doelen:

1. Een experiment maken om uit te voeren
1. Een omgeving maken waarin het script wordt uitgevoerd
1. Een configuratie voor het uitvoeren van een script maken, die verwijst naar het doel en de omgeving van de berekening
1. De uitvoering verzenden
1. Wacht totdat de uitvoering is voltooid

U kunt ook het volgende doen:

* Dien het experiment in met een `Estimator` object zoals wordt weer gegeven in de [trein ml-modellen met schattingen](how-to-train-ml-models.md).
* Verzend een HyperDrive-uitvoering voor [afstemming-afstemming](how-to-tune-hyperparameters.md).
* Een experiment verzenden via de [VS code-extensie](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Een experiment maken

Maak een experiment in uw werk ruimte.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'

experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="create-an-environment"></a>Een omgeving maken

Een gehoste omgeving bevat verzamelingen van Python-pakketten en is standaard beschikbaar in uw werk ruimte. Deze omgevingen worden ondersteund door docker-installatie kopieën in de cache, waarmee de kosten voor het uitvoeren van de voor bereiding worden verminderd. Voor een extern Compute-doel kunt u een van deze populaire gestarte omgevingen gebruiken om te beginnen met:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
my_environment = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Zie [& software omgevingen maken in azure machine learning](how-to-use-environments.md)voor meer informatie over omgevingen.
  
### <a name="local-compute-target"></a>Lokaal Compute-doel

Als uw reken doel uw **lokale computer**is, bent u verantwoordelijk voor het controleren of alle benodigde pakketten beschikbaar zijn in de python-omgeving waarin het script wordt uitgevoerd.  Gebruik `python.user_managed_dependencies` om uw huidige python-omgeving (of de python op het door u opgegeven pad) te gebruiken.

```python
from azureml.core import Environment

# Editing a run configuration property on-fly.
my_environment = Environment("user-managed-env")

my_environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#my_environment.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-script-run-configuration"></a>Configuratie voor het uitvoeren van scripts maken

Nu u een compute-doel ( `compute_target` ) en-omgeving ( `my_environment` ) hebt, maakt u een script voor het uitvoeren van een configuratie waarbij uw trainings script () wordt uitgevoerd `train.py` in uw `project_folder` map:

```python
from azureml.core import ScriptRunConfig

script_run_config = ScriptRunConfig(source_directory=project_folder, script='train.py')

# Set compute target
script_run_config.run_config.target = my_compute_target

# Set environment.   If you don't do this, a default environment will be created.
script_run_config.run_config.environment = my_environment
```

Het is ook mogelijk dat u het Framework wilt instellen voor de uitvoering.

* Voor een HDI-cluster:
    ```python
    src.run_config.framework = "pyspark"
    ```

* Voor een externe virtuele machine:
    ```python
    src.run_config.framework = "python"
    ```

## <a name="submit-the-experiment"></a>Het experiment verzenden

```python
run = experiment.submit(config=script_run_config)
```

> [!IMPORTANT]
> Wanneer u de trainings uitvoering verzendt, wordt een moment opname gemaakt van de map die uw trainings scripts bevat en verzonden naar het doel van de berekening. Het wordt ook opgeslagen als onderdeel van het experiment in uw werk ruimte. Als u bestanden wijzigt en de uitvoering opnieuw verzendt, worden alleen de gewijzigde bestanden geüpload.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Zie [moment opnamen](concept-azure-machine-learning-architecture.md#snapshots)voor meer informatie over moment opnamen.


<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git-tracking en-integratie

Wanneer u begint met het uitvoeren van een training waarbij de bronmap een lokale Git-opslag plaats is, wordt informatie over de opslag plaats opgeslagen in de uitvoerings geschiedenis. Zie [Git-integratie voor Azure machine learning](concept-train-model-git-integration.md)voor meer informatie.

## <a name="notebook-examples"></a>Voor beelden van notebooks

Bekijk deze notebooks voor voor beelden van training met verschillende Compute-doelen:
* [procedures voor het gebruik van azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [zelf studies/img-Classification-part1-training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

* [Zelf studie: een model trainen](tutorial-train-models-with-aml.md) maakt gebruik van een beheerd Compute-doel om een model te trainen.
* Meer informatie over hoe u [Hyper parameters efficiënt kunt afstemmen](how-to-tune-hyperparameters.md) om betere modellen te bouwen. weer geven = Azure-ml-py&pres Erve-View = True)
* Wanneer u een getraind model hebt, leert u [hoe en waar u modellen kunt implementeren](how-to-deploy-and-where.md).
* Bekijk de [RunConfiguration class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py&preserve-view=true) SDK-referentie.
* [Azure Machine Learning gebruiken met virtuele netwerken van Azure](how-to-enable-virtual-network.md)