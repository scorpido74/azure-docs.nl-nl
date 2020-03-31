---
title: Containers met Helm implementeren in Kubernetes op Azure
description: Meer informatie over het gebruik van het helm-verpakkingshulpprogramma om containers te implementeren in een AKS-cluster (Azure Kubernetes Service)
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/22/2019
ms.author: zarhoads
ms.openlocfilehash: 4a9ccaff0e3425c365a64ecb4fbadf3c7aa8dcfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595175"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Toepassingen met Helm installeren in Azure Kubernetes Service (AKS)

[Helm][helm] is een open-source verpakkingstool die u helpt bij het installeren en beheren van de levenscyclus van Kubernetes-toepassingen. Net als bij Linux-pakketmanagers zoals *APT* en *Yum*, wordt Helm gebruikt om Kubernetes-grafieken te beheren, die pakketten zijn met vooraf geconfigureerde Kubernetes-bronnen.

In dit artikel ziet u hoe u Helm configureert en gebruikt in een Kubernetes-cluster op AKS.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

U moet ook de Helm CLI geïnstalleerd, dat is de client die draait op uw ontwikkelingssysteem. Hiermee u toepassingen starten, stoppen en beheren met Helm. Als u de Azure Cloud Shell gebruikt, is de Helm CLI al geïnstalleerd. Zie [Helm installeren][helm-install]voor installatie-instructies op uw lokale platform.

> [!IMPORTANT]
> Helm is bedoeld om te draaien op Linux-knooppunten. Als u Windows Server-knooppunten in uw cluster hebt, moet u ervoor zorgen dat Helm-pods alleen worden uitgevoerd op Linux-knooppunten. U moet er ook voor zorgen dat alle Helm-diagrammen die u installeert, ook op de juiste knooppunten worden uitgevoerd. De opdrachten in dit artikel gebruiken [knooppuntselecties][k8s-node-selector] om ervoor te zorgen dat pods zijn gepland op de juiste knooppunten, maar niet alle Helm-diagrammen kunnen een knooppuntkiezer blootleggen. U ook overwegen andere opties op uw cluster te gebruiken, zoals [taints.][taints]

## <a name="verify-your-version-of-helm"></a>Uw versie van Helm verifiëren

Gebruik `helm version` de opdracht om de versie van Helm die u hebt geïnstalleerd te verifiëren:

```console
helm version
```

In het volgende voorbeeld wordt Helm versie 3.0.0 geïnstalleerd:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

Voor Helm v3, volg de stappen in de [Helm v3 sectie](#install-an-application-with-helm-v3). Voor Helm v2, volg de stappen in de [Helm v2 sectie](#install-an-application-with-helm-v2)

## <a name="install-an-application-with-helm-v3"></a>Een applicatie installeren met Helm v3

### <a name="add-the-official-helm-stable-charts-repository"></a>Voeg de officiële Helm stable charts repository toe

Gebruik de [opdracht helmrepo][helm-repo-add] om de officiële Helm-stabiele grafieken repository toe te voegen.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

### <a name="find-helm-charts"></a>Helmdiagrammen zoeken

Helmdiagrammen worden gebruikt om toepassingen in een Kubernetes-cluster te implementeren. Als u wilt zoeken naar vooraf gemaakte Helm-diagrammen, gebruikt u de [opdracht helmzoeken:][helm-search]

```console
helm search repo stable
```

In de volgende verkorte voorbeelduitvoer ziet u een aantal van de helmdiagrammen die beschikbaar zijn voor gebruik:


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

Als u de lijst met grafieken wilt bijwerken, gebruikt u de opdracht [repo-update van het roer.][helm-repo-update] In het volgende voorbeeld wordt een geslaagde repo-update weergegeven:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Helmdiagrammen uitvoeren

Als u grafieken met Helm wilt installeren, gebruikt u de opdracht [helminstallatie][helm-install-command] en geeft u een releasenaam en de naam van de te installeren grafiek op. Als u het installeren van een Helm-diagram in actie wilt zien, installeren we een basisnginx-implementatie met behulp van een Helm-grafiek.

```console
helm install my-nginx-ingress stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

In de volgende verkorte voorbeelduitvoer ziet u de implementatiestatus van de Kubernetes-bronnen die zijn gemaakt door de grafiek Helm:

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

Gebruik `kubectl get services` de opdracht om het *externe IP-IP* van uw service op te halen. De onderstaande opdracht toont bijvoorbeeld het *EXTERNE-IP* voor de service *my-nginx-ingress-controller:*

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-controller

NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
my-nginx-ingress-controller   LoadBalancer   10.0.123.1     <EXTERNAL-IP>   80:31301/TCP,443:31623/TCP   96s   app=nginx-ingress,component=controller,release=my-nginx-ingress
```

### <a name="list-releases"></a>Lijstreleases

Als u een lijst met releases wilt `helm list` zien die op uw cluster zijn geïnstalleerd, gebruikt u de opdracht.

```console
helm list
```

In het volgende voorbeeld ziet u de release *van my-nginx-ingress* die in de vorige stap is geïmplementeerd:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een Helm-diagram implementeert, worden een aantal Kubernetes-bronnen gemaakt. Deze bronnen omvatten pods, implementaties en services. Als u deze resources wilt opschonen, gebruikt u de opdracht `helm list` Helm [verwijderen][helm-cleanup] en geeft u de releasenaam op, zoals in de vorige opdracht.

```console
helm uninstall my-nginx-ingress
```

In het volgende voorbeeld ziet u dat de release met de naam *my-nginx-ingress* is verwijderd:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="install-an-application-with-helm-v2"></a>Een applicatie installeren met Helm v2

### <a name="create-a-service-account"></a>Een serviceaccount maken

Voordat u Helm implementeren in een AKS-cluster met RBAC-functie, hebt u een serviceaccount en rolbinding nodig voor de Tiller-service. Zie [Tiller, Namespaces en RBAC][tiller-rbac]voor meer informatie over het beveiligen van Helm / Tiller in een cluster met RBAC. Als uw AKS-cluster niet is ingeschakeld, slaat u deze stap over.

Maak een `helm-rbac.yaml` bestand met de naam en kopie in de volgende YAML:

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

Maak het serviceaccount en `kubectl apply` de rolbinding met de opdracht:

```console
kubectl apply -f helm-rbac.yaml
```

### <a name="secure-tiller-and-helm"></a>Veilige Tiller en Helm

De Helm-client en Tiller-service verifiëren en communiceren met elkaar via TLS/SSL. Deze verificatiemethode helpt om het Kubernetes-cluster te beveiligen en welke services kunnen worden geïmplementeerd. Om de beveiliging te verbeteren, u uw eigen ondertekende certificaten genereren. Elke Helm-gebruiker zou zijn eigen clientcertificaat ontvangen en Tiller zou worden geïnitialiseerd in het Kubernetes-cluster met toegepaste certificaten. Zie [TLS/SSL gebruiken tussen Helm en Tiller][helm2-ssl]voor meer informatie.

Met een RBAC-enabled Kubernetes-cluster u het toegangsniveau bepalen dat Tiller tot het cluster heeft. U de Kubernetes-naamruimte definiëren waarin Tiller is geïmplementeerd en beperken in welke naamruimten Tiller vervolgens resources kan implementeren. Met deze aanpak u Tiller-instanties in verschillende naamruimten maken en implementatiegrenzen beperken en de gebruikers van de Helm-client beperken tot bepaalde naamruimten. Zie [Helm-op rollen gebaseerde toegangscontroles][helm2-rbac]voor meer informatie.

### <a name="configure-helm"></a>Helm configureren

Als u een basis-Tiller wilt implementeren in een AKS-cluster, gebruikt u de opdracht [helminit.][helm2-init] Als uw cluster niet is ingeschakeld, `--service-account` verwijdert u het argument en de waarde. De volgende voorbeelden stellen ook de [history-max][helm2-history-max] op 200.

Als u TLS/SSL voor Tiller en Helm hebt geconfigureerd, slaat `--tiller-tls-` u deze basisinitialisatiestap over en verstrekt u in plaats daarvan de vereiste zoals weergegeven in het volgende voorbeeld.

```console
helm init --history-max 200 --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

Als u TLS/SSL tussen Helm en `--tiller-tls-*` Tiller hebt geconfigureerd, geeft u de parameters en namen van uw eigen certificaten op, zoals in het volgende voorbeeld wordt weergegeven:

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

### <a name="find-helm-charts"></a>Helmdiagrammen zoeken

Helmdiagrammen worden gebruikt om toepassingen in een Kubernetes-cluster te implementeren. Als u wilt zoeken naar vooraf gemaakte Helm-diagrammen, gebruikt u de [opdracht helmzoeken:][helm2-search]

```console
helm search
```

In de volgende verkorte voorbeelduitvoer ziet u een aantal van de helmdiagrammen die beschikbaar zijn voor gebruik:

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

Als u de lijst met grafieken wilt bijwerken, gebruikt u de opdracht [repo-update van het roer.][helm2-repo-update] In het volgende voorbeeld wordt een geslaagde repo-update weergegeven:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

### <a name="run-helm-charts"></a>Helmdiagrammen uitvoeren

Als u grafieken met Helm wilt installeren, gebruikt u de opdracht [helminstallatie][helm2-install-command] en geeft u de naam op van de te installeren grafiek. Als u het installeren van een Helm-diagram in actie wilt zien, installeren we een basisnginx-implementatie met behulp van een Helm-grafiek. Als u TLS/SSL hebt `--tls` geconfigureerd, voegt u de parameter toe om het Helm-clientcertificaat te gebruiken.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

In de volgende verkorte voorbeelduitvoer ziet u de implementatiestatus van de Kubernetes-bronnen die zijn gemaakt door de grafiek Helm:

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

Het duurt een minuut of twee voordat het *EXTERNE IP-adres* van de nginx-ingress-controller-service wordt ingevuld en u toegang geeft tot het met een webbrowser.

### <a name="list-helm-releases"></a>Lijst Helm releases

Als u een lijst met releases wilt zien die op uw cluster zijn geïnstalleerd, gebruikt u de opdracht [helmlijst.][helm2-list] In het volgende voorbeeld ziet u de nginx-ingress-release die in de vorige stap is geïmplementeerd. Als u TLS/SSL hebt `--tls` geconfigureerd, voegt u de parameter toe om het Helm-clientcertificaat te gebruiken.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

### <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een Helm-diagram implementeert, worden een aantal Kubernetes-bronnen gemaakt. Deze bronnen omvatten pods, implementaties en services. Als u deze bronnen `helm delete` wilt opschonen, gebruikt u de `helm list` opdracht en geeft u de releasenaam op, zoals in de vorige opdracht. In het volgende voorbeeld wordt de release met de naam *flailing-alpaca*verwijderd:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Volgende stappen

Zie de Documentatie Helm voor meer informatie over het beheren van Kubernetes-toepassingsimplementaties met Helm.

> [!div class="nextstepaction"]
> [Helm documentatie][helm-documentation]

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
