---
title: Modellen implementeren met aangepaste Docker-afbeelding
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van een aangepaste Docker-basisafbeelding bij het implementeren van uw Azure Machine Learning-modellen. Hoewel Azure Machine Learning een standaardbasisafbeelding voor u biedt, u ook uw eigen basisafbeelding gebruiken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/16/2020
ms.openlocfilehash: a237beb72e35a236e353c58db520a8d611fdfdcd
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618008"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Een model implementeren met een aangepaste Docker-basisafbeelding
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het gebruik van een aangepaste Docker-basisafbeelding bij het implementeren van getrainde modellen met Azure Machine Learning.

Wanneer u een getraind model implementeert op een webservice of IoT Edge-apparaat, wordt een pakket gemaakt dat een webserver bevat om binnenkomende aanvragen af te handelen.

Azure Machine Learning biedt een standaard Docker-basisafbeelding, zodat u zich geen zorgen hoeft te maken over het maken van een basisimage. U azure machine __learning-omgevingen__ ook gebruiken om een specifieke basisafbeelding te selecteren of een aangepaste afbeelding te gebruiken die u opgeeft.

Een basisafbeelding wordt gebruikt als uitgangspunt wanneer een afbeelding wordt gemaakt voor een implementatie. Het biedt het onderliggende besturingssysteem en componenten. Het implementatieproces voegt vervolgens extra componenten, zoals uw model, conda-omgeving en andere elementen, toe aan de afbeelding voordat deze wordt geïmplementeerd.

Doorgaans maakt u een aangepaste basisafbeelding wanneer u Docker wilt gebruiken om uw afhankelijkheden te beheren, de controle over onderdelenversies te herstellen of tijd te besparen tijdens de implementatie. U bijvoorbeeld standaardiseren op een specifieke versie van Python, Conda of een ander onderdeel. U ook software installeren die vereist is door uw model, waarbij het installatieproces lang duurt. Als u de software installeert bij het maken van de basisafbeelding, hoeft u deze niet voor elke implementatie te installeren.

> [!IMPORTANT]
> Wanneer u een model implementeert, u kerncomponenten zoals de webserver of IoT Edge-componenten niet overschrijven. Deze componenten bieden een bekende werkomgeving die wordt getest en ondersteund door Microsoft.

> [!WARNING]
> Microsoft kan mogelijk niet helpen bij het oplossen van problemen veroorzaakt door een aangepaste afbeelding. Als u problemen ondervindt, wordt u mogelijk gevraagd om de standaardafbeelding of een van de afbeeldingen te gebruiken die Microsoft biedt om te zien of het probleem specifiek is voor uw afbeelding.

Dit document is opgesplitst in twee secties:

* Een aangepaste basisafbeelding maken: geeft beheerders en Ontwikkelaars informatie over het maken van een aangepaste afbeelding en het configureren van verificatie naar een Azure Container Registry met behulp van azure CLI en Machine Learning CLI.
* Een model implementeren met behulp van een aangepaste basisafbeelding: biedt informatie aan gegevenswetenschappers en DevOps / ML-technici over het gebruik van aangepaste afbeeldingen bij het implementeren van een getraind model van de Python SDK of ML CLI.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkgroep. Zie het artikel [Een werkruimte maken](how-to-manage-workspace.md) voor meer informatie.
* De [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). 
* De [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* De [CLI-extensie voor Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Een [Azure Container Registry](/azure/container-registry) of een ander Docker-register dat toegankelijk is op internet.
* De stappen in dit document gaan ervan uit dat u bekend bent met het maken en gebruiken van een __object inference configuratie__ als onderdeel van modelimplementatie. Zie voor meer informatie het gedeelte 'Voorbereiden op implementatie' van [Waar te implementeren en hoe](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-base-image"></a>Een aangepaste basisafbeelding maken

De informatie in deze sectie gaat ervan uit dat u een Azure Container Registry gebruikt om Docker-afbeeldingen op te slaan. Gebruik de volgende checklist wanneer u van plan bent aangepaste afbeeldingen voor Azure Machine Learning te maken:

* Gebruikt u het Azure Container Registry dat is gemaakt voor de Azure Machine Learning-werkruimte of een zelfstandig Azure Container Registry?

    Wanneer u afbeeldingen gebruikt die zijn opgeslagen in het __containerregister voor de werkruimte,__ hoeft u zich niet te verifiëren bij het register. Verificatie wordt afgehandeld door de werkruimte.

    > [!WARNING]
    > Het Azure Container Registry voor uw werkruimte wordt __gemaakt wanneer u voor het eerst een model traint of implementeert__ met behulp van de werkruimte. Als u een nieuwe werkruimte hebt gemaakt, maar geen model hebt getraind of gemaakt, bestaat er geen Azure Container Registry voor de werkruimte.

    Zie de sectie Naam van het [containerregister ophalen](#getname) van uw werkruimte voor informatie over het ophalen van de naam van het Azure Container Registry voor uw werkruimte.

    Wanneer u afbeeldingen gebruikt die zijn opgeslagen in een __zelfstandig containerregister,__ moet u een serviceprincipal configureren die ten minste leestoegang heeft. Vervolgens geeft u de service principal ID (gebruikersnaam) en wachtwoord aan iedereen die afbeeldingen uit het register gebruikt. De uitzondering is als u het containerregister openbaar toegankelijk maakt.

    Zie [Een privécontainerregister maken](/azure/container-registry/container-registry-get-started-azure-cli)voor informatie over het maken van een privé-Azure-containerregister.

    Zie [Azure Container Registry-verificatie met serviceprincipals](/azure/container-registry/container-registry-auth-service-principal)voor informatie over het gebruik van serviceprincipals met Azure Container Registry.

* Azure Container Registry en afbeeldingsgegevens: geef de naam van de afbeelding op aan iedereen die deze moet gebruiken. Een afbeelding met `myimage`de naam , `myregistry`opgeslagen in een `myregistry.azurecr.io/myimage` register met de naam, wordt bijvoorbeeld genoemd als wanneer de afbeelding wordt gebruikt voor modelimplementatie

* Image-vereisten: Azure Machine Learning ondersteunt alleen Docker-afbeeldingen die de volgende software bieden:

    * Ubuntu 16.04 of hoger.
    * Conda 4.5.# of hoger.
    * Python 3.5.# of 3.6.#.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Informatie over containerregister

In deze sectie vindt u de naam van het Azure Container Registry voor uw Azure Machine Learning-werkruimte.

> [!WARNING]
> Het Azure Container Registry voor uw werkruimte wordt __gemaakt wanneer u voor het eerst een model traint of implementeert__ met behulp van de werkruimte. Als u een nieuwe werkruimte hebt gemaakt, maar geen model hebt getraind of gemaakt, bestaat er geen Azure Container Registry voor de werkruimte.

Als u al modellen hebt getraind of geïmplementeerd met Azure Machine Learning, is er een containerregister gemaakt voor uw werkruimte. Als u de naam van dit containerregister wilt vinden, gebruikt u de volgende stappen:

1. Open een nieuwe shell of opdrachtprompt en gebruik de volgende opdracht om te verifiëren voor uw Azure-abonnement:

    ```azurecli-interactive
    az login
    ```

    Volg de aanwijzingen om te verifiëren voor het abonnement.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Gebruik de volgende opdracht om het containerregister voor de werkruimte weer te geven. Vervang `<myworkspace>` de naam van uw Azure Machine Learning-werkruimte. Vervang `<resourcegroup>` de Azure-brongroep die uw werkruimte bevat:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    De geretourneerde informatie is vergelijkbaar met de volgende tekst:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    De `<registry_name>` waarde is de naam van het Azure Container Registry voor uw werkruimte.

### <a name="build-a-custom-base-image"></a>Een aangepaste basisafbeelding maken

De stappen in deze sectie doorlopen het maken van een aangepaste Docker-afbeelding in uw Azure Container Registry.

1. Maak een nieuw `Dockerfile`tekstbestand met de naam en gebruik de volgende tekst als inhoud:

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

2. Gebruik het volgende om vanuit een shell of opdrachtprompt te verifiëren in het Azure Container Registry. Vervang `<registry_name>` de afbeelding door de naam van het containerregister waarin u de afbeelding wilt opslaan:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Als u het Dockerbestand wilt uploaden en bouwen, gebruikt u de volgende opdracht. Vervang `<registry_name>` de naam van het containerregister waarin u de afbeelding wilt opslaan:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > In dit voorbeeld wordt `:v1` een tag van toegepast op de afbeelding. Als er geen tag is `:latest` opgegeven, wordt een tag van toegepast.

    Tijdens het bouwproces wordt informatie gestreamd naar de opdrachtregel. Als de build succesvol is, ontvangt u een bericht dat lijkt op de volgende tekst:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Zie [Een containerafbeelding bouwen en uitvoeren met Azure Container Registry Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli) voor meer informatie over het maken van afbeeldingen met een Azure Container Registry.

Zie [Uw eerste afbeelding naar een privéDocker-containerregister schuiven voor](/azure/container-registry/container-registry-get-started-docker-cli)meer informatie over het uploaden van bestaande afbeeldingen naar een Azure Container Registry.

## <a name="use-a-custom-base-image"></a>Een aangepaste basisafbeelding gebruiken

Als u een aangepaste afbeelding wilt gebruiken, hebt u de volgende informatie nodig:

* De __naam van de afbeelding__. Het `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` pad naar een basisdockerafbeelding van Microsoft is bijvoorbeeld het pad naar een basisdockerafbeelding.

    > [!IMPORTANT]
    > Voor aangepaste afbeeldingen die u hebt gemaakt, moet u alle tags opnemen die bij de afbeelding zijn gebruikt. Als uw afbeelding bijvoorbeeld is gemaakt met een `:v1`specifieke tag, zoals . Als u geen specifieke tag hebt gebruikt bij `:latest` het maken van de afbeelding, is er een tag van toegepast.

* Als de afbeelding zich in een __privéopslagplaats bevindt,__ hebt u de volgende informatie nodig:

    * Het __registeradres__. Bijvoorbeeld `myregistry.azureecr.io`.
    * Een __servicehoofdgebruikersnaam__ en __-wachtwoord__ die toegang tot het register heeft gelezen.

    Als u deze informatie niet hebt, spreekt u met de beheerder van het Azure Container Registry dat uw afbeelding bevat.

### <a name="publicly-available-base-images"></a>Openbaar beschikbare basisafbeeldingen

Microsoft biedt verschillende dockerafbeeldingen op een openbaar toegankelijke opslagplaats, die kunnen worden gebruikt met de stappen in deze sectie:

| Installatiekopie | Beschrijving |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Basisafbeelding voor Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Bevat ONNX Runtime voor CPU-inferencing |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Bevat de ONNX Runtime en CUDA voor GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Bevat ONNX Runtime en TensorRT voor GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm ` | Bevat ONNX Runtime en<sup> </sup> OpenVINO voor Intel Vision Accelerator Design op basis van Movidius<sup>TM</sup> MyriadX VPUs |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Bevat ONNX Runtime en<sup> </sup> OpenVINO voor Intel Movidius<sup>TM</sup> USB-sticks |

Zie de [onnx Runtime dockerfile-sectie](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) in de GitHub-repo voor meer informatie over de basisafbeeldingen van ONNX Runtime.

> [!TIP]
> Aangezien deze afbeeldingen openbaar beschikbaar zijn, hoeft u bij het gebruik ervan geen adres, gebruikersnaam of wachtwoord op te geven.

Zie [Azure Machine Learning-containers](https://github.com/Azure/AzureML-Containers)voor meer informatie .

> [!TIP]
>__Als uw model is getraind op Azure Machine Learning Compute__, met versie __1.0.22 of meer__ van de Azure Machine Learning SDK, wordt een afbeelding gemaakt tijdens de training. Als u de naam van `run.properties["AzureML.DerivedImageName"]`deze afbeelding wilt ontdekken, gebruikt u . In het volgende voorbeeld wordt uitgelegd hoe u deze afbeelding gebruikt:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Een afbeelding gebruiken met de Azure Machine Learning SDK

Als u een afbeelding wilt gebruiken die is opgeslagen in het **Azure Container Registry voor uw werkruimte**of een **containerregister dat openbaar toegankelijk is,** stelt u de volgende [omgevingskenmerken](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) in:

+ `docker.enabled=True`
+ `docker.base_image`: Ingesteld op het register en het pad naar de afbeelding.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Als u een afbeelding wilt gebruiken uit een register van `docker.base_image_registry` __privécontainers__ dat zich niet in uw werkruimte bevindt, moet u het adres van de opslagplaats en een gebruikersnaam en wachtwoord opgeven:

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

U moet azureml-standaardinstellingen toevoegen met versie >= 1.0,45 als pip-afhankelijkheid. Dit pakket bevat de functionaliteit die nodig is om het model als webservice te hosten. U moet ook inferencing_stack_version eigenschap op het milieu op "recentste" plaatsen, zal dit specifieke apt pakketten installeren die door webdienst worden vereist. 

Nadat u de omgeving hebt gedefinieerd, gebruikt u deze met een [Object InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) om de gevolgtrekkingsomgeving te definiëren waarin het model en de webservice worden uitgevoerd.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Op dit punt u doorgaan met de implementatie. In het volgende codefragment wordt bijvoorbeeld een webservice lokaal geïmplementeerd met behulp van de conclusieconfiguratie en aangepaste afbeelding:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Zie [Modellen implementeren met Azure Machine Learning voor](how-to-deploy-and-where.md)meer informatie over implementatie.

Zie [Omgevingen maken en beheren voor training en implementatie voor](how-to-use-environments.md)meer informatie over het aanpassen van uw Python-omgeving. 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Een afbeelding gebruiken met de Machine Learning CLI

> [!IMPORTANT]
> Momenteel kan de Machine Learning CLI afbeeldingen uit het Azure Container Registry gebruiken voor uw werkruimte of openbaar toegankelijke opslagplaatsen. Het kan geen afbeeldingen van zelfstandige privéregisters gebruiken.

Maak een [omgeving](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) die de aangepaste afbeelding gebruikt voordat u een model implementeert met de Machine Learning CLI. Maak vervolgens een inferenteisconfiguratiebestand dat verwijst naar de omgeving. U de omgeving ook rechtstreeks definiëren in het configuratiebestand van gevolgtrekking. In het volgende JSON-document wordt uitgelegd hoe u naar een afbeelding in een openbaar containerregister verwijzen. In dit voorbeeld wordt de omgeving inline gedefinieerd:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Dit bestand wordt `az ml model deploy` gebruikt met de opdracht. De `--ic` parameter wordt gebruikt om het configuratiebestand van de gevolgtrekking op te geven.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Zie het gedeelte 'modelregistratie, profilering en implementatie' van de [CLI-extensie voor Azure Machine Learning voor](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) meer informatie over het implementeren van een model met de ML CLI.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [waar te implementeren en hoe](how-to-deploy-and-where.md).
* Meer informatie over het [trainen en implementeren van machine learning-modellen met Azure Pipelines.](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)
