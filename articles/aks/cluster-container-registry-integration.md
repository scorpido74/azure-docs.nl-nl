---
title: Azure Container Registry integreren met de Azure Kubernetes-service
description: Meer informatie over het integreren van Azure Kubernetes service (AKS) met Azure Container Registry (ACR)
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 09/17/2018
ms.author: mlearned
ms.openlocfilehash: ba52cac4ebe923b7217550ed90948d908d8daf7f
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73900666"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Verifiëren met Azure Container Registry van de Azure Kubernetes-service

Wanneer u Azure Container Registry (ACR) met Azure Kubernetes service (AKS) gebruikt, moet er een verificatie mechanisme tot stand worden gebracht. In dit artikel vindt u voor beelden voor het configureren van verificatie tussen deze twee Azure-Services.

U kunt de AKS instellen op ACR-integratie in enkele eenvoudige opdrachten met de Azure CLI.

## <a name="before-you-begin"></a>Voordat u begint

Voor de volgende voor beelden is vereist:

* De rol van **eigenaar** of **Azure-account beheerder** voor het **Azure-abonnement**
* Azure CLI-versie 2.0.73 of hoger

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
U kunt ook de naam van de ACR met een ACR-Resource-ID opgeven. deze heeft de volgende indeling:

/Subscriptions/\<abonnement-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\> 
 
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
```
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

U kunt ook de integratie tussen een ACR en een AKS-cluster verwijderen met het volgende
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```
of
```
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

```
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
```
kubectl apply -f acr-nginx.yaml
```

U kunt de implementatie controleren door uit te voeren:
```
kubectl get pods
```
U moet een van de twee meest uitgevoerde peulen hebben.
```
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
