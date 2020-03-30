---
title: ML-modellen implementeren voor Azure App Service (voorbeeld)
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van Azure Machine Learning om een model te implementeren in een Web App in Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: 3e6cfde20d9f4d56af836e06b0c9a84010dea47b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282814"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Een machine learning-model implementeren in Azure App Service (voorbeeld)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het implementeren van een model vanuit Azure Machine Learning als web-app in Azure App Service.

> [!IMPORTANT]
> Hoewel zowel Azure Machine Learning als Azure App Service algemeen beschikbaar zijn, is de mogelijkheid om een model te implementeren van de Machine Learning-service naar App Service in preview.

Met Azure Machine Learning u Docker-afbeeldingen maken op basis van getrainde machine learning-modellen. Deze afbeelding bevat een webservice die gegevens ontvangt, deze naar het model verzendt en vervolgens het antwoord retourneert. Azure App Service kan worden gebruikt om de afbeelding te implementeren en biedt de volgende functies:

* Geavanceerde [verificatie](/azure/app-service/configure-authentication-provider-aad) voor verbeterde beveiliging. Verificatiemethoden omvatten zowel Azure Active Directory als multi-factor auth.
* [Automatisch schalen](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) zonder opnieuw te hoeven worden geïmplementeerd.
* [TLS-ondersteuning](/azure/app-service/configure-ssl-certificate-in-code) voor veilige communicatie tussen clients en de service.

Zie het [overzicht appservice](/azure/app-service/overview)voor meer informatie over functies van Azure App Service.

> [!IMPORTANT]
> Als u de mogelijkheid nodig hebt om de scoregegevens te registreren die worden gebruikt met uw geïmplementeerde model of de resultaten van het scoren, moet u in plaats daarvan implementeren in Azure Kubernetes Service. Zie [Gegevens verzamelen over uw productiemodellen voor](how-to-enable-data-collection.md)meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie het artikel [Een werkruimte maken](how-to-manage-workspace.md) voor meer informatie.
* De [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Een getraind machine learning-model dat in uw werkruimte is geregistreerd. Als u geen model hebt, gebruikt u de [zelfstudie Afbeeldingsclassificatie: treinmodel](tutorial-train-models-with-aml.md) om er een te trainen en te registreren.

    > [!IMPORTANT]
    > De codefragmenten in dit artikel gaan ervan uit dat u de volgende variabelen hebt ingesteld:
    >
    > * `ws`- Uw Azure Machine Learning-werkruimte.
    > * `model`- Het geregistreerde model dat zal worden ingezet.
    > * `inference_config`- De gevolgtrekkingsconfiguratie voor het model.
    >
    > Zie Modellen implementeren met Azure [Machine Learning voor](how-to-deploy-and-where.md)meer informatie over het instellen van deze variabelen.

## <a name="prepare-for-deployment"></a>Implementatie voorbereiden

Voordat u wordt geïmplementeerd, moet u definiëren wat er nodig is om het model als webservice uit te voeren. In de volgende lijst worden de basisitems beschreven die nodig zijn voor een implementatie:

* Een __toegangsscript__. Dit script accepteert aanvragen, scoort de aanvraag met behulp van het model en retourneert de resultaten.

    > [!IMPORTANT]
    > Het invoerscript is specifiek voor uw model; het moet inzicht krijgen in de indeling van de binnenkomende aanvraaggegevens, de indeling van de gegevens die door uw model worden verwacht en de indeling van de gegevens die aan clients worden geretourneerd.
    >
    > Als de aanvraaggegevens een indeling hebben die niet bruikbaar is voor uw model, kan het script deze omzetten in een acceptabele indeling. Het kan ook het antwoord transformeren voordat het terugkeert naar de client.

    > [!IMPORTANT]
    > De Azure Machine Learning SDK biedt geen manier voor de webservice toegang tot uw gegevensarchief of gegevenssets. Als u het geïmplementeerde model nodig hebt om toegang te krijgen tot gegevens die buiten de implementatie zijn opgeslagen, zoals in een Azure Storage-account, moet u een aangepaste codeoplossing ontwikkelen met behulp van de relevante SDK. Bijvoorbeeld de [Azure Storage SDK voor Python](https://github.com/Azure/azure-storage-python).
    >
    > Een ander alternatief dat kan werken voor uw scenario is [batch voorspellingen,](how-to-use-parallel-run-step.md)die wel toegang bieden tot datastores bij het scoren.

    Zie Modellen implementeren met [Azure Machine Learning voor](how-to-deploy-and-where.md)meer informatie over invoerscripts.

* **Afhankelijkheden,** zoals helperscripts of Python/Conda-pakketten die nodig zijn om het invoerscript of -model uit te voeren

Deze entiteiten zijn ingekapseld in een __gevolgtrekkingconfiguratie.__ De deductieconfiguratie verwijst naar het invoerscript en andere afhankelijkheden.

> [!IMPORTANT]
> Wanneer u een gevolgtrekkingsconfiguratie maakt voor gebruik met Azure App Service, moet u een [omgevingsobject](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) gebruiken. Houd er rekening mee dat als u een aangepaste omgeving definieert, u azureml-standaardinstellingen moet toevoegen met versie >= 1.0,45 als pip-afhankelijkheid. Dit pakket bevat de functionaliteit die nodig is om het model als webservice te hosten. In het volgende voorbeeld wordt het maken van een omgevingsobject en het gebruik ervan met een gevolgtrekkingsconfiguratie weergegeven:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
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

Zie [Omgevingen maken en beheren voor training en implementatie voor](how-to-use-environments.md)meer informatie over omgevingen.

Zie Modellen implementeren met Azure [Machine Learning voor](how-to-deploy-and-where.md)meer informatie over de configuratie van gevolgtrekkingen.

> [!IMPORTANT]
> Wanneer u wordt geïmplementeerd in Azure App Service, hoeft u geen __implementatieconfiguratie__te maken.

## <a name="create-the-image"></a>De installatiekopie maken

Als u de Docker-afbeelding wilt maken die is geïmplementeerd in Azure App Service, gebruikt u [Model.package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-). In het volgende codefragment wordt uitgelegd hoe u een nieuwe afbeelding van het model en de conclusieconfiguratie maken:

> [!NOTE]
> Het codefragment gaat `model` ervan uit dat `inference_config` een geregistreerd model bevat en dat de configuratie voor de gevolgtrekkingsomgeving bevat. Zie [Modellen implementeren met Azure Machine Learning voor](how-to-deploy-and-where.md)meer informatie.

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Wanneer `show_output=True`wordt de uitvoer van het Docker-bouwproces weergegeven. Zodra het proces is voltooid, is de afbeelding gemaakt in het Azure Container Registry voor uw werkruimte. Zodra de afbeelding is gemaakt, wordt de locatie in uw Azure Container Registry weergegeven. De geretourneerde locatie `<acrinstance>.azurecr.io/package:<imagename>`is in de indeling . Bijvoorbeeld `myml08024f78fd10.azurecr.io/package:20190827151241`.

> [!IMPORTANT]
> Sla de locatiegegevens op, omdat deze wordt gebruikt bij het implementeren van de afbeelding.

## <a name="deploy-image-as-a-web-app"></a>Afbeelding implementeren als web-app

1. Gebruik de volgende opdracht om de inloggegevens op te halen voor het Azure Container Registry dat de afbeelding bevat. Vervang `<acrinstance>` de eerder geretourneerde `package.location`waarde door th e van :

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

    Sla de waarde op voor __gebruikersnaam__ en een van de __wachtwoorden.__

1. Als u nog geen resourcegroep of app-serviceplan hebt om de service te implementeren, laten de volgende opdrachten zien hoe u beide maakt:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    In dit voorbeeld wordt een`--sku B1` __basisprijslaag__ ( ) gebruikt.

    > [!IMPORTANT]
    > Afbeeldingen die zijn gemaakt door Azure Machine `--is-linux` Learning gebruiken Linux, dus u moet de parameter gebruiken.

1. Als u de web-app wilt maken, gebruikt u de volgende opdracht. Vervang `<app-name>` door de naam die u wilt gebruiken. Vervang `<acrinstance>` `<imagename>` en met de `package.location` eerder geretourneerde waarden:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    Met deze opdracht worden gegevens geretourneerd die vergelijkbaar zijn met het volgende JSON-document:

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
    > Op dit moment is de web-app gemaakt. Omdat u de referenties echter niet hebt verstrekt aan het Azure Container Registry dat de afbeelding bevat, is de web-app niet actief. In de volgende stap verstrekt u de verificatiegegevens voor het containerregister.

1. Als u de web-app de referenties wilt verstrekken die nodig zijn om toegang te krijgen tot het containerregister, gebruikt u de volgende opdracht. Vervang `<app-name>` door de naam die u wilt gebruiken. Vervang `<acrinstance>` `<imagename>` en met de `package.location` waarden van eerder geretourneerd. Vervang `<username>` `<password>` en met de eerder opgehaalde ACR-inloggegevens:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Met deze opdracht worden gegevens geretourneerd die vergelijkbaar zijn met het volgende JSON-document:

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

Op dit punt begint de web-app met het laden van de afbeelding.

> [!IMPORTANT]
> Het kan enkele minuten duren voordat de afbeelding is geladen. Als u de voortgang wilt controleren, gebruikt u de volgende opdracht:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Zodra de afbeelding is geladen en de site actief is, `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`wordt in het logboek een bericht weergegeven waarin staat .

Zodra de afbeelding is geïmplementeerd, u de hostnaam vinden met behulp van de volgende opdracht:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Met deze opdracht retourneert informatie `<app-name>.azurewebsites.net`die vergelijkbaar is met de volgende hostnaam - . Gebruik deze waarde als onderdeel van de __basisurl__ voor de service.

## <a name="use-the-web-app"></a>De web-app gebruiken

De webservice die aanvragen doorgeeft `{baseurl}/score`aan het model bevindt zich op . Bijvoorbeeld `https://<app-name>.azurewebsites.net/score`. De volgende Python-code laat zien hoe u gegevens naar de URL verzendt en het antwoord weergeeft:

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

* Meer informatie over het configureren van uw web-app in de [App-service op Linux-documentatie.](/azure/app-service/containers/)
* Meer informatie over schalen in [Aan de slag met Autoscale in Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* [Gebruik een TLS/SSL-certificaat in uw Azure App Service.](/azure/app-service/configure-ssl-certificate-in-code)
* [Uw App Service-app configureren om aanmelding in Azure Active Directory te gebruiken.](/azure/app-service/configure-authentication-provider-aad)
* [Een ML-model gebruiken dat is geïmplementeerd als webservice](how-to-consume-web-service.md)
