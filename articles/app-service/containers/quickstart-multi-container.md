---
title: 'Snelstartgids: een app met meerdere containers maken'
description: Ga aan de slag met apps voor meerdere containers op Azure App Service door uw eerste app met meerdere containers te implementeren.
keywords: Azure app service, Web-app, Linux, docker, samen stellen, meerdere containers, multi-container, Web-app voor containers, meerdere containers, container, WordPress, Azure DB voor mysql, productie database met containers
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 4a0207299b480136e8054fbe3d4579dd98f45267
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430553"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Een app met meerdere containers (preview) maken met behulp van een configuratie van Docker Compose

> [!NOTE]
> Meerdere containers zijn in preview.

Met behulp van [Web App for Containers](app-service-linux-intro.md) kunt u op een flexibele manier Docker-installatiekopieën gebruiken. In deze Quick start ziet u hoe u een app met meerdere containers (preview) implementeert voor Web App for Containers in het [Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview) met behulp van een docker-samenstel configuratie.

U gaat deze snelstart in Cloud Shell doen, maar u kunt deze opdrachten ook lokaal uitvoeren met [Azure CLI](/cli/azure/install-azure-cli) (2.0.32 of hoger). 

![Voorbeeld-app met meerdere containers in Web App for Containers][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Voor deze snelstart gebruikt u het Opstellen-bestand van [Docker](https://docs.docker.com/compose/wordpress/#define-the-project). U vindt het configuratiebestanden in [Azure-voorbeelden](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Maak een map 'quickstart' in de Cloud Shell en ga er vervolgens naartoe.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Voer vervolgens de volgende opdracht uit om de voorbeeld-app-opslagplaats te klonen naar de map 'quickstart'. Ga vervolgens maar de map `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Maak een resourcegroep in Cloud Shell met de opdracht [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *US - zuid-centraal*. Als u alle ondersteunde locaties voor App Service op Linux in prijscategorie **Standard** wilt zien, voert u de opdracht [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) uit.

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

In het algemeen maakt u een resourcegroep en resources in een regio bij u in de buurt.

Wanneer de opdracht is voltooid, laat een JSON-uitvoer u de eigenschappen van de resource-groep zien.

## <a name="create-an-azure-app-service-plan"></a>Een Azure App Service-plan maken

Maak in Cloud Shell een App Service-plan in de resourcegroep met de opdracht [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).

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

Maak in de Cloud Shell-terminal een [web-app](app-service-linux-intro.md) met meerdere containers in het `myAppServicePlan` App Service-plan met de opdracht [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Vergeet niet om _\<app_name >_ te vervangen door een unieke app-naam (geldige tekens zijn `a-z`, `0-9`en `-`).

```bash
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

**Gefeliciteerd**, u hebt een app met meerdere containers gemaakt in Web App for Containers.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: WordPress-app met meerdere containers](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [Een aangepaste container configureren](configure-custom-container.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
