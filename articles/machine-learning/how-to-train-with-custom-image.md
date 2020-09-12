---
title: Een model trainen met een aangepaste docker-installatie kopie
titleSuffix: Azure Machine Learning
description: Meer informatie over het trainen van modellen met aangepaste docker-installatie kopieën in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 08/11/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 2289a761d4e266c305c2868e9f234871624ae528
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661315"
---
# <a name="train-a-model-using-a-custom-docker-image"></a>Een model trainen met een aangepaste docker-installatie kopie
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u een aangepaste docker-installatie kopie gebruikt wanneer u een trainings model met Azure Machine Learning. 

De voorbeeld scripts in dit artikel worden gebruikt voor het classificeren van huisdier installatie kopieën door het maken van een convolutional Neural-netwerk. 

Hoewel Azure Machine Learning een standaard docker-basis installatie kopie biedt, kunt u ook Azure Machine Learning-omgevingen gebruiken om een specifieke basis installatie kopie op te geven, zoals een van de beheerde [basis installatie kopieën van Azure ml](https://github.com/Azure/AzureML-Containers) of uw eigen [aangepaste installatie kopie](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image#create-a-custom-base-image). Aangepaste basis installatie kopieën bieden u de mogelijkheid om uw afhankelijkheden nauw keurig te beheren en de controle over onderdeel versies nauw keuriger te houden wanneer u trainings taken uitvoert. 

## <a name="prerequisites"></a>Vereisten 
Voer deze code uit in een van de volgende omgevingen:
* Azure Machine Learning Compute-instantie-geen down loads of installatie vereist
    * Voltooi de [zelf studie: installatie omgeving en werk ruimte](tutorial-1st-experiment-sdk-setup.md) om een toegewezen notebook server te maken vooraf geladen met de SDK en de voor beeld-opslag plaats.
    * Zoek in het Azure Machine Learning- [voor beeld-opslag plaats](https://github.com/Azure/azureml-examples)een voltooid notitie blok door naar deze map te navigeren: **notebooks > fastai > Train-huis dieren-resnet34. ipynb** 

* Uw eigen Jupyter Notebook-server
    * Maak een [configuratie bestand voor de werk ruimte](how-to-configure-environment.md#workspace).
    * De [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true). 
    * Een [Azure container Registry](/azure/container-registry) of een ander docker-REGI ster dat toegankelijk is op internet.

## <a name="set-up-the-experiment"></a>Het experiment instellen 
In deze sectie wordt het trainings experiment opgesteld door een werk ruimte te initialiseren, een experiment te maken en de trainings gegevens en trainings scripts te uploaden.

### <a name="initialize-a-workspace"></a>Een werk ruimte initialiseren
De [Azure machine learning werk ruimte](concept-workspace.md) is de resource op het hoogste niveau voor de service. Het biedt u een centrale locatie voor het werken met alle artefacten die u maakt. In de python-SDK hebt u toegang tot de werkruimte artefacten door een [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) object te maken.

Maak een werkruimte object op basis van het `config.json` bestand dat in de [sectie vereisten](#prerequisites)is gemaakt.

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Scripts voorbereiden
Voor deze zelf studie wordt het trainings script **Train.py** [hier](https://github.com/Azure/azureml-examples/blob/main/code/models/fastai/pets-resnet34/train.py)vermeld. In de praktijk kunt u een aangepast trainings script uitvoeren, zoals dat is, en dit met Azure Machine Learning.

### <a name="define-your-environment"></a>Uw omgeving definiëren
Maak een omgevings object en schakel docker in. 

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

Deze opgegeven basis installatie kopie ondersteunt de fast.ai-bibliotheek, waarmee gedistribueerde diepe leer mogelijkheden worden toegestaan. Zie de [Fast.ai DockerHub](https://hub.docker.com/u/fastdotai)voor meer informatie. 

Wanneer u uw aangepaste docker-installatie kopie gebruikt, is het mogelijk dat uw python-omgeving al correct is ingesteld. In dat geval stelt u de `user_managed_dependencies` vlag in op True om gebruik te maken van de ingebouwde python-omgeving van uw aangepaste installatie kopie. Azure ML bouwt standaard een Conda-omgeving met afhankelijkheden die u hebt opgegeven en voert de uitvoering in die omgeving uit in plaats van python-bibliotheken te gebruiken die u hebt geïnstalleerd op de basis installatie kopie.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

Als u een installatie kopie wilt gebruiken uit een persoonlijk container register dat zich niet in uw werk ruimte bevindt, moet u gebruiken `docker.base_image_registry` om het adres van de opslag plaats en een gebruikers naam en wacht woord op te geven:

```python
# Set the container registry information
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

Het is ook mogelijk om een aangepaste Dockerfile te gebruiken. Gebruik deze methode als u niet-python-pakketten als afhankelijkheden moet installeren en vergeet niet om de basis installatie kopie in te stellen op geen.

```python 
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

### <a name="create-or-attach-existing-amlcompute"></a>Bestaande AmlCompute maken of koppelen
U moet een [reken doel](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#compute-target) maken voor het trainen van uw model. In deze zelf studie maakt u AmlCompute als uw trainings Compute-resource.

Het maken van AmlCompute duurt ongeveer 5 minuten. Als de AmlCompute met die naam al in uw werk ruimte staat, wordt met deze code het aanmaak proces overs Laan.

Net als bij andere Azure-Services gelden er limieten voor bepaalde bronnen (bijvoorbeeld AmlCompute) die zijn gekoppeld aan de Azure Machine Learning service. Lees [dit artikel](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas) over de standaard limieten en hoe u meer quota kunt aanvragen. 

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current AmlCompute
print(compute_target.get_status().serialize())
```

### <a name="create-a-scriptrunconfig"></a>Een ScriptRunConfig maken
Met deze ScriptRunConfig wordt uw taak geconfigureerd voor uitvoering op het gewenste [Compute-doel](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets#compute-targets-for-training).

```python
from azureml.core import ScriptRunConfig

fastai_config = ScriptRunConfig(source_directory='fastai-example', script='train.py')
fastai_config.run_config.environment = fastai_env
fastai_config.run_config.target = compute_target
```

### <a name="submit-your-run"></a>Uw uitvoering verzenden
Wanneer een trainings uitvoering wordt verzonden met behulp van een ScriptRunConfig-object, retourneert de methode Submit een object van het type ScriptRun. Het geretourneerde ScriptRun-object biedt u programmatische toegang tot informatie over de uitvoering van de training. 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(fastai_config)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning trainings scripts worden uitgevoerd door de hele bronmap te kopiëren. Als u gevoelige gegevens hebt die u niet wilt uploaden, gebruikt u een [. ignore-bestand](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) of neemt u het niet op in de bron directory. In plaats daarvan opent u uw gegevens met behulp van een gegevens [opslag](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true).

Zie [& software omgevingen maken](how-to-use-environments.md)voor meer informatie over het aanpassen van uw python-omgeving. 

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een model getraind met behulp van een aangepaste docker-installatie kopie. Raadpleeg de volgende artikelen voor meer informatie over Azure Machine Learning.
* [Metrische uitvoerings gegevens](how-to-track-experiments.md) tijdens de training volgen
* [Implementeer een model](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image) met behulp van een aangepaste docker-installatie kopie.
