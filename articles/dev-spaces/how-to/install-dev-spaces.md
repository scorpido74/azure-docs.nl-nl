---
title: Azure dev Spaces inschakelen op AKS & de hulpprogram ma's aan de client zijde installeren
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Meer informatie over het inschakelen van Azure dev Spaces in een AKS-cluster en het installeren van de hulpprogram ma's aan de client zijde.
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S
ms.openlocfilehash: 3666eeb995c73615c522200a3619d785814ea0b5
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873524"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Azure dev Spaces inschakelen op een AKS-cluster en de hulpprogram ma's aan de client zijde installeren

In dit artikel wordt beschreven hoe u op verschillende manieren Azure dev-ruimten kunt inschakelen op een AKS-cluster en hoe u de hulpprogram ma's aan de client zijde installeert.

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>Azure-ontwikkel ruimten in-of uitschakelen met de CLI

Voordat u ontwikkel ruimten kunt inschakelen met behulp van de CLI, hebt u het volgende nodig:
* Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account][az-portal-create-account]maken.
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

Met deze `use-dev-spaces` opdracht wordt ook de Azure dev Space cli geïnstalleerd.

Als u Azure dev Spaces wilt verwijderen uit uw AKS-cluster, gebruikt u de `azds remove` opdracht. Bijvoorbeeld:

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Met de bovenstaande opdracht worden Azure dev Spaces verwijderd uit het *MyAKS* -cluster in *MyResourceGroup*. Alle naam ruimten die u met Azure dev Spaces hebt gemaakt, blijven samen met hun werk belastingen, maar nieuwe werk belastingen in deze naam ruimten worden niet met Azure dev-ruimten geinstrumenteerd. Als u een bestaand van de bestaande peulen met Azure dev Spaces opnieuw start, worden er bovendien fouten weer geven. Deze bestanden moeten opnieuw worden geïmplementeerd zonder Azure dev Spaces-hulpprogram ma's. Als u Azure dev Spaces volledig uit uw cluster wilt verwijderen, verwijdert u alle peulen in alle naam ruimten waar Azure dev Spaces is ingeschakeld.

## <a name="enable-or-remove-azure-dev-spaces-using-the-azure-portal"></a>Azure-ontwikkel ruimten in-of uitschakelen met behulp van de Azure Portal

Voordat u ontwikkel ruimten kunt inschakelen met behulp van de Azure Portal, hebt u het volgende nodig:
* Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account][az-portal-create-account]maken.
* [Een AKS-cluster][create-aks-portal] in een [ondersteunde regio][supported-regions].

Azure-ontwikkel ruimten inschakelen met behulp van de Azure Portal:
1. Meld u aan bij de [Azure-portal][az-portal].
1. Navigeer naar uw AKS-cluster.
1. Selecteer de menu opdracht *dev Spaces* .
1. Wijzig *dev-ruimten* in *Ja* en klik op *Opslaan*.

![Ontwikkel ruimten in de Azure Portal inschakelen](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Wanneer u Azure dev Spaces inschakelt met behulp van de Azure Portal **, worden geen** hulpprogram Ma's voor Azure-ontwikkel ruimten geïnstalleerd.

Als u Azure-ontwikkel ruimten wilt verwijderen uit uw AKS-cluster, wijzigt u de *optie ontwikkel ruimten* in *Nee* en klikt u op *Opslaan*. Alle naam ruimten die u met Azure dev Spaces hebt gemaakt, blijven samen met hun werk belastingen, maar nieuwe werk belastingen in deze naam ruimten worden niet met Azure dev-ruimten geinstrumenteerd. Als u een bestaand van de bestaande peulen met Azure dev Spaces opnieuw start, worden er bovendien fouten weer geven. Deze bestanden moeten opnieuw worden geïmplementeerd zonder Azure dev Spaces-hulpprogram ma's. Als u Azure dev Spaces volledig uit uw cluster wilt verwijderen, verwijdert u alle peulen in alle naam ruimten waar Azure dev Spaces is ingeschakeld.

## <a name="install-the-client-side-tools"></a>De hulpprogram ma's aan de client zijde installeren

U kunt de Azure dev Spaces-client-side hulp middelen gebruiken om te communiceren met ontwikkel ruimten in een AKS-cluster vanaf uw lokale computer. Er zijn verschillende manieren om de hulpprogram ma's aan de client zijde te installeren:

* Installeer de [Azure dev Space-extensie][vscode-extension]in [Visual Studio code][vscode].
* Installeer de werk belasting Azure Development in [Visual Studio 2019][visual-studio].
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
