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
ms.openlocfilehash: ab744efd205d826cb7ae2c3eda7bba28f4a9bee0
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097810"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Verifiëren met Azure Container Registry van de Azure Kubernetes-service

Wanneer u Azure Container Registry (ACR) met Azure Kubernetes service (AKS) gebruikt, moet er een verificatie mechanisme tot stand worden gebracht. In dit artikel vindt u meer informatie over de aanbevolen configuraties voor verificatie tussen deze twee Azure-Services.

U kunt de AKS instellen op ACR-integratie in enkele eenvoudige opdrachten met de Azure CLI.

## <a name="before-you-begin"></a>Voordat u begint

U moet het volgende hebben:

* De rol van **eigenaar** of **Azure-account beheerder** voor het **Azure-abonnement**
* U hebt ook de Azure CLI-versie 2.0.73 of hoger nodig
* [Docker](https://docs.docker.com/install/) moet zijn geïnstalleerd op de client en u moet toegang hebben tot [docker hub](https://hub.docker.com/)

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Een nieuw AKS-cluster maken met ACR-integratie

U kunt AKS-en ACR-integratie instellen tijdens het maken van de eerste keer dat u uw AKS-cluster maakt.  Als u een AKS-cluster wilt toestaan om te communiceren met ACR, wordt een Azure Active Directory **Service-Principal** gebruikt. Met de volgende CLI-opdracht kunt u een bestaande ACR in uw abonnement autoriseren en de juiste **ACRPull** -rol configureren voor de Service-Principal. Geef hieronder geldige waarden voor de para meters op.  De para meters tussen vier Kante haken zijn optioneel.
```azurecli
az login
az acr create -n myContainerRegistry -g myContainerRegistryResourceGroup --sku basic [in case you do not have an existing ACR]
az aks create -n myAKSCluster -g myResourceGroup --attach-acr <acr-name-or-resource-id>
```
**Een ACR-Resource-ID heeft de volgende indeling:** 

/Subscriptions/<-abonnement: d >/resourceGroups/< Resource-Group-name >/providers/Microsoft.ContainerRegistry/registries/{name} 
  
Het kan enkele minuten duren voordat deze stap is voltooid.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>ACR-integratie configureren voor bestaande AKS-clusters

Integreer een bestaande ACR met bestaande AKS-clusters door geldige waarden op te geven voor de **ACR-naam** of **ACR-resource-id** zoals hieronder wordt beschreven.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

U kunt ook de integratie tussen een ACR en een AKS-cluster verwijderen met het volgende
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```


## <a name="log-in-to-your-acr"></a>Meld u aan bij uw ACR

Gebruik de volgende opdracht om u aan te melden bij uw ACR.  Vervang de <acrname> para meter door de naam van uw ACR.  De standaard instelling is bijvoorbeeld **aks < uw-resource groep > ACR**.

```azurecli
az acr login -n <acrName>
```

## <a name="pull-an-image-from-docker-hub-and-push-to-your-acr"></a>Een installatie kopie van docker hub halen en naar uw ACR pushen

Haal een installatie kopie op uit docker hub, label de installatie kopie en push uw ACR.

```console
acrloginservername=$(az acr show -n <acrname> -g <myResourceGroup> --query loginServer -o tsv)
docker pull nginx
```

```
$ docker tag nginx $acrloginservername/nginx:v1
$ docker push $acrloginservername/nginx:v1

The push refers to repository [someacr1.azurecr.io/nginx]
fe6a7a3b3f27: Pushed
d0673244f7d4: Pushed
d8a33133e477: Pushed
v1: digest: sha256:dc85890ba9763fe38b178b337d4ccc802874afe3c02e6c98c304f65b08af958f size: 948
```

## <a name="update-the-state-and-verify-pods"></a>De status bijwerken en een Peul controleren

Voer de volgende stappen uit om uw implementatie te controleren.

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Bekijk het yaml-bestand en bewerk de eigenschap image door de waarde te vervangen door uw ACR-aanmeldings server, afbeelding en label.

```
$ cat acr-nginx.yaml

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

$ kubectl apply -f acr-nginx.yaml
$ kubectl get pods

You should have two running pods.

NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
