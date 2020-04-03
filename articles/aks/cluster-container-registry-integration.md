---
title: Azure-containerregister integreren met Azure Kubernetes-service
description: Meer informatie over het integreren van Azure Kubernetes Service (AKS) met Azure Container Registry (ACR)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 514cc25e1959145c65fe60cd3054cec4ed28f44d
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617426"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Verifiëren bij Azure Container Registry vanuit Azure Kubernetes Service

Wanneer u Azure Container Registry (ACR) gebruikt met Azure Kubernetes Service (AKS), moet een verificatiemechanisme worden ingesteld. In dit artikel vindt u voorbeelden voor het configureren van verificatie tussen deze twee Azure-services. 

U de AKS-integratie op ACR instellen in een paar eenvoudige opdrachten met de Azure CLI. Met deze integratie wordt de AcrPull-rol toeeigenen aan de serviceprincipal die is gekoppeld aan het AKS-cluster.

## <a name="before-you-begin"></a>Voordat u begint

Voor deze voorbeelden is het volgende nodig:

* **Rol eigenaar** of **Azure-accountbeheerder** op het **Azure-abonnement**
* Azure CLI-versie 2.0.73 of hoger

Om te voorkomen dat u een **rol voor eigenaar** of **Azure-accountbeheerder** nodig hebt, u een serviceprincipal handmatig configureren of een bestaande serviceprincipal gebruiken om ACR van AKS te verifiëren. Zie [ACR-verificatie met serviceprincipals](../container-registry/container-registry-auth-service-principal.md) of [Authenticeren van Kubernetes met een pull-geheim voor](../container-registry/container-registry-auth-kubernetes.md)meer informatie.

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Een nieuw AKS-cluster maken met ACR-integratie

U AKS- en ACR-integratie instellen tijdens de eerste creatie van uw AKS-cluster.  Als u een AKS-cluster wilt laten communiceren met ACR, wordt een Azure Active **Directory-serviceprincipal** gebruikt. Met de volgende CLI-opdracht u een bestaande ACR in uw abonnement autoriseren en de juiste **ACRPull-rol** configureren voor de serviceprincipal. Lever hieronder geldige waarden voor uw parameters.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
$MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

U ook de ACR-naam opgeven met behulp van een ACR-bron-id, die de volgende indeling heeft:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>` 

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Het kan enkele minuten duren voordat deze stap is voltooid.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>ACR-integratie configureren voor bestaande AKS-clusters

Integreer een bestaande ACR met bestaande AKS-clusters door geldige waarden voor **acr-name** of **acr-resource-id** zoals hieronder te leveren.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```

Of

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

U ook de integratie tussen een ACR en een AKS-cluster verwijderen met de volgende

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```

of

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Werken met ACR-& AKS

### <a name="import-an-image-into-your-acr"></a>Een afbeelding importeren in uw ACR

Importeer een afbeelding van dockerhub in uw ACR door het volgende uit te voeren:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>De voorbeeldafbeelding implementeren van ACR naar AKS

Zorg ervoor dat u over de juiste AKS-referenties beschikt

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Maak een bestand genaamd **acr-nginx.yaml** dat het volgende bevat:

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

Voer deze implementatie vervolgens uit in uw AKS-cluster:

```console
kubectl apply -f acr-nginx.yaml
```

U de implementatie controleren door het uitvoeren van:

```console
kubectl get pods
```

Je zou twee lopende peulen moeten hebben.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
