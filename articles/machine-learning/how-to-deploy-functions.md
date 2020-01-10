---
title: Ml-modellen implementeren in Azure Functions-apps (preview-versie)
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van Azure Machine Learning voor het implementeren van een model naar een Azure Functions-app.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 11/22/2019
ms.openlocfilehash: 77e23467551df8d72fd999049c490600eff11825
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75763629"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Een machine learning model implementeren op Azure Functions (preview-versie)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het implementeren van een model van Azure Machine Learning als een functie-app in Azure Functions.

> [!IMPORTANT]
> Hoewel zowel Azure Machine Learning als Azure Functions algemeen beschikbaar zijn, is de mogelijkheid om een model te verpakken van de Machine Learning-service voor functions in de preview-versie.

Met Azure Machine Learning kunt u docker-installatie kopieën maken op basis van getrainde machine learning modellen. Azure Machine Learning heeft nu de Preview-functionaliteit om deze machine learning modellen te bouwen in functie-apps, die [in azure functions](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container)kunnen worden geïmplementeerd.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie het artikel [een werk ruimte maken](how-to-manage-workspace.md) voor meer informatie.
* De [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Een getraind machine learning model dat is geregistreerd in uw werk ruimte. Als u geen model hebt, gebruikt u de [zelf studie voor installatie kopie classificatie: Train model](tutorial-train-models-with-aml.md) om er een te trainen en te registreren.

    > [!IMPORTANT]
    > In de code fragmenten in dit artikel wordt ervan uitgegaan dat u de volgende variabelen hebt ingesteld:
    >
    > * `ws`-uw Azure Machine Learning-werk ruimte.
    > * `model`-het geregistreerde model dat wordt geïmplementeerd.
    > * `inference_config`-de configuratie voor het afwachten van het model.
    >
    > Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md)voor meer informatie over het instellen van deze variabelen.

## <a name="prepare-for-deployment"></a>Voorbereiden op implementatie

Voordat u implementeert, moet u definiëren wat er nodig is om het model als een webservice uit te voeren. In de volgende lijst worden de basis items beschreven die nodig zijn voor een implementatie:

* Een __invoer script__. Met dit script worden aanvragen geaccepteerd, wordt de aanvraag met het model gescoord en worden de resultaten geretourneerd.

    > [!IMPORTANT]
    > Het invoer script is specifiek voor uw model. het moet inzicht krijgen in de indeling van de gegevens van de inkomende aanvraag, de indeling van de gegevens die worden verwacht door uw model en de indeling van de gegevens die aan clients worden geretourneerd.
    >
    > Als de gegevens van de aanvraag een indeling hebben die niet kan worden gebruikt door uw model, kan het script deze omzetten in een acceptabele indeling. Het kan ook de reactie transformeren voordat deze naar de client wordt teruggekeerd.
    >
    > Wanneer de functie wordt ingepakt, wordt de invoer standaard beschouwd als tekst. Als u geïnteresseerd bent in het verbruiken van de onbewerkte bytes van de invoer (bijvoorbeeld voor BLOB-triggers), moet u [AMLRequest gebruiken om onbewerkte gegevens te accepteren](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data).


* **Afhankelijkheden**, zoals hulp scripts of python/Conda-pakketten die zijn vereist voor het uitvoeren van het script of model van de vermelding

Deze entiteiten worden ingekapseld in een Afleidings __configuratie__. De deductieconfiguratie verwijst naar het invoerscript en andere afhankelijkheden.

> [!IMPORTANT]
> Wanneer u een configuratie voor afwijzen maakt voor gebruik met Azure Functions, moet u een [omgevings](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) object gebruiken. Houd er rekening mee dat als u een aangepaste omgeving definieert, de standaard waarden van azureml en versie > = 1.0.45 als een PIP-afhankelijkheid worden toegevoegd. Dit pakket bevat de functionaliteit die nodig is om het model als een webservice te hosten. In het volgende voor beeld ziet u hoe u een omgevings object maakt en dit gebruikt met een configuratie voor ingaand gebruik:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Zie [omgevingen maken en beheren voor training en implementatie](how-to-use-environments.md)voor meer informatie over omgevingen.

Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md)voor meer informatie over het afnemen van de configuratie.

> [!IMPORTANT]
> Wanneer u naar functies implementeert, hoeft u geen __implementatie configuratie__te maken.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Het SDK-preview-pakket voor functie-ondersteuning installeren

Als u pakketten voor Azure Functions wilt maken, moet u het SDK-preview-pakket installeren.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>De installatiekopie maken

Als u de docker-installatie kopie wilt maken die is geïmplementeerd op Azure Functions, gebruikt u [azureml. contrib. functions. package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) of de specifieke pakket functie voor de trigger die u wilt gebruiken. Het volgende code fragment laat zien hoe u een nieuw pakket maakt met een BLOB-trigger uit het model en de configuratie voor afleiden:

> [!NOTE]
> In het code fragment wordt ervan uitgegaan dat `model` een geregistreerd model bevat en dat `inference_config` de configuratie voor de afnemende omgeving bevat. Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md)voor meer informatie.

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Als `show_output=True`, wordt de uitvoer van het docker-bouw proces weer gegeven. Zodra het proces is voltooid, is de afbeelding gemaakt in de Azure Container Registry voor uw werk ruimte. Zodra de installatie kopie is gemaakt, wordt de locatie in uw Azure Container Registry weer gegeven. De geretourneerde locatie heeft de indeling `<acrinstance>.azurecr.io/package@sha256:<hash>`.

> [!NOTE]
> De functie voor verpakking voor functions ondersteunt momenteel HTTP-triggers, Blob-triggers en service bus-triggers. Zie [Azure functions-bindingen](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob?tabs=csharp#trigger---blob-name-patterns)voor meer informatie over triggers.

> [!IMPORTANT]
> Sla de locatie gegevens op, zoals deze worden gebruikt bij het implementeren van de installatie kopie.

## <a name="deploy-image-as-a-web-app"></a>Een installatie kopie implementeren als een web-app

1. Gebruik de volgende opdracht om de aanmeldings referenties op te halen voor de Azure Container Registry die de installatie kopie bevat. Vervang `<acrinstance>` door de waarde die u eerder van `package.location`hebt geretourneerd: 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    De uitvoer van deze opdracht is vergelijkbaar met het volgende JSON-document:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Sla de waarde voor de __gebruikers naam__ en een van de __wacht woorden__op.

1. Als u nog geen resource groep of app service-plan hebt voor het implementeren van de service, demonstreert de volgende opdrachten hoe u beide maakt:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku EP1 --is-linux
    ```

    In dit voor beeld wordt een prijs categorie voor _Linux Premium_ (`--sku EP1`) gebruikt.

    > [!IMPORTANT]
    > Installatie kopieën die zijn gemaakt door Azure Machine Learning Linux gebruiken, dus u moet de `--is-linux`-para meter gebruiken.

1. Als u de functie-app wilt maken, gebruikt u de volgende opdracht. Vervang `<app-name>` door de naam die u wilt gebruiken. Vervang `<acrinstance>` en `<imagename>` door de waarden van de geretourneerde `package.location` eerder:

    ```azurecli-interactive
    az storage account create --name 
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    > [!IMPORTANT]
    > Op dit moment is de functie-app gemaakt. Omdat u echter geen connection string hebt ingevoerd voor de BLOB-trigger of referenties voor de Azure Container Registry die de installatie kopie bevatten, is de functie-app niet actief. In de volgende stappen geeft u de connection string en de verificatie gegevens voor het container register. 

1. Maak het opslag account dat moet worden gebruikt als de trigger en ontvang het connection string.

    ```azurecli-interactive
    az storage account create --name triggerStorage --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name triggerStorage --query connectionString --output tsv
    ```
    Neem deze connection string op om te voorzien in de functie-app. Deze worden later gebruikt wanneer u wordt gevraagd om `<triggerConnectionString>`

1. Maak de containers voor de invoer en uitvoer in het opslag account. 

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. U moet het label dat is gekoppeld aan de gemaakte container, ophalen met de volgende opdracht:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    De meest recente tag die wordt weer gegeven, wordt hieronder `imagetag`.

1. Als u de functie-app met de referenties die nodig zijn voor toegang tot het container register, wilt opgeven, gebruikt u de volgende opdracht. Vervang `<app-name>` door de naam die u wilt gebruiken. Vervang `<acrinstance>` en `<imagetag>` door de waarden van de aanroep AZ CLI in de vorige stap. Vervang `<username>` en `<password>` door de ACR-aanmeldings gegevens die u eerder hebt opgehaald:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Deze opdracht retourneert informatie die lijkt op het volgende JSON-document:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

Op dit punt begint de functie-app het laden van de installatie kopie.

> [!IMPORTANT]
> Het kan enkele minuten duren voordat de installatie kopie is geladen. U kunt de voortgang bewaken met behulp van Azure Portal.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het configureren van uw functions-app in de [functions](/azure/azure-functions/functions-create-function-linux-custom-image) -documentatie.
* Meer informatie over Blob Storage activeert [Azure Blob-opslag bindingen](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).
* [Implementeer uw model naar Azure app service](how-to-deploy-app-service.md).
* [Een ML-Model dat is geïmplementeerd als een webservice gebruiken](how-to-consume-web-service.md)
* [API-naslaginformatie](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)