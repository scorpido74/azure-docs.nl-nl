---
title: 'Snelstart: een app met meerdere containers maken'
description: Ga aan de slag met multicontainer-apps in Azure App Service door uw eerste multicontainer-app te implementeren.
keywords: azure app service, web app, linux, docker, compose, multicontainer, multi-container, web app voor containers, meerdere containers, container, wordpress, azure db voor mysql, productiedatabase met containers
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 62e34859775cb8c574d8d463f636ed81dce8ece3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80045876"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Een app met meerdere containers (preview) maken met behulp van een configuratie van Docker Compose

> [!NOTE]
> Multi-container is in preview.

Met behulp van [Web App for Containers](app-service-linux-intro.md) kunt u op een flexibele manier Docker-installatiekopieën gebruiken. Met deze quickstart u zien hoe u een multi-container-app (voorbeeld) implementeert naar Web App voor containers in de [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) met behulp van een Docker Compose-configuratie.

U gaat deze snelstart in Cloud Shell doen, maar u kunt deze opdrachten ook lokaal uitvoeren met [Azure CLI](/cli/azure/install-azure-cli) (2.0.32 of hoger). 

![Voorbeeld-app met meerdere containers in Web App for Containers][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Voor deze snelstart gebruikt u het Opstellen-bestand van [Docker](https://docs.docker.com/compose/wordpress/#define-the-project). U vindt het configuratiebestanden in [Azure-voorbeelden](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Maak een map 'snelstart' in de Cloud Shell en ga er vervolgens naartoe.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Voer vervolgens de volgende opdracht uit om de voorbeeld-app-opslagplaats te klonen naar de map 'snelstart'. Ga vervolgens maar de map `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Maak in de Cloud Shell een [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) resourcegroep met de opdracht. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - zuid-centraal*. Als u alle ondersteunde locaties voor App Service op Linux in prijscategorie **Standard** wilt zien, voert u de opdracht [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) uit.

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

In het algemeen maakt u een resourcegroep en resources in een regio bij u in de buurt.

Wanneer de opdracht is voltooid, laat een JSON-uitvoer u de eigenschappen van de resource-groep zien.

## <a name="create-an-azure-app-service-plan"></a>Een Azure App Service-plan maken

Maak in de Cloud Shell een App Service-abonnement in de resourcegroep met de [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) opdracht.

In het volgende voorbeeld wordt een App Service-plan gemaakt met de naam `myAppServicePlan` in de prijscategorie **Standard** (`--sku S1`) en in een Linux-container (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Wanneer het App Service-plan is gemaakt, toont de Azure CLI soortgelijke informatie als in het volgende voorbeeld:

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="create-a-docker-compose-app"></a>Een Docker Compose-app maken

> [!NOTE]
> Docker Compose op Azure App Services heeft momenteel een limiet van 4.000 tekens op dit moment.

Maak in de Cloud Shell-terminal een [web-app](app-service-linux-intro.md) met meerdere containers in het `myAppServicePlan` App Service-plan met de opdracht [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Vergeet niet om _ \<app_name>_ te vervangen door een `a-z` `0-9`unieke `-`app-naam (geldige tekens zijn , , en ).

```azurecli
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Wanneer de web-app is gemaakt, toont de Azure CLI soortgelijke uitvoer als in het volgende voorbeeld:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Bladeren naar de app

Blader naar de geïmplementeerde app in (`http://<app_name>.azurewebsites.net`). Het laden van de app kan een paar minuten duren. Als u een foutbericht ontvangt, wacht u een paar minuten en vernieuwt u de browser.

![Voorbeeld-app met meerdere containers in Web App for Containers][1]

**Gefeliciteerd,** je hebt een multi-container app gemaakt in Web App voor containers.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: WordPress-app voor meerdere containers](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [Een aangepaste container configureren](configure-custom-container.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
