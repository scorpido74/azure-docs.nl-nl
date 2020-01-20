---
title: KEUR Containers implementeren met helm in azure Kubernetes
description: Gebruik het helm-verpakkings programma voor het implementeren van containers in een Kubernetes-cluster in Azure Container Service
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a32c9fab3877a693d2df26571b9fae4aa7b4380c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271083"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>KEUR Helm gebruiken om containers in een Kubernetes-cluster te implementeren

> [!TIP]
> Zie [Installing Applications with helm in azure Kubernetes service (AKS) (Engelstalig)](../../aks/kubernetes-helm.md)voor de bijgewerkte versie van dit artikel waarin de Azure Kubernetes-service wordt gebruikt.

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Helm](https://github.com/kubernetes/helm/) is een open-source-verpakkings programma waarmee u de levens cyclus van Kubernetes-toepassingen kunt installeren en beheren. Net als Linux-pakket beheerders, zoals apt-get en yum, wordt helm gebruikt voor het beheren van Kubernetes-grafieken, die pakketten van vooraf geconfigureerde Kubernetes-resources zijn. Dit artikel laat u zien hoe u met helm kunt werken in een Kubernetes-cluster dat is geïmplementeerd in Azure Container Service.

Helm heeft twee onderdelen: 
* De **helm cli** is een client die lokaal of in de Cloud op uw computer draait  

* **Tiller** is een server die wordt uitgevoerd op het Kubernetes-cluster en de levens cyclus van uw Kubernetes-toepassingen beheert 
 
## <a name="prerequisites"></a>Vereisten

* [Een Kubernetes-cluster maken](container-service-kubernetes-walkthrough.md) in azure container service

* [`kubectl`installeren en configureren](../container-service-connect.md) op een lokale computer

* [Helm installeren](https://github.com/kubernetes/helm/blob/master/docs/install.md) op een lokale computer

## <a name="helm-basics"></a>Basis beginselen van helm 

Als u informatie wilt weer geven over het Kubernetes-cluster dat u Tiller installeert en uw toepassingen implementeert, typt u de volgende opdracht:

```bash
kubectl cluster-info 
```
![kubectl-cluster-info](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Nadat u helm hebt geïnstalleerd, installeert u Tiller op uw Kubernetes-cluster door de volgende opdracht te typen:

```bash
helm init --upgrade
```
Wanneer de bewerking is voltooid, ziet u uitvoer als de volgende:

![Tiller-installatie](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Als u alle beschik bare helm-grafieken in de opslag plaats wilt weer geven, typt u de volgende opdracht:

```bash 
helm search 
```

De uitvoer ziet er als volgt uit:

![Helm zoeken](./media/container-service-kubernetes-helm/helm-search.png)
 
Als u de grafieken wilt bijwerken om de nieuwste versies op te halen, typt u:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Een nginx ingress-controller diagram implementeren 
 
Als u een nginx ingress-controller grafiek wilt implementeren, typt u één opdracht:

```bash
helm install stable/nginx-ingress 
```
![Ingangs controller implementeren](./media/container-service-kubernetes-helm/nginx-ingress.png)

Als u `kubectl get svc` typt om alle services weer te geven die op het cluster worden uitgevoerd, ziet u dat er een IP-adres is toegewezen aan de ingangs controller. (Terwijl de toewijzing wordt uitgevoerd, ziet u `<pending>`. Het duurt enkele minuten om de bewerking uit te voeren.) 

Nadat het IP-adres is toegewezen, gaat u naar de waarde van het externe IP-adres om de nginx-back-end te zien. 
 
![IP-adres ingangs](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Als u een lijst met grafieken wilt zien die op uw cluster zijn geïnstalleerd, typt u:

```bash
helm list 
```

U kunt de opdracht afkorten tot `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Een MariaDB-diagram en-client implementeren

Implementeer nu een MariaDB-diagram en een MariaDB-client om verbinding te maken met de data base.

Typ de volgende opdracht om de MariaDB-grafiek te implementeren:

```bash
helm install --name v1 stable/mariadb
```

waarbij `--name` een tag is die wordt gebruikt voor releases.

> [!TIP]
> Als de implementatie mislukt, voert u `helm repo update` uit en probeert u het opnieuw.
>
 
 
Als u alle grafieken wilt weer geven die zijn geïmplementeerd in uw cluster, typt u:

```bash 
helm list
```
 
Als u alle implementaties wilt weer geven die op uw cluster worden uitgevoerd, typt u:

```bash
kubectl get deployments 
``` 
 
 
Ten slotte, om een pod uit te voeren voor toegang tot de client, typt u:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Als u verbinding wilt maken met de client, typt u de volgende opdracht, waarbij u `v1-mariadb` vervangt door de naam van uw implementatie:

```bash
sudo mysql –h v1-mariadb
```
 
 
U kunt nu standaard SQL-opdrachten gebruiken voor het maken van data bases, tabellen, enzovoort. `Create DATABASE testdb1;` maakt bijvoorbeeld een lege data base. 
 
 
 
## <a name="next-steps"></a>Volgende stappen

* Zie de [helm-documentatie](https://github.com/kubernetes/helm/blob/master/docs/index.md)voor meer informatie over het beheren van Kubernetes-grafieken. 

