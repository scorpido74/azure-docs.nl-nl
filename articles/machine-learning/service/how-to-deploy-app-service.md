---
title: Ml-modellen implementeren op Azure App Service (preview-versie)
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de Azure Machine Learning-service voor het implementeren van een model in een web-app in Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: a5fd376a6da70ed68baedf44fd4c2cc47e68d3cf
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872374"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Een machine learning model implementeren op Azure App Service (preview-versie)

Meer informatie over het implementeren van een model van de Azure Machine Learning-service als een web-app in Azure App Service.

> [!IMPORTANT]
> Hoewel zowel Azure Machine Learning-service als Azure App Service algemeen beschikbaar zijn, is de mogelijkheid om een model van de Machine Learning-service naar App Service te implementeren, een preview-versie.

Met Azure Machine Learning-service kunt u docker-installatie kopieën maken op basis van getrainde machine learning modellen. Deze installatie kopie bevat een webservice waarmee gegevens worden ontvangen, naar het model worden verzonden en het antwoord vervolgens wordt geretourneerd. Azure App Service kan worden gebruikt om de installatie kopie te implementeren en biedt de volgende functies:

* Geavanceerde [verificatie](/azure/app-service/configure-authentication-provider-aad) voor verbeterde beveiliging. Verificatie methoden zijn zowel Azure Active Directory als multi-factor Authentication.
* [Automatisch schalen](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) zonder opnieuw te hoeven implementeren.
* [SSL-ondersteuning](/azure/app-service/app-service-web-ssl-cert-load) voor beveiligde communicatie tussen clients en de service.

Zie [app service-overzicht](/azure/app-service/overview)voor meer informatie over de functies van Azure app service.

> [!IMPORTANT]
> Als u de mogelijkheid wilt bieden om de Score gegevens die worden gebruikt met uw geïmplementeerde model of de resultaten van de score te registreren, moet u in plaats daarvan implementeren naar de Azure Kubernetes-service. Zie [gegevens verzamelen in uw productie modellen](how-to-enable-data-collection.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een werkruimte van Azure Machine Learning-service. Zie het artikel [een werk ruimte maken](how-to-manage-workspace.md) voor meer informatie.
* Een getraind machine learning model dat is geregistreerd in uw werk ruimte. Als u geen model hebt, gebruikt u de [zelf studie voor installatie kopie classificatie: Train model](tutorial-train-models-with-aml.md) om er een te trainen en te registreren.

    > [!IMPORTANT]
    > In de code fragmenten in dit artikel wordt ervan uitgegaan dat u de volgende variabelen hebt ingesteld:
    >
    > * `ws`-Uw Azure Machine Learning-werk ruimte.
    > * `model`-Het geregistreerde model dat wordt geïmplementeerd.
    > * `inference_config`-De configuratie voor het afwachten van het model.
    >
    > Zie [modellen implementeren met de Azure machine learning-service](how-to-deploy-and-where.md)voor meer informatie over het instellen van deze variabelen.

## <a name="prepare-for-deployment"></a>Voorbereiden voor implementatie

Voordat u implementeert, moet u definiëren wat er nodig is om het model als een webservice uit te voeren. In de volgende lijst worden de basis items beschreven die nodig zijn voor een implementatie:

* Een __invoer script__. Met dit script worden aanvragen geaccepteerd, wordt de aanvraag met het model gescoord en worden de resultaten geretourneerd.

    > [!IMPORTANT]
    > Het invoer script is specifiek voor uw model. het moet inzicht krijgen in de indeling van de gegevens van de inkomende aanvraag, de indeling van de gegevens die worden verwacht door uw model en de indeling van de gegevens die aan clients worden geretourneerd.
    >
    > Als de gegevens van de aanvraag een indeling hebben die niet kan worden gebruikt door uw model, kan het script deze omzetten in een acceptabele indeling. Het kan ook de reactie transformeren voordat deze naar de client wordt teruggekeerd.

    > [!IMPORTANT]
    > De Azure Machine Learning SDK biedt geen manier om toegang te krijgen tot uw Data Store-of gegevens sets via de webservice. Als u het geïmplementeerde model nodig hebt om toegang te krijgen tot gegevens die buiten de implementatie zijn opgeslagen, zoals in een Azure Storage-account, moet u een oplossing voor aangepaste code ontwikkelen met behulp van de relevante SDK. Bijvoorbeeld de [Azure Storage SDK voor python](https://github.com/Azure/azure-storage-python).
    >
    > Een ander alternatief dat kan worden gebruikt voor uw scenario is [batch](how-to-run-batch-predictions.md)voorspellingen. Dit biedt ook toegang tot gegevens opslag in de score.

    Zie [modellen implementeren met de Azure machine learning-service](how-to-deploy-and-where.md)voor meer informatie over invoer scripts.

* **Afhankelijkheden**, zoals hulp scripts of python/Conda-pakketten die zijn vereist voor het uitvoeren van het script of model van de vermelding

Deze entiteiten worden ingekapseld in een Afleidings __configuratie__. De configuratie voor afwijzen verwijst naar het script voor de vermelding en andere afhankelijkheden.

> [!IMPORTANT]
> Wanneer u een configuratie voor afwijzen maakt voor gebruik met Azure App Service, moet u [](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) een omgevings object gebruiken. In het volgende voor beeld ziet u hoe u een omgevings object maakt en dit gebruikt met een configuratie voor ingaand gebruik:
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

Zie [modellen implementeren met de Azure machine learning-service](how-to-deploy-and-where.md)voor meer informatie over het afnemen van de configuratie.

> [!IMPORTANT]
> Wanneer u naar Azure App Service implementeert, hoeft u geen __implementatie configuratie__te maken.

## <a name="create-the-image"></a>De installatiekopie maken

Als u de docker-installatie kopie wilt maken die is geïmplementeerd op Azure App Service, gebruikt u [model. package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config--generate-dockerfile-false-). Het volgende code fragment laat zien hoe u een nieuwe installatie kopie kunt bouwen op basis van het model en de configuratie voor het afwijzen van de afleiding:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Wanneer `show_output=True`wordt de uitvoer van het docker-bouw proces weer gegeven. Zodra het proces is voltooid, is de afbeelding gemaakt in de Azure Container Registry voor uw werk ruimte.

## <a name="deploy-image-as-a-web-app"></a>Een installatie kopie implementeren als een web-app

1. Selecteer uw Azure Machine Learning-werk ruimte in de [Azure Portal](https://portal.azure.com). Gebruik in de sectie __overzicht__ de __register__ koppeling om toegang te krijgen tot de Azure container Registry voor de werk ruimte.

    ![Scherm afbeelding van het overzicht voor de werk ruimte](media/how-to-deploy-app-service/workspace-overview.png)

2. Selecteer in de Azure Container Registry __opslag__plaatsen en selecteer vervolgens de naam van de __installatie kopie__ die u wilt implementeren. Voor de versie die u wilt implementeren, selecteert u de vermelding __...__ en implementeert __u deze in web app__.

    ![Scherm opname van implementeren vanuit ACR naar een web-app](media/how-to-deploy-app-service/deploy-to-web-app.png)

3. Als u de Web-App wilt maken, geeft u een site naam, een abonnement, een resource groep op en selecteert u het app service-plan/de locatie. Selecteer tot slot __maken__.

    ![Scherm afbeelding van het dialoog venster nieuwe web-app](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>De web-app gebruiken

Selecteer de web-app die u in de vorige stap hebt gemaakt in de [Azure Portal](https://portal.azure.com). Kopieer de __URL__in de sectie __overzicht__ . Deze waarde is de __basis-URL__ van de service.

![Scherm afbeelding van het overzicht voor de web-app](media/how-to-deploy-app-service/web-app-overview.png)

De webservice die aanvragen doorgeeft aan het model, bevindt zich op `{baseurl}/score`. Bijvoorbeeld `https://mywebapp.azurewebsites.net/score`. De volgende python-code laat zien hoe u gegevens indient naar de URL en hoe de reactie wordt weer gegeven:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

print(headers)
    
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

* Zie de documentatie [over app service in Linux](/azure/app-service/containers/) voor meer informatie over het configureren van uw web-app.
* Zie [aan de slag met automatisch schalen in azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)voor meer informatie over schalen.
* Zie [een SSL-certificaat gebruiken in uw Azure app service](/azure/app-service/app-service-web-ssl-cert-load)voor meer informatie over SSL-ondersteuning.
* Zie [uw app service-app configureren voor het gebruik van Azure Active Directory-aanmelding](/azure/app-service/configure-authentication-provider-aad)voor meer informatie over verificatie.
* [Een ML-Model dat is geïmplementeerd als een webservice gebruiken](how-to-consume-web-service.md)