---
title: Azure dev Spaces inschakelen op AKS & de hulpprogram ma's aan de client zijde installeren
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Meer informatie over het inschakelen van Azure dev Spaces in een AKS-cluster en het installeren van de hulpprogram ma's aan de client zijde.
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S
ms.openlocfilehash: 0b7f6cb4a801c84df59bd5157d8c2a1a15eaaf7e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302896"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Azure dev Spaces inschakelen op een AKS-cluster en de hulpprogram ma's aan de client zijde installeren

In dit artikel wordt beschreven hoe u op verschillende manieren Azure dev-ruimten kunt inschakelen op een AKS-cluster en hoe u de hulpprogram ma's aan de client zijde installeert.

## <a name="enable-azure-dev-spaces-using-the-cli"></a>Azure-ontwikkel ruimten inschakelen met de CLI

Voordat u ontwikkel ruimten kunt inschakelen met behulp van de CLI, hebt u het volgende nodig:
* Een Azure-abonnement. Als u geen abonnement op Azure hebt, kunt u een [gratis account][az-portal-create-account] maken.
* [De Azure-cli is geïnstalleerd][install-cli].
* [Een AKS-cluster][create-aks-cli] in een [ondersteunde regio][supported-regions].

Gebruik de `use-dev-spaces` opdracht om ontwikkel ruimten in uw AKS-cluster in te scha kelen en de prompts te volgen.

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

Met de opdracht `use-dev-spaces` wordt ook de Azure dev Space CLI geïnstalleerd.

## <a name="enable-azure-dev-spaces-using-the-azure-portal"></a>Azure dev Spaces inschakelen met behulp van de Azure Portal

Voordat u ontwikkel ruimten kunt inschakelen met behulp van de Azure Portal, hebt u het volgende nodig:
* Een Azure-abonnement. Als u geen abonnement op Azure hebt, kunt u een [gratis account][az-portal-create-account] maken.
* [Een AKS-cluster][create-aks-portal] in een [ondersteunde regio][supported-regions].

Azure-ontwikkel ruimten inschakelen met behulp van de Azure Portal:
1. Meld u aan bij de [Azure-portal][az-portal].
1. Navigeer naar uw AKS-cluster.
1. Selecteer de menu opdracht *dev Spaces* .
1. Wijzig *dev-ruimten* in *Ja* en klik op *Opslaan*.

![Ontwikkel ruimten in de Azure Portal inschakelen](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Wanneer u Azure dev Spaces inschakelt met behulp van de Azure Portal **, worden geen** hulpprogram Ma's voor Azure-ontwikkel ruimten geïnstalleerd.

## <a name="install-the-client-side-tools"></a>De hulpprogram ma's aan de client zijde installeren

U kunt de Azure dev Spaces-client-side hulp middelen gebruiken om te communiceren met ontwikkel ruimten in een AKS-cluster vanaf uw lokale computer. Er zijn verschillende manieren om de hulpprogram ma's aan de client zijde te installeren:

* Installeer de [Azure dev Space-extensie][vscode-extension]in [Visual Studio code][vscode].
* Installeer de werk belasting Azure Development in [Visual Studio 2019][visual-studio].
* Installeer in Visual Studio 2017 de workload Web Development-en [Visual Studio Tools voor Kubernetes][visual-studio-k8s-tools].
* Down load en installeer de [Windows][cli-win]-, [Mac][cli-mac]-of [Linux][cli-linux] -cli.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure dev Spaces u helpt om complexere toepassingen te ontwikkelen in meerdere containers en hoe u samenwerkings ontwikkeling kunt vereenvoudigen door te werken met verschillende versies of vertakkingen van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Team ontwikkeling in azure dev Spaces][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
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
