---
title: Aangepaste Linux-containers beheren met CLI
description: Meer informatie over het beheren van aangepaste Linux-containers in Azure App Service vanaf de opdrachtregel. Automatiseer app-inrichting of onderhoud.
keywords: azure app service, web app, cli, linux, oss
ms.topic: article
ms.date: 08/22/2017
ms.custom: seodec18
ms.openlocfilehash: 5ca5322467402af710df68c82d747f8f8d65e142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255928"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Web App for Containers beheren met behulp van Azure CLI

Met behulp van de opdrachten in dit artikel u een web-app voor containers maken en beheren met behulp van de Azure CLI.
U de nieuwe versie van de CLI op twee manieren gaan gebruiken:

* [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli) op uw machine.
* [Azure Cloud Shell gebruiken (voorbeeld)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Een Linux App-serviceplan maken

Als u een Linux App Service Plan wilt maken, u de volgende opdracht gebruiken:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Een aangepaste Docker container Web App maken

Als u een web-app wilt maken en wilt configureren om een aangepaste Docker-container uit te voeren, u de volgende opdracht gebruiken:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>De dockercontainerlogboekregistratie activeren

Als u de docker-containerlogboekregistratie wilt activeren, u de volgende opdracht gebruiken:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>De aangepaste Docker-container wijzigen voor een bestaande web-app voor containers-app

Als u een eerder gemaakte app wilt wijzigen, van de huidige Docker-afbeelding naar een nieuwe afbeelding, u de volgende opdracht gebruiken:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Docker-afbeeldingen gebruiken uit een privéregister

U uw app configureren om afbeeldingen uit een privéregister te gebruiken. U moet de url opgeven voor uw register, gebruikersnaam en wachtwoord. Dit kan worden bereikt met de volgende opdracht:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Continue implementaties inschakelen voor aangepaste Docker-afbeeldingen

Met de volgende opdracht u de cd-functionaliteit inschakelen en de webhook-url ophalen. Deze url kan worden gebruikt om u DockerHub of Azure Container Registry repos te configureren.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Maak een Web App for Containers App met een van onze ingebouwde runtime frameworks

Als u een PHP 5.6-webapp voor containers wilt maken die u gebruiken, u de volgende opdracht gebruiken.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Framework-versie wijzigen voor een bestaande Web App for Containers-app

Als u een eerder gemaakte app wilt wijzigen, van de huidige frameworkversie naar Node.js 6.11, u de volgende opdracht gebruiken:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Git-implementaties instellen voor uw web-app

Als u Git-implementaties voor uw app wilt instellen, u de volgende opdracht gebruiken:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure App Service op Linux?](app-service-linux-intro.md)
* [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloud Shell (voorbeeld)](../../cloud-shell/overview.md)
* [Faseringsomgevingen in Azure App Service instellen](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Continue implementatie met Web App voor containers](app-service-linux-ci-cd.md)
