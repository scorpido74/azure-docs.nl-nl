---
title: 'Zelfstudie voor Kubernetes in Azure: Een cluster implementeren'
description: In deze zelfstudie Azure Kubernetes Service (AKS) gaat u een AKS-cluster maken en kubectl gebruiken om verbinding te maken met het hoofdknooppunt van Kubernetes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: e0dea5fcc7860e8653d542653e279010f8ede200
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898852"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Zelfstudie: Een AKS-cluster (Azure Kubernetes Service) implementeren

Kubernetes biedt een gedistribueerd platform voor toepassingen in containers. Met AKS kunt u snel een productieklaar Kubernetes-cluster maken. In deze zelfstudie, deel drie van de zeven, wordt een Kubernetes-cluster geïmplementeerd in AKS. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een service-principal voor de resource-interacties maken
> * Een Kubernetes AKS-cluster implementeren
> * De Kubernetes-CLI (kubectl) installeren
> * Kubectl configureren om verbinding te maken met uw AKS-cluster

In volgende zelfstudies wordt de Azure Vote-toepassing geïmplementeerd in het cluster en vervolgens geschaald en bijgewerkt.

## <a name="before-you-begin"></a>Voordat u begint

In de vorige zelfstudies is een containerinstallatiekopie gemaakt en geüpload naar een Azure Container Registry-exemplaar. Als u deze stappen niet hebt uitgevoerd en u wilt door gaan met de [zelf studie 1: container installatie kopieën maken][aks-tutorial-prepare-app].

Voor deze zelfstudie moet u Azure CLI versie 2.0.53 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-service-principal"></a>Een service-principal maken

Een AKS-cluster heeft een service-principal van Azure Active Directory nodig om met andere Azure-resources te kunnen communiceren. Deze service-principal kan automatisch worden gemaakt door de Azure CLI of via de portal, of u kunt er een vooraf maken en hieraan extra machtigingen toewijzen. In deze zelfstudie gaat u een service-principal maken, toegang verlenen tot de ACR-instantie (Azure Container Registry) die u in de vorige zelfstudie hebt gemaakt, en vervolgens een AKS-cluster maken.

Maak een service-principal met behulp van de opdracht [az ad sp create-for-rbac][]. De parameter `--skip-assignment` zorgt ervoor dat eventuele extra machtigingen beperkt kunnen worden toegewezen. Standaard is de service-principal geldig voor één jaar.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

De uitvoer lijkt op die in het volgende voorbeeld:

```
{
  "appId": "e7596ae3-6864-4cb8-94fc-20164b1588a9",
  "displayName": "azure-cli-2018-06-29-19-14-37",
  "name": "http://azure-cli-2018-06-29-19-14-37",
  "password": "52c95f25-bd1e-4314-bd31-d8112b293521",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Noteer de *appId* en *wachtwoord*. Deze waarden worden gebruikt in de volgende stappen.

## <a name="configure-acr-authentication"></a>ACR-verificatie configureren

Voor toegang tot installatiekopieën die zijn opgeslagen in ACR, moet u aan de AKS-service-principal de juiste rechten verlenen om installatiekopieën uit ACR op te kunnen halen.

Haal eerst de resource-ID van de ACR op met behulp van [az acr show][]. Werk de naam van het `<acrName>`-register bij met die van uw ACR-instantie en van de resourcegroep waar de ACR-instantie zich bevindt.

```azurecli
az acr show --resource-group myResourceGroup --name <acrName> --query "id" --output tsv
```

Als u het AKS-cluster de juiste toegangsrechten wilt verlenen om installatiekopieën te pullen die zijn opgeslagen in ACR, wijst u de rol `AcrPull` toe met behulp van de opdracht [az role assignment create][]. Vervang `<appId`> en `<acrId>` door de waarden die zijn verzameld in de vorige twee stappen.

```azurecli
az role assignment create --assignee <appId> --scope <acrId> --role acrpull
```

## <a name="create-a-kubernetes-cluster"></a>Een Kubernetes-cluster maken

AKS-clusters kunnen gebruikmaken van op rollen gebaseerd toegangsbeheer (RBAC) van Kubernetes. Met deze vorm van toegangsbeheer kunt u de toegang tot resources definiëren op basis van rollen die zijn toegewezen aan gebruikers. Machtigingen worden gecombineerd als aan een gebruiker meerdere rollen zijn toegewezen, en machtigingen kunnen gelden voor één enkele naamruimte of voor een heel cluster. Standaard schakelt de Azure CLI automatisch RBAC in wanneer u een AKS-cluster maakt.

Maak een AKS-cluster met behulp van [az aks create][]. In het volgende voorbeeld wordt een cluster met de naam *myAKSCluste* gemaakt in de resourcegroep met de naam *myResourceGroup*. Deze resource groep is gemaakt in de [vorige zelf studie][aks-tutorial-prepare-acr]. Voer uw eigen `<appId>` en `<password>` in die in de vorige stap zijn gemaakt toen de service-principal werd gemaakt.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --service-principal <appId> \
    --client-secret <password> \
    --generate-ssh-keys
```

Na enkele minuten is de implementatie voltooid en retourneert JSON opgemaakte informatie over de AKS-implementatie.

## <a name="install-the-kubernetes-cli"></a>De Kubernetes-CLI installeren

Als u verbinding wilt maken met het Kubernetes-cluster vanaf uw lokale computer, gebruikt u [kubectl][kubectl], de Kubernetes-opdracht regel-client.

Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u het lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Verbinding maken met cluster met behulp van kubectl

Gebruik de opdracht [az aks get-credentials][] om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. In het volgende voorbeeld worden de referenties voor het AKS-cluster met de naam *myAKSCluster* in *myResourceGroup* opgehaald:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, voert u de opdracht [kubectl Get nodes][kubectl-get] uit:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.13.10
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie is een Kubernetes-cluster geïmplementeerd in AKS, en hebt u `kubectl` geconfigureerd om er verbinding mee te maken. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een service-principal voor de resource-interacties maken
> * Een Kubernetes AKS-cluster implementeren
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
