---
title: Een Azure-PostgreSQL grootschalige-Server groep verwijderen
description: Een Azure-post gres grootschalige-Server groep verwijderen
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: dcabe4b1520c66b8d5bfa398dc1248972587cd32
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936806"
---
# <a name="delete-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Een Azure-PostgreSQL grootschalige-Server groep verwijderen

In dit document worden de stappen beschreven voor het verwijderen van een server groep uit de Azure Arc-installatie.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-the-server-group"></a>De Server groep verwijderen

U kunt bijvoorbeeld het _postgres01_ -exemplaar verwijderen uit de onderstaande installatie:

```console
azdata arc postgres server list
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

De algemene indeling van de opdracht verwijderen is:
```console
azdata arc postgres server delete -n <server group name>
```
Voor meer informatie over de opdracht verwijderen voert u het volgende uit:
```console
azdata arc postgres server delete --help
```

### <a name="lets-delete-the-server-group-used-in-this-example"></a>Laten we de Server groep verwijderen die in dit voor beeld wordt gebruikt:
```console
azdata arc postgres server delete -n postgres01
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>De Kubernetes permanente volume claims (Pvc's) opnieuw claimen

Als u een server groep verwijdert, worden de bijbehorende [pvc's](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)niet verwijderd. Dit is standaard. De bedoeling is de gebruiker te helpen bij het openen van de database bestanden voor het geval het verwijderen van de instantie onbedoeld is. Het verwijderen van Pvc's is niet verplicht. Het wordt echter wel aanbevolen. Als u deze Pvc's niet vrijmaakt, wordt er uiteindelijk een fout opgetreden bij het uitvoeren van een Kubernetes-cluster dat er bijna geen schijf ruimte meer is. Voer de volgende stappen uit om de Pvc's vrij te maken:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. de Pvc's weer geven voor de Server groep die u hebt verwijderd
Als u de Pvc's wilt weer geven, voert u deze opdracht uit:
```console
kubectl get pvc [-n <namespace name>]
```

Hiermee wordt de lijst met PVSs geretourneerd, met name de Pvc's voor de Server groep die u hebt verwijderd. Bijvoorbeeld:
```console
kubectl get pvc
NAME                STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-postgres01-0   Bound    pvc-72ccc225-dad0-4dee-8eae-ed352be847aa   5Gi        RWO            default        2d18h
data-postgres01-1   Bound    pvc-ce6f0c51-faed-45ae-9472-8cdf390deb0d   5Gi        RWO            default        2d18h
data-postgres01-2   Bound    pvc-5a863ab9-522a-45f3-889b-8084c48c32f8   5Gi        RWO            default        2d18h
data-postgres01-3   Bound    pvc-00e1ace3-1452-434f-8445-767ec39c23f2   5Gi        RWO            default        2d15h
logs-postgres01-0   Bound    pvc-8b810f4c-d72a-474a-a5d7-64ec26fa32de   5Gi        RWO            default        2d18h
logs-postgres01-1   Bound    pvc-51d1e91b-08a9-4b6b-858d-38e8e06e60f9   5Gi        RWO            default        2d18h
logs-postgres01-2   Bound    pvc-8e5ad55e-300d-4353-92d8-2e383b3fe96e   5Gi        RWO            default        2d18h
logs-postgres01-3   Bound    pvc-f9e4cb98-c943-45b0-aa07-dd5cff7ea585   5Gi        RWO            default        2d15h
```
Er zijn 8 Pvc's voor deze server groep.

### <a name="2-delete-each-of-the-pvcs"></a>2. alle Pvc's verwijderen
Verwijder de gegevens en logboek-Pvc's voor elk van de PostgreSQL grootschalige-knoop punten (coördinator en werk nemers) van de Server groep die u hebt verwijderd.
De algemene indeling van deze opdracht is: 
```console
kubectl delete pvc <name of pvc>  [-n <namespace name>]
```

Bijvoorbeeld:
```console
kubectl delete pvc data-postgres01-0
kubectl delete pvc data-postgres01-1 
kubectl delete pvc data-postgres01-2
kubectl delete pvc data-postgres01-3
kubectl delete pvc logs-postgres01-0
kubectl delete pvc logs-postgres01-1
kubectl delete pvc logs-postgres01-2
kubectl delete pvc logs-postgres01-3
```

Met elk van deze kubectl-opdrachten wordt bevestigd dat het verwijderen van het PVC is geslaagd. Bijvoorbeeld:
```console
persistentvolumeclaim "data-postgres01-0" deleted
```
  

>**Opmerking** Zoals aangegeven kan het verwijderen van de Pvc's ertoe leiden dat uw Kubernetes-cluster uiteindelijk wordt opgehaald in een situatie waarin fouten optreden. Bij sommige van deze fouten kan het voor komen dat u zich niet kunt aanmelden bij uw Kubernetes-cluster met azdata, aangezien het peul kan worden verwijderd vanwege dit opslag probleem (normaal Kubernetes gedrag).
>
> Er kunnen bijvoorbeeld berichten in de logboeken worden weer gegeven die vergelijkbaar zijn met:  
    > Annotaties: microsoft.com/ignore-pod-health: True  
    > Status: mislukt  
    > Reden: verwijderd  
    > Bericht: het knoop punt heeft weinig resources: kortstondige opslag. Container controller gebruikt 16372Ki, die de aanvraag van 0 overschrijdt.
    
## <a name="next-step"></a>Volgende stap
[Azure Arc enabled postgresql grootschalige](create-postgresql-hyperscale-server-group.md) maken
