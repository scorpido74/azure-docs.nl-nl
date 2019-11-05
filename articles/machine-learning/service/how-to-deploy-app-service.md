---
title: Ml-modellen implementeren op Azure App Service (preview-versie)
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van Azure Machine Learning voor het implementeren van een model in een web-app in Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: b0d7286d96d2fbfa35eb7ce9079413dfd186288c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496956"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Een machine learning model implementeren op Azure App Service (preview-versie)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het implementeren van een model van Azure Machine Learning als een web-app in Azure App Service.

> [!IMPORTANT]
> Hoewel zowel Azure Machine Learning als Azure App Service algemeen beschikbaar zijn, is de mogelijkheid om een model van de Machine Learning-service naar App Service te implementeren, een preview-versie.

Met Azure Machine Learning kunt u docker-installatie kopieën maken op basis van getrainde machine learning modellen. Deze installatie kopie bevat een webservice waarmee gegevens worden ontvangen, naar het model worden verzonden en het antwoord vervolgens wordt geretourneerd. Azure App Service kan worden gebruikt om de installatie kopie te implementeren en biedt de volgende functies:

* Geavanceerde [verificatie](/azure/app-service/configure-authentication-provider-aad) voor verbeterde beveiliging. Verificatie methoden zijn zowel Azure Active Directory als multi-factor Authentication.
* [Automatisch schalen](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) zonder opnieuw te hoeven implementeren.
* [SSL-ondersteuning](/azure/app-service/configure-ssl-certificate-in-code) voor beveiligde communicatie tussen clients en de service.

Zie [app service-overzicht](/azure/app-service/overview)voor meer informatie over de functies van Azure app service.

> [!IMPORTANT]
> Als u de mogelijkheid wilt bieden om de Score gegevens die worden gebruikt met uw geïmplementeerde model of de resultaten van de score te registreren, moet u in plaats daarvan implementeren naar de Azure Kubernetes-service. Zie [gegevens verzamelen in uw productie modellen](how-to-enable-data-collection.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie het artikel [een werk ruimte maken](how-to-manage-workspace.md) voor meer informatie.
* De [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Een getraind machine learning model dat is geregistreerd in uw werk ruimte. Als u geen model hebt, gebruikt u de [zelf studie voor installatie kopie classificatie: Train model](tutorial-train-models-with-aml.md) om er een te trainen en te registreren.

    > [!IMPORTANT]
    > In de code fragmenten in dit artikel wordt ervan uitgegaan dat u de volgende variabelen hebt ingesteld:
    >
    > * `ws`-uw Azure Machine Learning-werk ruimte.
    > * `model`-het geregistreerde model dat wordt geïmplementeerd.
    > * `inference_config`-de configuratie voor het afwachten van het model.
    >
    > Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md)voor meer informatie over het instellen van deze variabelen.

## <a name="prepare-for-deployment"></a>Implementatie voorbereiden

Voordat u implementeert, moet u definiëren wat er nodig is om het model als een webservice uit te voeren. In de volgende lijst worden de basis items beschreven die nodig zijn voor een implementatie:

* Een __invoer script__. Met dit script worden aanvragen geaccepteerd, wordt de aanvraag met het model gescoord en worden de resultaten geretourneerd.

    > [!IMPORTANT]
    > Het invoer script is specifiek voor uw model. het moet inzicht krijgen in de indeling van de gegevens van de inkomende aanvraag, de indeling van de gegevens die worden verwacht door uw model en de indeling van de gegevens die aan clients worden geretourneerd.
    >
    > Als de gegevens van de aanvraag een indeling hebben die niet kan worden gebruikt door uw model, kan het script deze omzetten in een acceptabele indeling. Het kan ook de reactie transformeren voordat deze naar de client wordt teruggekeerd.

    > [!IMPORTANT]
    > De Azure Machine Learning SDK biedt geen manier om toegang te krijgen tot uw Data Store-of gegevens sets via de webservice. Als u het geïmplementeerde model nodig hebt om toegang te krijgen tot gegevens die buiten de implementatie zijn opgeslagen, zoals in een Azure Storage-account, moet u een oplossing voor aangepaste code ontwikkelen met behulp van de relevante SDK. Bijvoorbeeld de [Azure Storage SDK voor python](https://github.com/Azure/azure-storage-python).
    >
    > Een ander alternatief dat kan worden gebruikt voor uw scenario is [batch voorspellingen](how-to-run-batch-predictions.md). Dit biedt ook toegang tot gegevens opslag in de score.

    Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md)voor meer informatie over invoer scripts.

* **Afhankelijkheden**, zoals hulp scripts of python/Conda-pakketten die zijn vereist voor het uitvoeren van het script of model van de vermelding

Deze entiteiten worden ingekapseld in een Afleidings __configuratie__. De configuratie voor afwijzen verwijst naar het script voor de vermelding en andere afhankelijkheden.

> [!IMPORTANT]
> Wanneer u een configuratie voor afwijzen maakt voor gebruik met Azure App Service, moet u een [omgevings](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) object gebruiken. In het volgende voor beeld ziet u hoe u een omgevings object maakt en dit gebruikt met een configuratie voor ingaand gebruik:
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

Zie [omgevingen maken en beheren voor training en implementatie](how-to-use-environments.md)voor meer informatie over omgevingen.

Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md)voor meer informatie over het afnemen van de configuratie.

> [!IMPORTANT]
> Wanneer u naar Azure App Service implementeert, hoeft u geen __implementatie configuratie__te maken.

## <a name="create-the-image"></a>De installatiekopie maken

Als u de docker-installatie kopie wilt maken die is geïmplementeerd op Azure App Service, gebruikt u [model. package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config--generate-dockerfile-false-). Het volgende code fragment laat zien hoe u een nieuwe installatie kopie kunt bouwen op basis van het model en de configuratie voor het afwijzen van de afleiding:

> [!NOTE]
> In het code fragment wordt ervan uitgegaan dat `model` een geregistreerd model bevat en dat `inference_config` de configuratie voor de afnemende omgeving bevat. Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md)voor meer informatie.

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Als `show_output=True`, wordt de uitvoer van het docker-bouw proces weer gegeven. Zodra het proces is voltooid, is de afbeelding gemaakt in de Azure Container Registry voor uw werk ruimte. Zodra de installatie kopie is gemaakt, wordt de locatie in uw Azure Container Registry weer gegeven. De geretourneerde locatie heeft de indeling `<acrinstance>.azurecr.io/package:<imagename>`. Bijvoorbeeld `myml08024f78fd10.azurecr.io/package:20190827151241`.

> [!IMPORTANT]
> Sla de locatie gegevens op, zoals deze worden gebruikt bij het implementeren van de installatie kopie.

## <a name="deploy-image-as-a-web-app"></a>Een installatie kopie implementeren als een web-app

1. Gebruik de volgende opdracht om de aanmeldings referenties op te halen voor de Azure Container Registry die de installatie kopie bevat. Vervang `<acrinstance>` door de waarde van `package.location`die u eerder hebt geretourneerd: 

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
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    In dit voor beeld wordt een __basis__ prijs categorie (`--sku B1`) gebruikt.

    > [!IMPORTANT]
    > Installatie kopieën die zijn gemaakt door Azure Machine Learning Linux gebruiken, dus u moet de `--is-linux`-para meter gebruiken.

1. Gebruik de volgende opdracht om de web-app te maken. Vervang `<app-name>` door de naam die u wilt gebruiken. Vervang `<acrinstance>` en `<imagename>` door de waarden van de geretourneerde `package.location` eerder:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    Deze opdracht retourneert informatie die lijkt op het volgende JSON-document:

    ```json
    { 
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > Op dit moment is de web-app gemaakt. Omdat u echter niet de referenties hebt ingevoerd voor de Azure Container Registry die de installatie kopie bevat, is de web-app niet actief. In de volgende stap geeft u de verificatie gegevens voor het container register op.

1. Gebruik de volgende opdracht om de web-app te voorzien van de referenties die nodig zijn voor toegang tot het container register. Vervang `<app-name>` door de naam die u wilt gebruiken. Vervang `<acrinstance>` en `<imagename>` door de waarden van `package.location` eerder zijn geretourneerd. Vervang `<username>` en `<password>` door de ACR-aanmeldings gegevens die u eerder hebt opgehaald:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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

Op dit moment begint de web-app met het laden van de installatie kopie.

> [!IMPORTANT]
> Het kan enkele minuten duren voordat de installatie kopie is geladen. Als u de voortgang wilt bewaken, gebruikt u de volgende opdracht:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Zodra de afbeelding is geladen en de site actief is, wordt in het logboek een bericht weer gegeven met de status `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`.

Als de installatie kopie eenmaal is geïmplementeerd, kunt u de hostnaam vinden met behulp van de volgende opdracht:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Deze opdracht retourneert informatie die lijkt op de volgende hostnaam-`<app-name>.azurewebsites.net`. Gebruik deze waarde als onderdeel van de __basis-URL__ voor de service.

## <a name="use-the-web-app"></a>De web-app gebruiken

De webservice die aanvragen doorgeeft aan het model, bevindt zich op `{baseurl}/score`. Bijvoorbeeld `https://<app-name>.azurewebsites.net/score`. De volgende python-code laat zien hoe u gegevens indient naar de URL en hoe de reactie wordt weer gegeven:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het configureren van uw web-app in de documentatie [over app service in Linux](/azure/app-service/containers/) .
* Meer informatie over schalen vindt u in aan de [slag met automatisch schalen in azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* [Gebruik een SSL-certificaat in uw Azure app service](/azure/app-service/configure-ssl-certificate-in-code).
* [Configureer uw app service-app voor het gebruik van Azure Active Directory aanmelden](/azure/app-service/configure-authentication-provider-aad).
* [Een ML-model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
