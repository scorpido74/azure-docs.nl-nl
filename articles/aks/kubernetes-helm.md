---
title: Bestaande toepassingen met helm in AKS installeren
description: Meer informatie over het gebruik van het helm-verpakkings programma voor het implementeren van containers in een Azure Kubernetes service-cluster (AKS)
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/24/2020
ms.author: zarhoads
ms.openlocfilehash: d05d0166724e586fa79e58e2e74fb583b45d0cc6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88852873"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Bestaande toepassingen met helm in azure Kubernetes service (AKS) installeren

[Helm][helm] is een open-source-verpakkings programma waarmee u de levens cyclus van Kubernetes-toepassingen kunt installeren en beheren. Net als Linux-pakket beheerders, zoals *apt* en *yum*, wordt helm gebruikt voor het beheren van Kubernetes-grafieken, die pakketten van vooraf geconfigureerde Kubernetes-resources zijn.

In dit artikel wordt beschreven hoe u helm configureert en gebruikt in een Kubernetes-cluster op AKS.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

U hebt ook de helm CLI geïnstalleerd. Dit is de client die wordt uitgevoerd op uw ontwikkel systeem. Hiermee kunt u toepassingen starten, stoppen en beheren met helm. Als u de Azure Cloud Shell gebruikt, is de CLI van helm al geïnstalleerd. Zie [Installing helm][helm-install](Engelstalig) voor installatie-instructies op uw lokale platform.

> [!IMPORTANT]
> Helm is bedoeld om te worden uitgevoerd op Linux-knoop punten. Als u Windows Server-knoop punten in uw cluster hebt, moet u ervoor zorgen dat helm peul alleen wordt gepland voor uitvoering op Linux-knoop punten. U moet er ook voor zorgen dat alle helm-grafieken die u installeert, ook zijn gepland voor uitvoering op de juiste knoop punten. Met de opdrachten in dit artikel wordt gebruikgemaakt van [node-Selects] [K8S-node-selector] om ervoor te zorgen dat de juiste knoop punten worden gepland, maar niet alle helm-grafieken kunnen een knooppunt kiezer weer geven. U kunt ook overwegen andere opties in uw cluster te gebruiken, zoals [taints][taints].

## <a name="verify-your-version-of-helm"></a>Uw versie van helm controleren

Gebruik de `helm version` opdracht om te controleren of helm 3 is geïnstalleerd:

```console
helm version
```

In het volgende voor beeld ziet u dat helm-versie 3.0.0 is geïnstalleerd:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>Een toepassing installeren met helm v3

### <a name="add-helm-repositories"></a>Helm-opslag plaatsen toevoegen

Gebruik de opdracht [helm opslag plaats][helm-repo-add] om de officiële helm stabiele grafieken en inkomende *nginx-* opslag plaatsen toe te voegen.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>Helm-grafieken zoeken

Helm-grafieken worden gebruikt voor het implementeren van toepassingen in een Kubernetes-cluster. Gebruik de [helm-Zoek][helm-search] opdracht om te zoeken naar vooraf gemaakte helm-grafieken:

```console
helm search repo stable
```

In de volgende gecomprimeerde voorbeeld uitvoer ziet u enkele van de helm-grafieken die beschikbaar zijn voor gebruik:

```console
$ helm search repo stable

NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION                                       
stable/acs-engine-autoscaler            2.2.2           2.1.1                           DEPRECATED Scales worker nodes within agent pools 
stable/aerospike                        0.3.1           v4.5.0.5                        A Helm chart for Aerospike in Kubernetes          
stable/airflow                          4.10.0          1.10.4                          Airflow is a platform to programmatically autho...
stable/ambassador                       4.4.7           0.85.0                          A Helm chart for Datawire Ambassador              
stable/anchore-engine                   1.3.7           0.5.2                           Anchore container analysis and policy evaluatio...
stable/apm-server                       2.1.5           7.0.0                           The server receives data from the Elastic APM a...
stable/ark                              4.2.2           0.10.2                          DEPRECATED A Helm chart for ark                   
stable/artifactory                      7.3.1           6.1.0                           DEPRECATED Universal Repository Manager support...
stable/artifactory-ha                   0.4.1           6.2.0                           DEPRECATED Universal Repository Manager support...
stable/atlantis                         3.8.4           v0.8.2                          A Helm chart for Atlantis https://www.runatlant...
stable/auditbeat                        1.1.0           6.7.0                           A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler           0.3.3                                           Scales worker nodes within autoscaling groups.    
stable/aws-iam-authenticator            0.1.1           1.0                             A Helm chart for aws-iam-authenticator            
stable/bitcoind                         0.2.2           0.17.1                          Bitcoin is an innovative payment network and a ...
stable/bookstack                        1.1.2           0.27.4-1                        BookStack is a simple, self-hosted, easy-to-use...
stable/buildkite                        0.2.4           3                               DEPRECATED Agent for Buildkite                    
stable/burrow                           1.5.2           0.29.0                          Burrow is a permissionable smart contract machine 
stable/centrifugo                       3.1.0           2.1.0                           Centrifugo is a real-time messaging server.       
stable/cerebro                          1.3.1           0.8.5                           A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager                     v0.6.7          v0.6.2                          A Helm chart for cert-manager                     
stable/chaoskube                        3.1.2           0.14.0                          Chaoskube periodically kills random pods in you...
stable/chartmuseum                      2.4.0           0.8.2                           Host your own Helm Chart Repository               
stable/chronograf                       1.1.0           1.7.12                          Open-source web application written in Go and R...
stable/clamav                           1.0.4           1.6                             An Open-Source antivirus engine for detecting t...
stable/cloudserver                      1.0.3           8.1.5                           An open-source Node.js implementation of the Am...
stable/cluster-autoscaler               6.2.0           1.14.6                          Scales worker nodes within autoscaling groups.    
stable/cluster-overprovisioner          0.2.6           1.0                             Installs the a deployment that overprovisions t...
stable/cockroachdb                      2.1.16          19.1.5                          CockroachDB is a scalable, survivable, strongly...
stable/collabora-code                   1.0.5           4.0.3.1                         A Helm chart for Collabora Office - CODE-Edition  
stable/concourse                        8.2.7           5.6.0                           Concourse is a simple and scalable CI system.     
stable/consul                           3.9.2           1.5.3                           Highly available and distributed service discov...
stable/contour                          0.1.0           v0.15.0                         Contour Ingress controller for Kubernetes         
stable/coredns                          1.7.4           1.6.4                           CoreDNS is a DNS server that chains plugins and...
stable/cosbench                         1.0.1           0.0.6                           A benchmark tool for cloud object storage services
stable/coscale                          1.0.0           3.16.0                          CoScale Agent                                     
stable/couchbase-operator               1.0.1           1.2.1                           A Helm chart to deploy the Couchbase Autonomous...
stable/couchdb                          2.3.0           2.3.1                           DEPRECATED A database featuring seamless multi-...
stable/dask                             3.1.0           1.1.5                           Distributed computation in Python with task sch...
stable/dask-distributed                 2.0.2                                           DEPRECATED: Distributed computation in Python     
stable/datadog                          1.38.3          6.14.0                          DataDog Agent 
...
```

Als u de lijst met grafieken wilt bijwerken, gebruikt u de opdracht [helm opslag plaats update][helm-repo-update] .

```console
helm repo update
```

In het volgende voor beeld ziet u een geslaagde opslag plaats-update:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Helm-grafieken uitvoeren

Als u grafieken met helm wilt installeren, gebruikt u de [installatie opdracht helm][helm-install-command] en geeft u een release naam en de naam van de grafiek op die u wilt installeren. Als u de installatie van een helm-diagram in actie wilt zien, kunt u een eenvoudige nginx-implementatie installeren met behulp van een helm-grafiek.

```console
helm install my-nginx-ingress ingress-nginx/ingress-nginx \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

In de volgende verkorte voorbeeld uitvoer ziet u de implementatie status van de Kubernetes-resources die zijn gemaakt door de helm-grafiek:

```console
$ helm install my-nginx-ingress ingress-nginx/ingress-nginx \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller'
...
```

Gebruik de `kubectl get services` opdracht om het *externe IP-adres* van uw service op te halen.

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller
```

Met de onderstaande opdracht wordt bijvoorbeeld het *externe IP-adres* voor de service *My-nginx-ingress-ingress-nginx-controller* weer gegeven:

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller

NAME                                        TYPE           CLUSTER-IP   EXTERNAL-IP      PORT(S)                      AGE   SELECTOR
my-nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.2.237   <EXTERNAL-IP>    80:31380/TCP,443:32239/TCP   72s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=my-nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>Releases weer geven

Gebruik de opdracht om een lijst weer te geven met releases die op uw cluster zijn geïnstalleerd `helm list` .

```console
helm list
```

In het volgende voor beeld ziet u de release van *mijn nginx-ingang* die in de vorige stap is geïmplementeerd:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een helm-grafiek implementeert, worden er een aantal Kubernetes-resources gemaakt. Deze resources omvatten Peul, implementaties en services. Als u deze resources wilt opschonen, gebruikt u de opdracht [helm uninstall][helm-cleanup] en geeft u de naam van uw release op, zoals gevonden in de vorige `helm list` opdracht.

```console
helm uninstall my-nginx-ingress
```

In het volgende voor beeld ziet u de release met de naam *mijn-nginx-ingress* is verwijderd:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="next-steps"></a>Volgende stappen

Zie de helm-documentatie voor meer informatie over het beheren van Kubernetes-toepassings implementaties met helm.

> [!div class="nextstepaction"]
> [Helm-documentatie][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md
