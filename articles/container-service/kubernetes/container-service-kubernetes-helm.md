---
title: (AFGESCHAFT) Containers met Helm implementeren in Azure Kubernetes
description: Het helm-verpakkingshulpprogramma gebruiken om containers te implementeren op een Kubernetes-cluster in Azure Container Service
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a32c9fab3877a693d2df26571b9fae4aa7b4380c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271083"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>(AFGESCHAFT) Helm gebruiken om containers te implementeren op een Kubernetes-cluster

> [!TIP]
> Zie [Toepassingen installeren met Helm in Azure Kubernetes Service (AKS)](../../aks/kubernetes-helm.md)voor de bijgewerkte versie van dit artikel die Azure Kubernetes Service gebruikt.

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Helm](https://github.com/kubernetes/helm/) is een open-source verpakkingstool die u helpt bij het installeren en beheren van de levenscyclus van Kubernetes-toepassingen. Net als bij Linux-pakketmanagers zoals Apt-get en Yum, wordt Helm gebruikt om Kubernetes-grafieken te beheren, die pakketten zijn met vooraf geconfigureerde Kubernetes-bronnen. In dit artikel ziet u hoe u met Helm werken aan een Kubernetes-cluster dat is geïmplementeerd in Azure Container Service.

Helm heeft twee componenten: 
* De **Helm CLI** is een client die lokaal of in de cloud op uw machine draait  

* **Tiller** is een server die draait op het Kubernetes-cluster en de levenscyclus van uw Kubernetes-toepassingen beheert 
 
## <a name="prerequisites"></a>Vereisten

* [Een Kubernetes-cluster maken](container-service-kubernetes-walkthrough.md) in Azure Container Service

* [Installeren en `kubectl` configureren](../container-service-connect.md) op een lokale computer

* [Helm installeren](https://github.com/kubernetes/helm/blob/master/docs/install.md) op een lokale computer

## <a name="helm-basics"></a>Helm basics 

Als u informatie wilt weergeven over het Kubernetes-cluster waarop u Tiller installeert en uw toepassingen implementeert, typt u de volgende opdracht:

```bash
kubectl cluster-info 
```
![kubectl cluster-info](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Nadat u Helm hebt geïnstalleerd, installeert u Tiller op uw Kubernetes-cluster door de volgende opdracht te typen:

```bash
helm init --upgrade
```
Wanneer de uitvoer is voltooid, ziet u de uitvoer als volgt:

![Helmstokinstallatie](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Als u alle helmdiagrammen wilt weergeven die beschikbaar zijn in de opslagplaats, typt u de volgende opdracht:

```bash 
helm search 
```

U ziet uitvoer als volgt:

![Helm zoeken](./media/container-service-kubernetes-helm/helm-search.png)
 
Als u de grafieken wilt bijwerken om de nieuwste versies te krijgen, typt u het:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Een Nginx-ingress-controllerdiagram implementeren 
 
Als u een Nginx-ingress-controllerdiagram wilt implementeren, typt u één opdracht:

```bash
helm install stable/nginx-ingress 
```
![Invallencontroller implementeren](./media/container-service-kubernetes-helm/nginx-ingress.png)

Als u `kubectl get svc` typt om alle services weer te geven die op het cluster worden uitgevoerd, ziet u dat een IP-adres is toegewezen aan de invallende controller. (Terwijl de toewijzing aan de `<pending>`gang is, ziet u . Het duurt een paar minuten om te voltooien.) 

Nadat het IP-adres is toegewezen, navigeert u naar de waarde van het externe IP-adres om de nginx-backend te zien worden uitgevoerd. 
 
![Ip-adres binnenbinnendringen](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Als u een lijst met grafieken wilt bekijken die op uw cluster zijn geïnstalleerd, typt u het gaat om:

```bash
helm list 
```

U de opdracht verkorten om `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Een MariaDB-grafiek en -client implementeren

Implementeer nu een MariaDB-grafiek en een MariaDB-client om verbinding te maken met de database.

Als u de MariaDB-grafiek wilt implementeren, typt u de volgende opdracht:

```bash
helm install --name v1 stable/mariadb
```

waar `--name` is een tag die wordt gebruikt voor releases.

> [!TIP]
> Als de implementatie `helm repo update` mislukt, voert u het uit en probeert u het opnieuw.
>
 
 
Als u alle grafieken wilt weergeven die op uw cluster zijn geïmplementeerd, typt u het beste:

```bash 
helm list
```
 
Als u alle implementaties op uw cluster wilt weergeven, typt u het:

```bash
kubectl get deployments 
``` 
 
 
Als u ten slotte een pod wilt uitvoeren om toegang te krijgen tot de client, typt u het:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Als u verbinding wilt maken met de `v1-mariadb` client, typt u de volgende opdracht, vervangen door de naam van uw implementatie:

```bash
sudo mysql –h v1-mariadb
```
 
 
U nu standaard SQL-opdrachten gebruiken om databases, tabellen, enz. `Create DATABASE testdb1;` Hiermee maakt u bijvoorbeeld een lege database. 
 
 
 
## <a name="next-steps"></a>Volgende stappen

* Zie de [helmdocumentatie](https://github.com/kubernetes/helm/blob/master/docs/index.md)voor meer informatie over het beheren van Kubernetes-grafieken. 

