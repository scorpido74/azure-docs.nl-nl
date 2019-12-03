---
title: Aangepaste Linux-containers beheren met CLI
description: Meer informatie over het beheren van aangepaste Linux-containers in Azure App Service vanaf de opdracht regel. App-inrichting of-onderhoud automatiseren.
keywords: Azure app service, Web-app, CLI, Linux, oss
author: ahmedelnably
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 773c8036a345383162013f9f7103164b0f382f12
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689061"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Web App for Containers beheren met Azure CLI

Met de opdrachten in dit artikel kunt u een Web App for Containers maken en beheren met behulp van de Azure CLI.
U kunt op twee manieren beginnen met het gebruik van de nieuwe versie van de CLI:

* [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) op uw computer installeren.
* [Azure Cloud shell gebruiken (preview-versie)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Een Linux-App Service plan maken

Als u een Linux App Service-abonnement wilt maken, kunt u de volgende opdracht gebruiken:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Een aangepaste docker-container-web-app maken

Als u een web-app wilt maken en deze wilt configureren om een aangepaste docker-container uit te voeren, kunt u de volgende opdracht gebruiken:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>De logboek registratie van de docker-container activeren

Als u de logboek registratie van de docker-container wilt activeren, kunt u de volgende opdracht gebruiken:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>De aangepaste docker-container voor een bestaande Web App for Containers-app wijzigen

Als u een eerder gemaakte app wilt wijzigen van de huidige docker-installatie kopie naar een nieuwe installatie kopie, kunt u de volgende opdracht gebruiken:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Docker-installatie kopieën uit een persoonlijk REGI ster gebruiken

U kunt uw app configureren voor het gebruik van installatie kopieën uit een persoonlijk REGI ster. U moet de URL voor het REGI ster, de gebruikers naam en het wacht woord opgeven. Dit kan worden bereikt met de volgende opdracht:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Continue implementaties inschakelen voor aangepaste docker-installatie kopieën

Met de volgende opdracht kunt u de CD-functionaliteit inschakelen en de webhook-URL ophalen. Deze URL kan worden gebruikt om u DockerHub of Azure Container Registry opslag plaatsen te configureren.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Een Web App for Containers-app maken met behulp van een van onze ingebouwde runtime Frameworks

Als u een PHP 5,6 Web App for Containers-app wilt maken, kunt u de volgende opdracht gebruiken.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Framework-versie wijzigen voor een bestaande Web App for Containers-app

Als u een eerder gemaakte app wilt wijzigen van de huidige Framework-versie in node. js 6,11, kunt u de volgende opdracht gebruiken:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Git-implementaties instellen voor uw web-app

Als u Git-implementaties voor uw app wilt instellen, kunt u de volgende opdracht gebruiken:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure App Service op Linux?](app-service-linux-intro.md)
* [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloud Shell (preview-versie)](../../cloud-shell/overview.md)
* [Faseringsomgevingen in Azure App Service instellen](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Doorlopende implementatie met Web App for Containers](app-service-linux-ci-cd.md)
