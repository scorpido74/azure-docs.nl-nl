---
title: 'Zelfstudie voor Kubernetes in Azure: Een cluster implementeren'
description: In deze zelfstudie Azure Kubernetes Service (AKS) gaat u een AKS-cluster maken en kubectl gebruiken om verbinding te maken met het hoofdknooppunt van Kubernetes.
services: container-service
ms.topic: tutorial
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: bc31a4197b08cbeb1a99820d7ff490f20147c7bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78191262"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Zelfstudie: Een AKS-cluster (Azure Kubernetes Service) implementeren

Kubernetes biedt een gedistribueerd platform voor toepassingen in containers. Met AKS kunt u snel een productieklaar Kubernetes-cluster maken. In deze zelfstudie, deel drie van de zeven, wordt een Kubernetes-cluster geïmplementeerd in AKS. Procedures voor:

> [!div class="checklist"]
> * Een Kubernetes AKS-cluster implementeren dat kan worden geverifieerd in een Azure-containerregister
> * De Kubernetes-CLI (kubectl) installeren
> * Kubectl configureren om verbinding te maken met uw AKS-cluster

In volgende zelfstudies wordt de Azure Vote-toepassing geïmplementeerd in het cluster en vervolgens geschaald en bijgewerkt.

## <a name="before-you-begin"></a>Voordat u begint

In de vorige zelfstudies is een containerinstallatiekopie gemaakt en geüpload naar een Azure Container Registry-exemplaar. Als u deze stappen niet hebt uitgevoerd en u deze zelfstudie wilt volgen, begint u bij [Zelfstudie 1: containerinstallatiekopieën maken][aks-tutorial-prepare-app].

Deze zelfstudie vereist dat u de Azure CLI-versie 2.0.75 of hoger uitvoert. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Een Kubernetes-cluster maken

AKS-clusters kunnen gebruikmaken van op rollen gebaseerd toegangsbeheer (RBAC) van Kubernetes. Met deze vorm van toegangsbeheer kunt u de toegang tot resources definiëren op basis van rollen die zijn toegewezen aan gebruikers. Machtigingen worden gecombineerd als aan een gebruiker meerdere rollen zijn toegewezen, en machtigingen kunnen gelden voor één enkele naamruimte of voor een heel cluster. Standaard schakelt de Azure CLI automatisch RBAC in wanneer u een AKS-cluster maakt.

Maak een AKS-cluster met behulp van [az aks create][]. In het volgende voorbeeld wordt een cluster met de naam *myAKSCluste* gemaakt in de resourcegroep met de naam *myResourceGroup*. Deze resourcegroep is gemaakt in de [vorige zelfstudie][aks-tutorial-prepare-acr]. Als u wilt dat een AKS-cluster kan communiceren met andere Azure-bronnen, wordt automatisch een Azure Active Directory-serviceprincipal gemaakt, omdat u er geen hebt opgegeven. Deze serviceprincipal [krijgt hier het recht om afbeeldingen op te halen][container-registry-integration] uit het ACR-exemplaar (Azure Container Registry) dat u in de vorige zelfstudie hebt gemaakt.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

U ook handmatig een serviceprincipal configureren om afbeeldingen van ACR te halen. Zie [ACR-verificatie met serviceprincipals](../container-registry/container-registry-auth-service-principal.md) of [Authenticeren van Kubernetes met een pull-geheim voor](../container-registry/container-registry-auth-kubernetes.md)meer informatie.

Na enkele minuten is de implementatie voltooid en retourneert JSON opgemaakte informatie over de AKS-implementatie.

> [!NOTE]
> Om ervoor te zorgen dat uw cluster betrouwbaar werkt, moet u ten minste twee (twee) knooppunten uitvoeren.

## <a name="install-the-kubernetes-cli"></a>De Kubernetes-CLI installeren

Gebruik [kubectl][kubectl], de Kubernetes-opdrachtregelclient, als u vanaf uw lokale computer verbinding wilt maken met het Kubernetes-cluster.

Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u het lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Verbinding maken met cluster met behulp van kubectl

Gebruik de opdracht [az aks get-credentials][] om `kubectl` te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. In het volgende voorbeeld worden de referenties voor het AKS-cluster met de naam *myAKSCluster* in *myResourceGroup* opgehaald:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt verifiëren, voert u de opdracht [kubectl get nodes][kubectl-get] uit om een lijst met clusterknooppunten terug te sturen:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.14.8
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie is een Kubernetes-cluster geïmplementeerd in AKS, en hebt u `kubectl` geconfigureerd om er verbinding mee te maken. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een Kubernetes AKS-cluster implementeren dat kan worden geverifieerd in een Azure-containerregister
> * De Kubernetes-CLI (kubectl) installeren
> * Kubectl configureren om verbinding te maken met uw AKS-cluster

Ga door naar de volgende zelfstudie voor informatie over het implementeren van toepassingen naar het cluster.

> [!div class="nextstepaction"]
> [Toepassing implementeren in Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
