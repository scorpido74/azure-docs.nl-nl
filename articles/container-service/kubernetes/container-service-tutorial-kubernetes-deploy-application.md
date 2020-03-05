---
title: '(AFGESCHAFT) Zelfstudie Azure Container Service: Toepassing implementeren'
description: 'Zelfstudie Azure Container Service: Toepassing implementeren'
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 2c2d50da4328ff07c3d2fda4a8721839aa3aa6e7
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274057"
---
# <a name="deprecated-run-applications-in-kubernetes"></a>(AFGESCHAFT) Toepassingen uitvoeren in Kubernetes

> [!TIP]
> Voor de bijgewerkte versie van deze zelf studie die gebruikmaakt van de Azure Kubernetes-service, raadpleegt u [zelf studie: toepassingen uitvoeren in azure Kubernetes service (AKS)](../../aks/tutorial-kubernetes-deploy-application.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

In deze zelfstudie, deel vier van zeven, wordt een voorbeeldtoepassing geïmplementeerd in een Kubernetes-cluster. Dit zijn de uitgevoerde stappen:

> [!div class="checklist"]
> * Kubernetes-manifestbestanden bijwerken
> * Toepassing uitvoeren in Kubernetes
> * De toepassing testen

In de volgende zelfstudies wordt deze toepassing opgeschaald en bijgewerkt, en wordt Log Analytics geconfigureerd om het Kubernetes-cluster te controleren.

In deze zelfstudie wordt ervan uitgegaan dat u over basiskennis van Kubernetes-concepten beschikt. Raadpleeg de [Kubernetes-documentatie](https://kubernetes.io/docs/home/) voor gedetailleerde informatie over Kubernetes.

## <a name="before-you-begin"></a>Voordat u begint

In de vorige zelfstudies is een toepassing verpakt in een containerinstallatiekopie, is deze installatiekopie geüpload naar Azure Container Registry en is een Kubernetes-cluster gemaakt. 

Om deze zelfstudie te voltooien hebt u het vooraf gemaakte Kubernetes-manifestbestand `azure-vote-all-in-one-redis.yml` nodig. Dit bestand is met de broncode van de toepassing gedownload in een vorige zelfstudie. Controleer of u de opslagplaats hebt gekloond en of u naar de gekloonde opslagplaats bent gegaan.

Als u deze stappen niet hebt uitgevoerd en deze zelfstudie wilt volgen, gaat u terug naar [Zelfstudie 1: Containerinstallatiekopieën maken](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-manifest-file"></a>Manifestbestand bijwerken

In deze zelfstudie wordt Azure Container Registry (ACR) gebruikt om een containerinstallatiekopie op te slaan. Voordat u de toepassing uitvoert, moet de naam van de ACR-aanmeldingsserver in het Kubernetes-manifestbestand worden bijgewerkt.

Haal de naam van de ACR-aanmeldingsserver op met de opdracht [az acr list](/cli/azure/acr#az-acr-list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Het manifestbestand is vooraf gemaakt met `microsoft` als naam van de aanmeldingsserver. Open het bestand met een teksteditor. In dit voorbeeld wordt het bestand geopend met `vi`.

```bash
vi azure-vote-all-in-one-redis.yml
```

Vervang `microsoft` door de naam van de ACR-aanmeldingsserver. U vindt deze waarde op regel **47** van het manifestbestand.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Sla het bestand op en sluit het.

## <a name="deploy-application"></a>Toepassing implementeren

Gebruik de opdracht [kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create) om de toepassing uit te voeren. Deze opdracht parseert het manifestbestand en maakt de gedefinieerde Kubernetes-objecten.

```console
kubectl create -f azure-vote-all-in-one-redis.yml
```

Uitvoer:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Toepassing testen

Er wordt een [Kubernetes-service](https://kubernetes.io/docs/concepts/services-networking/service/) gemaakt die de toepassing beschikbaar maakt op internet. Dit proces kan enkele minuten duren. 

Gebruik de opdracht [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) met het argument `--watch` om de voortgang te controleren.

```console
kubectl get service azure-vote-front --watch
```

In eerste instantie wordt het **EXTERNE IP-adres** voor de service `azure-vote-front` weergegeven als `pending`. Zodra het EXTERNE IP-adres is gewijzigd van `pending` in een `IP address`, gebruikt u `CTRL-C` om het controleproces van kubectl te stoppen.

```output
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Als u de toepassing wilt zien, bladert u naar het externe IP-adres.

![Afbeelding van Kubernetes-cluster in Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie is een Azure Vote-toepassing geïmplementeerd in een Kubernetes-cluster in Azure Container Service. Dit zijn de uitgevoerde taken:  

> [!div class="checklist"]
> * Kubernetes-manifestbestanden downloaden
> * De toepassing uitvoeren in Kubernetes
> * De toepassing testen

Ga naar de volgende zelfstudie om te leren hoe u zowel een Kubernetes-toepassing als de onderliggende Kubernetes-infrastructuur schaalt. 

> [!div class="nextstepaction"]
> [Kubernetes-toepassing en -infrastructuur schalen](./container-service-tutorial-kubernetes-scale.md)
