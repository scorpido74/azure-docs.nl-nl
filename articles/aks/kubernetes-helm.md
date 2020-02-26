---
title: Containers implementeren met helm in Kubernetes op Azure
description: Meer informatie over het gebruik van het helm-verpakkings programma voor het implementeren van containers in een Azure Kubernetes service-cluster (AKS)
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/22/2019
ms.author: zarhoads
ms.openlocfilehash: 4a9ccaff0e3425c365a64ecb4fbadf3c7aa8dcfb
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595175"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Toepassingen installeren met helm in azure Kubernetes service (AKS)

[Helm][helm] is een open-source-verpakkings programma waarmee u de levens cyclus van Kubernetes-toepassingen kunt installeren en beheren. Net als Linux-pakket beheerders, zoals *apt* en *yum*, wordt helm gebruikt voor het beheren van Kubernetes-grafieken, die pakketten van vooraf geconfigureerde Kubernetes-resources zijn.

In dit artikel wordt beschreven hoe u helm configureert en gebruikt in een Kubernetes-cluster op AKS.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

U hebt ook de helm CLI geïnstalleerd. Dit is de client die wordt uitgevoerd op uw ontwikkel systeem. Hiermee kunt u toepassingen starten, stoppen en beheren met helm. Als u de Azure Cloud Shell gebruikt, is de CLI van helm al geïnstalleerd. Zie [Installing helm][helm-install](Engelstalig) voor installatie-instructies op uw lokale platform.

> [!IMPORTANT]
> Helm is bedoeld om te worden uitgevoerd op Linux-knoop punten. Als u Windows Server-knoop punten in uw cluster hebt, moet u ervoor zorgen dat helm peul alleen wordt gepland voor uitvoering op Linux-knoop punten. U moet er ook voor zorgen dat alle helm-grafieken die u installeert, ook zijn gepland voor uitvoering op de juiste knoop punten. Met de opdrachten in dit artikel worden [knooppunt selecties][k8s-node-selector] gebruikt om ervoor te zorgen dat de juiste knoop punten worden gepland, maar niet alle helm-grafieken kunnen een knooppunt kiezer weer geven. U kunt ook overwegen andere opties in uw cluster te gebruiken, zoals [taints][taints].

## <a name="verify-your-version-of-helm"></a>Uw versie van helm controleren

Gebruik de `helm version` opdracht om te controleren of de versie van helm die u hebt geïnstalleerd:

```console
helm version
```

In het volgende voor beeld ziet u dat helm-versie 3.0.0 is geïnstalleerd:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

Volg voor helm v3 de stappen in de [sectie helm v3](#install-an-application-with-helm-v3). Volg voor helm v2 de stappen in de [sectie helm v2](#install-an-application-with-helm-v2)

## <a name="install-an-application-with-helm-v3"></a>Een toepassing installeren met helm v3

### <a name="add-the-official-helm-stable-charts-repository"></a>De officiële helm-opslag plaats met stabiele grafieken toevoegen

Gebruik de [helm opslag plaats][helm-repo-add] opdracht voor het toevoegen van de opslag plaats officiële helm stabiele grafieken.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
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

Als u de lijst met grafieken wilt bijwerken, gebruikt u de opdracht [helm opslag plaats update][helm-repo-update] . In het volgende voor beeld ziet u een geslaagde opslag plaats-update:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Helm-grafieken uitvoeren

Als u grafieken met helm wilt installeren, gebruikt u de [installatie opdracht helm][helm-install-command] en geeft u een release naam en de naam van de grafiek op die u wilt installeren. Als u de installatie van een helm-diagram in actie wilt zien, kunt u een eenvoudige nginx-implementatie installeren met behulp van een helm-grafiek.

```console
helm install my-nginx-ingress stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

In de volgende verkorte voorbeeld uitvoer ziet u de implementatie status van de Kubernetes-resources die zijn gemaakt door de helm-grafiek:

```console
$ helm install my-nginx-ingress stable/nginx-ingress \
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
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-controller'
...
```

Gebruik de `kubectl get services` opdracht om het *externe IP-adres* van uw service op te halen. De onderstaande opdracht toont bijvoorbeeld het *externe IP-adres* voor de service *My-nginx-ingress-controller* :

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-controller

NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
my-nginx-ingress-controller   LoadBalancer   10.0.123.1     <EXTERNAL-IP>   80:31301/TCP,443:31623/TCP   96s   app=nginx-ingress,component=controller,release=my-nginx-ingress
```

### <a name="list-releases"></a>Releases weer geven

Als u een lijst met releases wilt zien die op uw cluster zijn geïnstalleerd, gebruikt u de opdracht `helm list`.

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

## <a name="install-an-application-with-helm-v2"></a>Een toepassing installeren met helm v2

### <a name="create-a-service-account"></a>Een service account maken

Voordat u helm kunt implementeren in een AKS-cluster met RBAC, hebt u een service account en een rol-koppeling voor de Tiller-service nodig. Zie voor meer informatie over het beveiligen van helm/Tiller in een op RBAC ingeschakeld cluster [Tiller, naam ruimten en RBAC][tiller-rbac]. Als op uw AKS-cluster geen RBAC is ingeschakeld, slaat u deze stap over.

Maak een bestand met de naam `helm-rbac.yaml` en kopieer de volgende YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Maak het service account en de functie binding met de opdracht `kubectl apply`:

```console
kubectl apply -f helm-rbac.yaml
```

### <a name="secure-tiller-and-helm"></a>Beveiligde Tiller en helm

De helm-client en Tiller-service verifiëren en communiceren met elkaar met behulp van TLS/SSL. Met deze verificatie methode kunt u het Kubernetes-cluster beveiligen en welke services kunnen worden geïmplementeerd. U kunt de beveiliging verbeteren door uw eigen ondertekende certificaten te genereren. Elke helm-gebruiker ontvangt een eigen client certificaat en Tiller zou worden geïnitialiseerd in het Kubernetes-cluster waarop de certificaten zijn toegepast. Zie [using TLS/SSL tussen helm en Tiller][helm2-ssl]voor meer informatie.

Met een Kubernetes-cluster met RBAC kunt u het niveau van de toegangs Tiller beheren die is ingesteld op het cluster. U kunt de Kubernetes-naam ruimte definiëren die Tiller is geïmplementeerd in en beperken welke naam ruimten Tiller vervolgens bronnen kan implementeren in. Met deze aanpak kunt u Tiller-instanties maken in verschillende naam ruimten en de implementatie grenzen beperken en de gebruikers van helm-client op bepaalde naam ruimten bereiken. Zie [helm voor op rollen gebaseerde toegangs beheer][helm2-rbac]voor meer informatie.

### <a name="configure-helm"></a>Helm configureren

Als u een Basic-Tiller in een AKS-cluster wilt implementeren, gebruikt u de opdracht [helm init][helm2-init] . Als op uw cluster geen RBAC is ingeschakeld, verwijdert u het argument en de waarde `--service-account`. In de volgende voor beelden wordt ook de [geschiedenis ingesteld: Maxi maal][helm2-history-max] 200.

Als u TLS/SSL hebt geconfigureerd voor Tiller en helm, kunt u deze eenvoudige initialisatie stap overs Laan en in plaats daarvan de vereiste `--tiller-tls-` opgeven, zoals in het volgende voor beeld wordt weer gegeven.

```console
helm init --history-max 200 --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

Als u TLS/SSL hebt geconfigureerd tussen helm en Tiller, geeft u de `--tiller-tls-*` para meters en namen van uw eigen certificaten op, zoals wordt weer gegeven in het volgende voor beeld:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --history-max 200 \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os=linux"
```

### <a name="find-helm-charts"></a>Helm-grafieken zoeken

Helm-grafieken worden gebruikt voor het implementeren van toepassingen in een Kubernetes-cluster. Gebruik de [helm-Zoek][helm2-search] opdracht om te zoeken naar vooraf gemaakte helm-grafieken:

```console
helm search
```

In de volgende gecomprimeerde voorbeeld uitvoer ziet u enkele van de helm-grafieken die beschikbaar zijn voor gebruik:

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

Als u de lijst met grafieken wilt bijwerken, gebruikt u de opdracht [helm opslag plaats update][helm2-repo-update] . In het volgende voor beeld ziet u een geslaagde opslag plaats-update:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

### <a name="run-helm-charts"></a>Helm-grafieken uitvoeren

Als u grafieken met helm wilt installeren, gebruikt u de opdracht [helm installeren][helm2-install-command] en geeft u de naam van de grafiek op die u wilt installeren. Als u de installatie van een helm-diagram in actie wilt zien, kunt u een eenvoudige nginx-implementatie installeren met behulp van een helm-grafiek. Als u TLS/SSL hebt geconfigureerd, voegt u de para meter `--tls` toe om uw helm-client certificaat te gebruiken.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

In de volgende verkorte voorbeeld uitvoer ziet u de implementatie status van de Kubernetes-resources die zijn gemaakt door de helm-grafiek:

```
$ helm install stable/nginx-ingress --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME:   flailing-alpaca
LAST DEPLOYED: Thu May 23 12:55:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                      DATA  AGE
flailing-alpaca-nginx-ingress-controller  1     0s

==> v1/Pod(related)
NAME                                                            READY  STATUS             RESTARTS  AGE
flailing-alpaca-nginx-ingress-controller-56666dfd9f-bq4cl       0/1    ContainerCreating  0         0s
flailing-alpaca-nginx-ingress-default-backend-66bc89dc44-m87bp  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                                           TYPE          CLUSTER-IP  EXTERNAL-IP  PORT(S)                     AGE
flailing-alpaca-nginx-ingress-controller       LoadBalancer  10.0.109.7  <pending>    80:31219/TCP,443:32421/TCP  0s
flailing-alpaca-nginx-ingress-default-backend  ClusterIP     10.0.44.97  <none>       80/TCP                      0s
...
```

Het duurt een paar minuten voordat het *externe IP-* adres van de nginx-ingress-controller service moet worden gevuld en u toegang hebt tot de server met een webbrowser.

### <a name="list-helm-releases"></a>Helm releases weer geven

Gebruik de [lijst opdracht helm][helm2-list] voor een overzicht van de versies die op uw cluster zijn geïnstalleerd. In het volgende voor beeld ziet u de release van de nginx-ingang die in de vorige stap is geïmplementeerd. Als u TLS/SSL hebt geconfigureerd, voegt u de para meter `--tls` toe om uw helm-client certificaat te gebruiken.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

### <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een helm-grafiek implementeert, worden er een aantal Kubernetes-resources gemaakt. Deze resources omvatten Peul, implementaties en services. Als u deze resources wilt opschonen, gebruikt u de opdracht `helm delete` en geeft u de naam van uw release op, zoals gevonden in de vorige `helm list` opdracht. In het volgende voor beeld wordt de release met de naam *flailing-Alpaca*verwijderd:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Volgende stappen

Zie de helm-documentatie voor meer informatie over het beheren van Kubernetes-toepassings implementaties met helm.

> [!div class="nextstepaction"]
> [Documentatie voor helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm2-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm2-install-command]: https://v2.helm.sh/docs/helm/#helm-install
[helm2-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm2-list]: https://v2.helm.sh/docs/helm/#helm-list
[helm2-history-max]: https://v2.helm.sh/docs/using_helm/#initialize-helm-and-install-tiller
[helm2-rbac]: https://v2.helm.sh/docs/using_helm/#role-based-access-control
[helm2-repo-update]: https://v2.helm.sh/docs/helm/#helm-repo-update
[helm2-search]: https://v2.helm.sh/docs/helm/#helm-search
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
[helm2-ssl]: https://v2.helm.sh/docs/using_helm/#using-ssl-between-helm-and-tiller
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md
