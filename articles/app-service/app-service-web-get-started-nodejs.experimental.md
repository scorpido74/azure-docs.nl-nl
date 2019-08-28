---
title: Een Node.js-web-app maken - Azure App Service | Microsoft Docs
description: Implementeer in enkele minuten uw eerste Node.js-app (Hallo wereld) in Azure App Service Web Apps.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 7d81dae25b793144bfe044f8434a6df181dbcd1b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067392"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Een Node.js-web-app maken in Azure

> [!NOTE]
> In dit artikel gaat u een app implementeren in App Service onder Windows. Zie [Een Node.js-web-app maken en implementeren in Azure App Service onder Linux ](./containers/quickstart-nodejs.md) om een app te implementeren in App Service onder _Linux_.
>

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie.  In deze snelstart ziet u hoe u een Node.js-app naar Azure App Service implementeert. U gaat de web-app in [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) maken, maar u kunt deze opdrachten ook lokaal uitvoeren met [Azure CLI](/cli/azure/install-azure-cli). U implementeert de Node.js-voorbeeldcode naar de web-app met de opdracht [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

![Voorbeeld-app die wordt uitgevoerd in Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

U kunt de onderstaande stappen volgen met behulp van een Mac-, Windows- of Linux-computer. Het duurt ongeveer drie minuten om de stappen te voltooien.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Maak een map 'snelstart' in de Cloud Shell en ga er vervolgens naartoe.

```azurecli-interactive
mkdir quickstart

cd $HOME/quickstart
```

Voer vervolgens de volgende opdracht uit om de voorbeeld-app-opslagplaats te klonen naar de map 'snelstart'.

```azurecli-interactive
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

De opdracht geeft informatie weer die lijkt op het volgende voorbeeld:

```bash
Cloning into 'nodejs-docs-hello-world'...
remote: Counting objects: 40, done.
remote: Total 40 (delta 0), reused 0 (delta 0), pack-reused 40
Unpacking objects: 100% (40/40), done.
Checking connectivity... done.
```

> [!NOTE]
> In het voorbeeld met index.js wordt de poort voor luisteren ingesteld op process.env.PORT. Deze omgevingsvariabele wordt toegewezen door App Service.
>

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-scus.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-scus.md)]

## <a name="create-a-web-app"></a>Een web-app maken

Maak in Cloud Shell een web-app in het App Service-plan van `myAppServicePlan` met de opdracht [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create).

Vervang in het volgende voorbeeld `<app_name>` door een unieke naam (geldige tekens zijn `a-z`, `0-9`, en `-`).

```azurecli-interactive
# Bash and Powershell
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name>
```

Wanneer de web-app is gemaakt, toont de Azure CLI soortgelijke uitvoer als in het volgende voorbeeld:

```json
{
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

### <a name="set-nodejs-runtime"></a>Node.js-runtime instellen

Stel de Node-runtime in op 10.14.1. Voer [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) uit als u alle ondersteunde runtimes wilt zien.

```azurecli-interactive
# Bash and Powershell
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITE_NODE_DEFAULT_VERSION=10.14.1
```

Blader naar uw nieuwe web-app. Vervang `<app_name>` door een unieke app-naam.

```
http://<app_name>.azurewebsites.net
```

Zo zou uw nieuwe web-app er moeten uitzien: ![Lege pagina van web-app](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

## <a name="deploy-zip-file"></a>Het ZIP-bestand implementeren

Navigeer in Cloud Shell naar de hoofdmap van uw app en maak een nieuw ZIP-bestand voor uw voorbeeldproject.

```azurecli-interactive
cd nodejs-docs-hello-world  

zip -r myUpdatedAppFiles.zip *.*
```

Implementeer het ZIP-bestand in uw web-app met de opdracht [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Met deze opdracht worden de bestanden en mappen uit het ZIP-bestand geïmplementeerd in de standaardmap voor de App Service-toepassing (`\home\site\wwwroot`) en wordt de app opnieuw opgestart. Als er een aanvullend aangepast opbouwproces is geconfigureerd, wordt dit ook uitgevoerd. Zie onze [Kudu-documentatie](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file) voor meer informatie.

## <a name="browse-to-the-app"></a>Bladeren naar de app

Blader naar de geïmplementeerde toepassing via uw webbrowser.

```
http://<app_name>.azurewebsites.net
```

De Node.js-voorbeeldcode wordt uitgevoerd in een web-app van Azure App Service.

![Voorbeeld-app die wordt uitgevoerd in Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

> [!NOTE]
> In Azure App Service wordt de app uitgevoerd in IIS met [iisnode](https://github.com/Azure/iisnode). Voor het uitvoeren van de app met iisnode staat er een web.config-bestand in de hoofdmap van de app. Dit bestand kan worden gelezen door IIS en de instellingen voor iisnode zijn beschreven in [de GitHub-opslagplaats voor iisnode](https://github.com/Azure/iisnode/blob/master/src/samples/configuration/web.config).

**Gefeliciteerd!** U hebt uw eerste Node.js-app geïmplementeerd in App Service.

## <a name="update-and-redeploy-the-code"></a>De code bijwerken en opnieuw implementeren

Typ `code index.js` in Cloud Shell om de Cloud Shell-editor te openen.

![Code index.js](media/app-service-web-get-started-nodejs-poc/code-indexjs.png)

Breng een kleine wijziging aan in de tekst in de aanroep voor `response.end`:

```javascript
response.end("Hello Azure!");
```

Sla uw wijzigingen op en sluit de editor af. Sla op met de opdracht `^S` en sluit af met `^Q`.

Maak een ZIP-bestand en implementeer het met de opdracht [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

```azurecli-interactive
# Bash
zip -r myUpdatedAppFiles.zip *.*

az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Ga terug naar het browservenster dat is geopend in de stap **Bladeren naar de app** en vernieuw de pagina.

![Bijgewerkte voorbeeld-app die wordt uitgevoerd in Azure](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Uw nieuwe Azure-app beheren

Ga naar <a href="https://portal.azure.com" target="_blank">Azure Portal</a> om de web-app te beheren die u hebt gemaakt.

Klik in het linkermenu op **App Services** en klik op de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

De pagina Overzicht van uw web-app wordt weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen.

![App Service-pagina in Azure Portal](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

Het linkermenu bevat een aantal pagina's voor het configureren van uw app.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Node.js met MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)
