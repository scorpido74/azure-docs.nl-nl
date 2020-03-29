---
title: ML-modellen implementeren in Azure Functions Apps (voorbeeld)
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van Azure Machine Learning om een model te implementeren in een Azure Functions App.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: d03a3d482d147d3bc69354ee09dfe0b187610a09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927442"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Een machine learning-model implementeren in Azure-functies (voorbeeld)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het implementeren van een model vanuit Azure Machine Learning als functie-app in Azure-functies.

> [!IMPORTANT]
> Hoewel zowel Azure Machine Learning als Azure Functions algemeen beschikbaar zijn, is de mogelijkheid om een model te verpakken vanuit de Machine Learning-service voor functies in preview.While both Azure Machine Learning and Azure Functions are generally available, the ability to package a model from the Machine Learning service for Functions is in preview.

Met Azure Machine Learning u Docker-afbeeldingen maken op basis van getrainde machine learning-modellen. Azure Machine Learning heeft nu de preview-functionaliteit om deze machine learning-modellen in te bouwen in functie-apps, die kunnen worden [geïmplementeerd in Azure-functies.](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container)

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
    >
    > Standaard bij het verpakken van functies wordt de invoer behandeld als tekst. Als u geïnteresseerd bent in het consumeren van de ruwe bytes van de invoer (bijvoorbeeld voor Blob-triggers), moet u [AMLRequest](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data)gebruiken om ruwe gegevens te accepteren.


* **Afhankelijkheden,** zoals helperscripts of Python/Conda-pakketten die nodig zijn om het invoerscript of -model uit te voeren

Deze entiteiten zijn ingekapseld in een __gevolgtrekkingconfiguratie.__ De deductieconfiguratie verwijst naar het invoerscript en andere afhankelijkheden.

> [!IMPORTANT]
> Wanneer u een gevolgtrekkingsconfiguratie maakt voor gebruik met Azure-functies, moet u een [omgevingsobject](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) gebruiken. Houd er rekening mee dat als u een aangepaste omgeving definieert, u azureml-standaardinstellingen moet toevoegen met versie >= 1.0,45 als pip-afhankelijkheid. Dit pakket bevat de functionaliteit die nodig is om het model als webservice te hosten. In het volgende voorbeeld wordt het maken van een omgevingsobject en het gebruik ervan met een gevolgtrekkingsconfiguratie weergegeven:
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

Zie [Omgevingen maken en beheren voor training en implementatie voor](how-to-use-environments.md)meer informatie over omgevingen.

Zie Modellen implementeren met Azure [Machine Learning voor](how-to-deploy-and-where.md)meer informatie over de configuratie van gevolgtrekkingen.

> [!IMPORTANT]
> Wanneer u naar functies wordt geïmplementeerd, hoeft u geen __implementatieconfiguratie__te maken.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Het SDK-voorbeeldpakket installeren voor ondersteuning voor functies

Als u pakketten voor Azure-functies wilt maken, moet u het SDK-voorbeeldpakket installeren.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>De installatiekopie maken

Als u de Docker-afbeelding wilt maken die is geïmplementeerd in Azure-functies, gebruikt u [azureml.contrib.functions.package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) of de specifieke pakketfunctie voor de trigger die u wilt gebruiken. In het volgende codefragment wordt uitgelegd hoe u een nieuw pakket maakt met een blobtrigger van het model en de conclusieconfiguratie:

> [!NOTE]
> Het codefragment gaat `model` ervan uit dat `inference_config` een geregistreerd model bevat en dat de configuratie voor de gevolgtrekkingsomgeving bevat. Zie [Modellen implementeren met Azure Machine Learning voor](how-to-deploy-and-where.md)meer informatie.

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Wanneer `show_output=True`wordt de uitvoer van het Docker-bouwproces weergegeven. Zodra het proces is voltooid, is de afbeelding gemaakt in het Azure Container Registry voor uw werkruimte. Zodra de afbeelding is gemaakt, wordt de locatie in uw Azure Container Registry weergegeven. De geretourneerde locatie `<acrinstance>.azurecr.io/package@sha256:<hash>`is in de indeling .

> [!NOTE]
> Verpakking voor functies ondersteunt momenteel HTTP Triggers, Blob triggers en Service bus triggers. Zie [Azure Functions-bindingen](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns)voor meer informatie over triggers.

> [!IMPORTANT]
> Sla de locatiegegevens op, omdat deze wordt gebruikt bij het implementeren van de afbeelding.

## <a name="deploy-image-as-a-web-app"></a>Afbeelding implementeren als web-app

1. Gebruik de volgende opdracht om de inloggegevens op te halen voor het Azure Container Registry dat de afbeelding bevat. Vervang `<myacr>` de eerder geretourneerde waarde van `package.location`: 

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

    In dit voorbeeld wordt een`--sku B1` _Linux-basisprijslaag_ () gebruikt.

    > [!IMPORTANT]
    > Afbeeldingen die zijn gemaakt door Azure Machine `--is-linux` Learning gebruiken Linux, dus u moet de parameter gebruiken.

1. Maak het opslagaccount dat u wilt gebruiken voor de webtaakopslag en ontvang de verbindingstekenreeks. Vervang `<webjobStorage>` door de naam die u wilt gebruiken.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Als u de functie-app wilt maken, gebruikt u de volgende opdracht. Vervang `<app-name>` door de naam die u wilt gebruiken. Vervang `<acrinstance>` `<imagename>` en met de `package.location` waarden van eerder geretourneerd. Vervang `<webjobStorage>` de naam van het opslagaccount uit de vorige stap:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > Op dit moment is de functie-app gemaakt. Aangezien u echter de verbindingstekenreeks voor de blobtrigger of referenties niet hebt verstrekt aan het Azure Container Registry dat de afbeelding bevat, is de functie-app niet actief. In de volgende stappen geeft u de verbindingstekenreeks en de verificatiegegevens voor het containerregister. 

1. Maak het opslagaccount dat u wilt gebruiken voor de blobtriggeropslag en download de verbindingstekenreeks. Vervang `<triggerStorage>` door de naam die u wilt gebruiken.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactiv
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Neem deze verbindingstekenreeks op die u aan de functie-app wilt verstrekken. We zullen het later gebruiken als we vragen om`<triggerConnectionString>`

1. Maak de containers voor de invoer en uitvoer in het opslagaccount. Vervang `<triggerConnectionString>` de verbindingstekenreeks die eerder is geretourneerd:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Als u de tekenreeks voor de triggerverbinding wilt koppelen aan de functie-app, gebruikt u de volgende opdracht. Vervang `<app-name>` door de naam van de functie-app. Vervang `<triggerConnectionString>` de verbindingstekenreeks die eerder is geretourneerd:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. U moet de tag die is gekoppeld aan de gemaakte container ophalen met de volgende opdracht. Vervang `<username>` door de gebruikersnaam die eerder is geretourneerd uit het containerregister:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Sla de geretourneerde waarde op, `imagetag` deze wordt gebruikt als de in de volgende stap.

1. Als u de functie-app de referenties wilt geven die nodig zijn om toegang te krijgen tot het containerregister, gebruikt u de volgende opdracht. Vervang `<app-name>` door de naam van de functie-app. Vervang `<acrinstance>` `<imagetag>` en met de waarden uit de AZ CLI-aanroep in de vorige stap. Vervang `<username>` `<password>` en met de eerder opgehaalde ACR-inloggegevens:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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

Op dit punt begint de functie-app met het laden van de afbeelding.

> [!IMPORTANT]
> Het kan enkele minuten duren voordat de afbeelding is geladen. U de voortgang controleren met de Azure Portal.

## <a name="test-the-deployment"></a>De implementatie testen

Zodra de afbeelding is geladen en de app beschikbaar is, gebruikt u de volgende stappen om de app te activeren:

1. Maak een tekstbestand met de gegevens die het score.py bestand verwacht. In het volgende voorbeeld wordt gewerkt met een score.py dat een array van 10 nummers verwacht:

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > Het formaat van de gegevens is afhankelijk van wat uw score.py en model verwacht.

2. Gebruik de volgende opdracht om dit bestand te uploaden naar de invoercontainer in de eerder gemaakte blob met triggeropslag. Vervang `<file>` de naam van het bestand met de gegevens. Vervang `<triggerConnectionString>` door de verbindingstekenreeks die eerder is geretourneerd. In dit `input` voorbeeld is de naam van de eerder gemaakte invoercontainer. Als u een andere naam hebt gebruikt, vervangt u deze waarde:

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. Als u de uitvoer wilt weergeven die door de functie wordt geproduceerd, gebruikt u de volgende opdracht om de gegenereerde uitvoerbestanden weer te geven. Vervang `<triggerConnectionString>` door de verbindingstekenreeks die eerder is geretourneerd. In dit `output` voorbeeld is de naam van de uitvoercontainer die eerder is gemaakt. Als u een andere naam hebt gebruikt, vervangt u deze waarde::

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    De uitvoer van deze `sample_input_out.json`opdracht is vergelijkbaar met .

4. Als u het bestand wilt downloaden en de inhoud wilt inspecteren, gebruikt u de volgende opdracht. Vervang `<file>` door de bestandsnaam die door de vorige opdracht is geretourneerd. Vervang `<triggerConnectionString>` de verbindingstekenreeks die eerder is geretourneerd: 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Zodra de opdracht is voltooid, opent u het bestand. Het bevat de gegevens die door het model worden geretourneerd.

Zie de functie Een functie [maken die wordt geactiveerd door Azure Blob-opslag](/azure/azure-functions/functions-create-storage-blob-triggered-function) voor meer informatie over het gebruik van blobtriggers.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het configureren van uw functie-app in de [documentatie Functies.](/azure/azure-functions/functions-create-function-linux-custom-image)
* Meer informatie over Blob-opslagactiveert [Azure Blob-opslagbindingen](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).
* [Implementeer uw model in Azure App Service](how-to-deploy-app-service.md).
* [Een ML-model gebruiken dat is geïmplementeerd als webservice](how-to-consume-web-service.md)
* [Naslaginformatie voor API](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)
