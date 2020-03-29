---
title: Azure Dev Spaces op AKS inschakelen & de hulpprogramma's aan clientzijde installeren
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Meer informatie over het inschakelen van Azure Dev Spaces op een AKS-cluster en het installeren van de client-side tools.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: a6b3be5ceba5e60b99b2f75e060f3321cd3151f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898956"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Azure Dev Spaces inschakelen op een AKS-cluster en de hulpprogramma's aan de clientzijde installeren

In dit artikel ziet u verschillende manieren om Azure Dev Spaces in te schakelen op een AKS-cluster en de hulpprogramma's aan de clientzijde te installeren.

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>Azure Dev-ruimten in- of verwijderen met de CLI

Voordat u Dev Spaces inschakelen met de CLI, hebt u het:
* Een Azure-abonnement. Als u geen Azure-abonnement hebt, u een [gratis account][az-portal-create-account]maken.
* [De Azure CLI geïnstalleerd][install-cli].
* [Een AKS-cluster][create-aks-cli] in een [ondersteunde regio][supported-regions].

Gebruik `use-dev-spaces` de opdracht om Dev Spaces in te schakelen op uw AKS-cluster en volg de aanwijzingen.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Met de bovenstaande opdracht schakelt U Spaties in op het *cluster myAKSCluster* in de *groep myResourceGroup* in en wordt een standaarddev-ruimte gemaakt. *default*

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

De `use-dev-spaces` opdracht installeert ook de Azure Dev Spaces CLI.

Als u Azure Dev Spaces uit uw `azds remove` AKS-cluster wilt verwijderen, gebruikt u de opdracht. Bijvoorbeeld:

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Met de bovenstaande opdracht worden Azure Dev Spaces verwijderd uit het *MyAKS-cluster* in *MyResourceGroup*. Alle naamruimten die u met Azure Dev Spaces hebt gemaakt, blijven samen met hun workloads, maar nieuwe workloads in die naamruimten worden niet uitgevoerd met Azure Dev Spaces. Als u bovendien bestaande pods opnieuw start die zijn uitgerust met Azure Dev Spaces, ziet u mogelijk fouten. Deze pods moeten opnieuw worden geïmplementeerd zonder Azure Dev Spaces-tooling. Als u Azure Dev Spaces volledig uit uw cluster wilt verwijderen, verwijdert u alle pods in alle naamruimten waar Azure Dev Spaces is ingeschakeld.

## <a name="enable-or-remove-azure-dev-spaces-using-the-azure-portal"></a>Azure Dev-ruimten in- of verwijderen met behulp van de Azure-portal

Voordat u Dev Spaces inschakelen met de Azure-portal, moet u het:
* Een Azure-abonnement. Als u geen Azure-abonnement hebt, u een [gratis account][az-portal-create-account]maken.
* [Een AKS-cluster][create-aks-portal] in een [ondersteunde regio][supported-regions].

Ga als eerste voor azure dev-ruimten met de Azure-portal:
1. Meld u aan bij [Azure Portal][az-portal].
1. Navigeer naar uw AKS-cluster.
1. Selecteer het *menu-item Dev Spaces.*
1. Schakel *Spaties inschakelen wijzigen* in *Ja* en klik op *Opslaan*.

![Dev-spaties inschakelen in de Azure-portal](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Als u Azure Dev Spaces inschakelt met de Azure-portal, worden **geen** client-side tools voor Azure Dev Spaces geïnstalleerd.

Als u Azure Dev Spaces uit uw AKS-cluster wilt verwijderen, wijzigt u *Dev Spaces inschakelen* in *Nee* en klikt u op *Opslaan*. Alle naamruimten die u met Azure Dev Spaces hebt gemaakt, blijven samen met hun workloads, maar nieuwe workloads in die naamruimten worden niet uitgevoerd met Azure Dev Spaces. Als u bovendien bestaande pods opnieuw start die zijn uitgerust met Azure Dev Spaces, ziet u mogelijk fouten. Deze pods moeten opnieuw worden geïmplementeerd zonder Azure Dev Spaces-tooling. Als u Azure Dev Spaces volledig uit uw cluster wilt verwijderen, verwijdert u alle pods in alle naamruimten waar Azure Dev Spaces is ingeschakeld.

## <a name="install-the-client-side-tools"></a>De hulpprogramma's aan de clientzijde installeren

U de hulpprogramma's aan de clientzijde van Azure Dev Spaces gebruiken om vanaf uw lokale machine te communiceren met dev-ruimten op een AKS-cluster. Er zijn verschillende manieren om de client-side tools te installeren:

* Installeer in [Visual Studio Code][vscode]de azure [dev spaces-extensie][vscode-extension].
* Installeer in [Visual Studio 2019][visual-studio]de Azure Development-workload.
* Installeer in Visual Studio 2017 de Web Development-workload en [Visual Studio Tools voor Kubernetes.][visual-studio-k8s-tools]
* Download en installeer de [Windows,][cli-win] [Mac][cli-mac]of [Linux][cli-linux] CLI.

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Azure Dev Spaces u helpt complexere toepassingen te ontwikkelen voor meerdere containers en hoe u de samenwerking vereenvoudigen door te werken met verschillende versies of branches van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Teamontwikkeling in Azure Dev Spaces][team-development-qs]

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
