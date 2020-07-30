---
title: Azure dev Spaces inschakelen op AKS & de hulpprogram ma's aan de client zijde installeren
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Meer informatie over het inschakelen van Azure dev Spaces in een AKS-cluster en het installeren van de hulpprogram ma's aan de client zijde.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s
ms.openlocfilehash: ac010a466f7db7b829cc3d6f0687dbdbefdd7b6c
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407894"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Azure dev Spaces inschakelen op een AKS-cluster en de hulpprogram ma's aan de client zijde installeren

In dit artikel wordt beschreven hoe u op verschillende manieren Azure dev-ruimten kunt inschakelen op een AKS-cluster en hoe u de hulpprogram ma's aan de client zijde installeert.

## <a name="enable-azure-dev-spaces-using-the-cli"></a>Azure-ontwikkel ruimten inschakelen met de CLI

Voordat u ontwikkel ruimten kunt inschakelen met behulp van de CLI, hebt u het volgende nodig:
* Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account][az-portal-create-account] maken.
* [De Azure-cli is geïnstalleerd][install-cli].
* [Een AKS-cluster][create-aks-cli] in een [ondersteunde regio][supported-regions].

Gebruik de opdracht `use-dev-spaces` om Dev Spaces in te schakelen voor uw AKS-cluster en volg de prompts.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

De bovenstaande opdracht maakt ontwikkel ruimten in het *myAKSCluster* -cluster in de *myResourceGroup* -groep mogelijk en maakt een *standaard* dev-ruimte.

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

Met deze `use-dev-spaces` opdracht wordt ook de Azure dev Space cli geïnstalleerd.

## <a name="install-the-client-side-tools"></a>De hulpprogram ma's aan de client zijde installeren

U kunt de Azure dev Spaces-client-side hulp middelen gebruiken om te communiceren met ontwikkel ruimten in een AKS-cluster vanaf uw lokale computer. Er zijn verschillende manieren om de hulpprogram ma's aan de client zijde te installeren:

* Installeer de [Azure dev Space-extensie][vscode-extension]in [Visual Studio code][vscode].
* Installeer de werk belasting Azure Development in [Visual Studio 2019][visual-studio].
* Down load en installeer de [Windows][cli-win]-, [Mac][cli-mac]-of [Linux][cli-linux] -cli.

## <a name="remove-azure-dev-spaces-using-the-cli"></a>Azure-ontwikkel ruimten verwijderen met de CLI

Als u Azure dev Spaces wilt verwijderen uit uw AKS-cluster, gebruikt u de `azds remove` opdracht.

```azurecli
azds remove -g MyResourceGroup -n MyAKS
```

In de onderstaande voorbeeld uitvoer ziet u hoe u Azure dev Spaces verwijdert uit het *MyAKS* -cluster.

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Alle naam ruimten die u met Azure dev Spaces hebt gemaakt, blijven samen met hun werk belastingen, maar nieuwe werk belastingen in deze naam ruimten worden niet met Azure dev-ruimten geinstrumenteerd. Als u een bestaand van de bestaande peulen met Azure dev Spaces opnieuw start, worden er bovendien fouten weer geven. Deze bestanden moeten opnieuw worden geïmplementeerd zonder Azure dev Spaces-hulpprogram ma's. Als u Azure dev Spaces volledig uit uw cluster wilt verwijderen, verwijdert u alle peulen in alle naam ruimten waar Azure dev Spaces is ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Azure Dev Spaces u kan helpen om complexere toepassingen te ontwikkelen binnen meerdere containers en hoe u ontwikkeling in samenwerkingsverband vereenvoudigt door in verschillende ruimten met verschillende versies of vertakkingen van uw code te werken.

> [!div class="nextstepaction"]
> [Team ontwikkeling in azure dev Spaces][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
