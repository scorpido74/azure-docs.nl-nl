---
title: Azure Container Registry integreren met de Azure Kubernetes-service
description: Meer informatie over het integreren van Azure Kubernetes service (AKS) met Azure Container Registry (ACR)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 70c36f9a18a85b90bb3a66d4083a71a00f61f14e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84016369"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Verifiëren bij Azure Container Registry vanuit Azure Kubernetes Service

Wanneer u Azure Container Registry (ACR) met Azure Kubernetes service (AKS) gebruikt, moet er een verificatie mechanisme tot stand worden gebracht. In dit artikel vindt u voor beelden voor het configureren van verificatie tussen deze twee Azure-Services. 

U kunt de AKS instellen op ACR-integratie in enkele eenvoudige opdrachten met de Azure CLI. Met deze integratie wordt de AcrPull-rol toegewezen aan de service-principal die is gekoppeld aan het AKS-cluster.

## <a name="before-you-begin"></a>Voordat u begint

Voor de volgende voor beelden is vereist:

* De rol van **eigenaar** of **Azure-account beheerder** voor het **Azure-abonnement**
* Azure CLI-versie 2.0.73 of hoger

Om te voor komen dat een **eigenaar** of een rol beheerder van een **Azure-account** nodig is, kunt u een Service-Principal hand matig configureren of een bestaande Service-Principal gebruiken om ACR van AKS te verifiëren. Zie [ACR-verificatie met Service-principals](../container-registry/container-registry-auth-service-principal.md) of verificatie [van Kubernetes met een pull-geheim](../container-registry/container-registry-auth-kubernetes.md)voor meer informatie.

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Een nieuw AKS-cluster maken met ACR-integratie

U kunt AKS-en ACR-integratie instellen tijdens het maken van de eerste keer dat u uw AKS-cluster maakt.  Als u een AKS-cluster wilt toestaan om te communiceren met ACR, wordt een Azure Active Directory **Service-Principal** gebruikt. Met de volgende CLI-opdracht kunt u een bestaande ACR in uw abonnement autoriseren en de juiste **ACRPull** -rol configureren voor de Service-Principal. Geef hieronder geldige waarden voor de para meters op.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

U kunt ook de naam van de ACR met een ACR-Resource-ID opgeven, die de volgende indeling heeft:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>` 

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Het kan enkele minuten duren voordat deze stap is voltooid.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>ACR-integratie configureren voor bestaande AKS-clusters

Integreer een bestaande ACR met bestaande AKS-clusters door geldige waarden op te geven voor de **ACR-naam** of **ACR-resource-id** zoals hieronder wordt beschreven.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```

of

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

U kunt ook de integratie tussen een ACR en een AKS-cluster verwijderen met het volgende

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```

of

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Werken met ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Een installatie kopie importeren in uw ACR

Importeer een installatie kopie van docker hub in uw ACR door het volgende uit te voeren:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Implementeer de voorbeeld installatie kopie van ACR naar AKS

Zorg ervoor dat u over de juiste AKS-referenties beschikt

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Maak een bestand met de naam **ACR-nginx. yaml** dat het volgende bevat:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80
```

Voer vervolgens deze implementatie uit in uw AKS-cluster:

```console
kubectl apply -f acr-nginx.yaml
```

U kunt de implementatie controleren door uit te voeren:

```console
kubectl get pods
```

U moet een van de twee meest uitgevoerde peulen hebben.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
