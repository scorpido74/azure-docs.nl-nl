---
title: Modellen implementeren met een aangepaste docker-installatie kopie
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van een aangepaste docker-basis installatie kopie bij het implementeren van uw Azure Machine Learning modellen. Hoewel Azure Machine Learning een standaard basis installatie kopie voor u biedt, kunt u ook uw eigen basis installatie kopie gebruiken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: 8c55fec08f05352d4587a8821c10600b7d7fad07
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396161"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Een model implementeren met behulp van een aangepaste docker-basis installatie kopie
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het gebruik van een aangepaste docker-basis installatie kopie bij het implementeren van getrainde modellen met Azure Machine Learning.

Wanneer u een getraind model implementeert voor een webservice of IoT Edge apparaat, wordt er een pakket gemaakt met een webserver voor het verwerken van binnenkomende aanvragen.

Azure Machine Learning biedt een standaarddocker-basis installatie kopie, zodat u zich geen zorgen hoeft te maken dat u er een maakt. U kunt ook Azure Machine Learning __omgevingen__ gebruiken om een specifieke basis installatie kopie te selecteren, of een aangepaste versie gebruiken die u opgeeft.

Een basis installatie kopie wordt gebruikt als uitgangs punt wanneer er een installatie kopie voor een implementatie wordt gemaakt. Het biedt het onderliggende besturings systeem en onderdelen. Het implementatie proces voegt vervolgens extra onderdelen, zoals uw model, Conda-omgeving en andere assets, toe aan de installatie kopie voordat u deze implementeert.

Normaal gesp roken maakt u een aangepaste basis installatie kopie wanneer u docker wilt gebruiken voor het beheren van uw afhankelijkheden, het behoud van de controle over onderdeel versies en het besparen van tijd tijdens de implementatie. U kunt bijvoorbeeld standaardiseren op een specifieke versie van python, Conda of een ander onderdeel. Het is ook mogelijk dat u software wilt installeren die vereist is voor uw model, waarbij het installatie proces veel tijd in beslag neemt. Het installeren van de software bij het maken van de basis installatie kopie betekent dat u deze niet voor elke implementatie hoeft te installeren.

> [!IMPORTANT]
> Wanneer u een model implementeert, kunt u de kern onderdelen, zoals de webserver of de IoT Edge onderdelen, niet overschrijven. Deze onderdelen bieden een bekende werk omgeving die wordt getest en ondersteund door micro soft.

> [!WARNING]
> Micro soft kan niet helpen bij het oplossen van problemen die zijn veroorzaakt door een aangepaste installatie kopie. Als u problemen ondervindt, wordt u mogelijk gevraagd om de standaard installatie kopie of een van de installatie kopieën te gebruiken die micro soft biedt om te controleren of het probleem specifiek is voor uw installatie kopie.

Dit document is onderverdeeld in twee secties:

* Een aangepaste basis installatie kopie maken: bevat informatie over beheerders en DevOps over het maken van een aangepaste installatie kopie en het configureren van verificatie voor een Azure Container Registry met behulp van de Azure CLI en Machine Learning CLI.
* Een model implementeren met behulp van een aangepaste basis installatie kopie: hierin vindt u informatie over gegevens wetenschappers en DevOps/ML-technici voor het gebruik van aangepaste installatie kopieën bij het implementeren van een getraind model vanuit de python-SDK of ML CLI.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning werk groep. Zie het artikel [een werk ruimte maken](how-to-manage-workspace.md) voor meer informatie.
* De [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). 
* De [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* De [cli-extensie voor Azure machine learning](reference-azure-machine-learning-cli.md).
* Een [Azure container Registry](/azure/container-registry) of een ander docker-REGI ster dat toegankelijk is op internet.
* Bij de stappen in dit document wordt ervan uitgegaan dat u bekend bent met het maken en gebruiken van een inkomend __configuratie__ object als onderdeel van de implementatie van het model. Zie voor meer informatie de sectie ' voor bereiding implementeren ' van [waar u wilt implementeren en hoe](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-base-image"></a>Een aangepaste basis installatie kopie maken

In de informatie in deze sectie wordt ervan uitgegaan dat u een Azure Container Registry gebruikt voor het opslaan van docker-installatie kopieën. Gebruik de volgende controle lijst bij het plannen van het maken van aangepaste installatie kopieën voor Azure Machine Learning:

* Gebruikt u de Azure Container Registry die is gemaakt voor de Azure Machine Learning-werk ruimte of een zelfstandige Azure Container Registry?

    Wanneer u installatie kopieën gebruikt die zijn opgeslagen in het __container register voor de werk ruimte__, hoeft u zich niet te verifiëren bij het REGI ster. Verificatie wordt verwerkt door de werk ruimte.

    > [!WARNING]
    > De Azure Container Registry voor uw werk ruimte wordt __gemaakt wanneer u voor het eerst een model traint of implementeert__ met behulp van de werk ruimte. Als u een nieuwe werk ruimte hebt gemaakt, maar niet hebt getraind of een model hebt gemaakt, bestaat er geen Azure Container Registry voor de werk ruimte.

    Zie de sectie [register naam van container ophalen](#getname) in dit artikel voor meer informatie over het ophalen van de naam van de Azure container Registry voor uw werk ruimte.

    Wanneer u installatie kopieën gebruikt die zijn opgeslagen in een __zelfstandig container register__, moet u een Service-Principal configureren die ten minste lees toegang heeft. Vervolgens geeft u de Service-Principal-ID (gebruikers naam) en het wacht woord op voor iedereen die gebruikmaakt van installatie kopieën uit het REGI ster. De uitzonde ring hierop is als u het container register openbaar toegankelijk maakt.

    Zie [een persoonlijk container register maken](/azure/container-registry/container-registry-get-started-azure-cli)voor meer informatie over het maken van een persoonlijke Azure container Registry.

    Zie voor meer informatie over het gebruik van service-principals met Azure Container Registry [Azure container Registry verificatie met Service-principals](/azure/container-registry/container-registry-auth-service-principal).

* Azure Container Registry en installatie kopie-informatie: Geef de naam van de installatie kopie op die moet worden gebruikt door iedereen. Bijvoorbeeld, een installatie kopie met de naam `myimage`, opgeslagen in een REGI ster met de naam `myregistry`, wordt verwezen als `myregistry.azurecr.io/myimage` wanneer de installatie kopie wordt gebruikt voor model implementatie

* Vereisten voor installatie kopie: Azure Machine Learning ondersteunt alleen docker-installatie kopieën die de volgende software bieden:

    * Ubuntu 16,04 of hoger.
    * Conda 4.5. # of hoger.
    * Python 3.5. # of 3.6. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Container register gegevens ophalen

In deze sectie leert u hoe u de naam van de Azure Container Registry voor uw Azure Machine Learning-werk ruimte kunt ophalen.

> [!WARNING]
> De Azure Container Registry voor uw werk ruimte wordt __gemaakt wanneer u voor het eerst een model traint of implementeert__ met behulp van de werk ruimte. Als u een nieuwe werk ruimte hebt gemaakt, maar niet hebt getraind of een model hebt gemaakt, bestaat er geen Azure Container Registry voor de werk ruimte.

Als u al uw modellen hebt getraind of geïmplementeerd met behulp van Azure Machine Learning, is er een container register gemaakt voor uw werk ruimte. Als u de naam van dit container register wilt vinden, gebruikt u de volgende stappen:

1. Open een nieuwe shell of opdracht prompt en gebruik de volgende opdracht om u te verifiëren bij uw Azure-abonnement:

    ```azurecli-interactive
    az login
    ```

    Volg de aanwijzingen om u te verifiëren bij het abonnement.

2. Gebruik de volgende opdracht om het container register voor de werk ruimte weer te geven. Vervang `<myworkspace>` door de naam van uw Azure Machine Learning werk ruimte. Vervang `<resourcegroup>` door de Azure-resource groep die uw werk ruimte bevat:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    De informatie die wordt geretourneerd, is vergelijkbaar met de volgende tekst:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    De `<registry_name>` waarde is de naam van de Azure Container Registry voor uw werk ruimte.

### <a name="build-a-custom-base-image"></a>Een aangepaste basis installatie kopie bouwen

Met de stappen in deze sectie wordt uitgelegd hoe u een aangepaste docker-installatie kopie maakt in uw Azure Container Registry.

1. Maak een nieuw tekst bestand met de naam `Dockerfile`en gebruik de volgende tekst als de inhoud:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Gebruik bij een shell of opdracht prompt het volgende om te verifiëren bij de Azure Container Registry. Vervang de `<registry_name>` door de naam van het container register waarvan u de installatie kopie wilt opslaan:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Als u de Dockerfile wilt uploaden en deze wilt bouwen, gebruikt u de volgende opdracht. Vervang `<registry_name>` door de naam van het container register waarin u de installatie kopie wilt opslaan:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    Tijdens het bouw proces worden gegevens gestreamd naar de opdracht regel. Als de build is geslaagd, wordt er een bericht weer gegeven dat vergelijkbaar is met de volgende tekst:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Zie [een container installatie kopie bouwen en uitvoeren met Azure container Registry taken](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli) voor meer informatie over het bouwen van installatie kopieën met een Azure container Registry

Zie [uw eerste installatie kopie naar een privé-docker-container register pushen](/azure/container-registry/container-registry-get-started-docker-cli)voor meer informatie over het uploaden van bestaande installatie kopieën naar een Azure container Registry.

## <a name="use-a-custom-base-image"></a>Een aangepaste basis installatie kopie gebruiken

Als u een aangepaste installatie kopie wilt gebruiken, hebt u de volgende informatie nodig:

* De __naam van de installatie kopie__. `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` is bijvoorbeeld het pad naar een basis-docker-installatie kopie van micro soft.
* Als de installatie kopie zich in een __privé opslagplaats__bevindt, hebt u de volgende informatie nodig:

    * Het register __adres__. Bijvoorbeeld `myregistry.azureecr.io`.
    * Een __gebruikers naam__ en- __wacht woord__ voor de service-principal met lees toegang tot het REGI ster.

    Als u deze informatie niet hebt, neemt u contact op met de beheerder voor de Azure Container Registry die uw installatie kopie bevat.

### <a name="publicly-available-base-images"></a>Openbaar beschik bare basis installatie kopieën

Micro soft biedt verschillende docker-installatie kopieën op een openbaar toegankelijke opslag plaats die kan worden gebruikt met de stappen in deze sectie:

| Afbeelding | Beschrijving |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Basis installatie kopie voor Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Bevat ONNX-runtime voor CPU-de |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Bevat de ONNX runtime en CUDA voor GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Bevat ONNX runtime en TensorRT voor GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm ` | Bevat de ONNX runtime en open-wijn<sup> </sup> voor het ontwerp van de Intel Vision Accelerator op basis van Movidius<sup>TM</sup> tallozex VPUs |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Bevat ONNX runtime en-wijn<sup> </sup> voor Intel Movidius<sup>TM</sup> USB-sticks |

Zie de [sectie ONNX runtime dockerfile](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) in de GitHub opslag plaats voor meer informatie over de basis installatie kopieën van ONNX-runtime.

> [!TIP]
> Omdat deze installatie kopieën openbaar beschikbaar zijn, hoeft u geen adres, gebruikers naam of wacht woord op te geven wanneer u deze gebruikt.

Zie [Azure machine learning containers](https://github.com/Azure/AzureML-Containers)voor meer informatie.

> [!TIP]
>__Als uw model is getraind op Azure machine learning Compute__met __versie 1.0.22 of hoger__ van de Azure machine learning SDK, wordt er tijdens de training een installatie kopie gemaakt. Als u de naam van deze installatie kopie wilt detecteren, gebruikt u `run.properties["AzureML.DerivedImageName"]`. In het volgende voor beeld ziet u hoe u deze installatie kopie gebruikt:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Een installatie kopie gebruiken met de Azure Machine Learning SDK

Als u een installatie kopie wilt gebruiken die is opgeslagen in de **Azure container Registry voor uw werk ruimte**of een **container register dat openbaar toegankelijk is**, stelt u de volgende [omgevings](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) kenmerken in:

+ `docker.enabled=True`
+ `docker.base_image`: ingesteld op het REGI ster en pad naar de installatie kopie.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Als u een installatie kopie wilt gebruiken uit een __persoonlijk container register__ dat zich niet in uw werk ruimte bevindt, moet u `docker.base_image_registry` gebruiken om het adres van de opslag plaats en een gebruikers naam en wacht woord op te geven:

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

U moet de standaard waarden van azureml toevoegen met versie > = 1.0.45 als een PIP-afhankelijkheid. Dit pakket bevat de functionaliteit die nodig is om het model als een webservice te hosten. U moet ook inferencing_stack_version eigenschap op de omgeving instellen op ' meest recent ', maar Hiermee installeert u specifieke apt-pakketten die nodig zijn voor de webservice. 

Nadat u de omgeving hebt gedefinieerd, kunt u deze gebruiken met een [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) -object om de Afleidings omgeving te definiëren waarin het model en de webservice worden uitgevoerd.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

U kunt nu door gaan met de implementatie. Met het volgende code fragment wordt bijvoorbeeld lokaal een webservice geïmplementeerd met behulp van de configuratie voor inactiviteit en aangepaste installatie kopie:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md)voor meer informatie over de implementatie.

Zie [omgevingen maken en beheren voor training en implementatie](how-to-use-environments.md)voor meer informatie over het aanpassen van uw python-omgeving. 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Een installatie kopie gebruiken met de Machine Learning CLI

> [!IMPORTANT]
> Momenteel kan de Machine Learning CLI installatie kopieën van de Azure Container Registry gebruiken voor uw werk ruimte of openbaar toegankelijke opslag plaatsen. Het kan geen installatie kopieën van zelfstandige persoonlijke registers gebruiken.

Wanneer u een model implementeert met behulp van de Machine Learning CLI, geeft u een bestand voor een Afleidings configuratie op dat verwijst naar de aangepaste installatie kopie. In het volgende JSON-document ziet u hoe u kunt verwijzen naar een installatie kopie in een open bare container register:

```json
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "infenv.yml",
   "extraDockerfileSteps": null,
   "sourceDirectory": null,
   "enableGpu": false,
   "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
   "baseImageRegistry": "mcr.microsoft.com"
}
```

Dit bestand wordt gebruikt met de opdracht `az ml model deploy`. De para meter `--ic` wordt gebruikt om het configuratie bestand voor de afleiding in te stellen.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Zie de sectie model registratie, profile ring en implementatie van de [cli-uitbrei ding voor Azure machine learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) artikel voor meer informatie over het implementeren van een model met de ml cli.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [waar u kunt implementeren en hoe u](how-to-deploy-and-where.md)dit kunt doen.
* Leer hoe u [machine learning modellen traint en implementeert met behulp van Azure-pijp lijnen](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).
