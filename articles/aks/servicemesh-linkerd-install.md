---
title: Linkerd installeren in Azure Kubernetes Service (AKS)
description: Meer informatie over het installeren en gebruiken van Linkerd om een servicemesh te maken in een AKS-cluster (Azure Kubernetes Service)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 419b61527b68299c82dec4f2f5da6b0220859cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593711"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Linkerd installeren in Azure Kubernetes Service (AKS)

[Linkerd][linkerd-github] is een open-source service mesh en [CNCF incubatie project.][linkerd-cncf] Linkerd is een ultralichte servicemesh die functies biedt die verkeersbeheer, serviceidentiteit en -beveiliging, betrouwbaarheid en waarneembaarheid omvatten. Zie voor meer informatie over Linkerd de officiële [Linkerd FAQ][linkerd-faq] en [Linkerd Architecture][linkerd-architecture] documentatie.

In dit artikel ziet u hoe u Linkerd installeert. De linkerd `linkerd` client binaire is geïnstalleerd op uw client machine en de Linkerd componenten worden geïnstalleerd in een Kubernetes cluster op AKS.

> [!NOTE]
> Deze instructies verwijzen `stable-2.6.0`naar Linkerd-versie .
>
> De Linkerd `stable-2.6.x` kan worden `1.13+`uitgevoerd tegen Kubernetes versies. U vindt extra stabiele en rand Linkerd versies op [GitHub - Linkerd Releases][linkerd-github-releases].

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Download en installeer de Linkerd linkerd client binaire
> * Linkerd installeren op AKS
> * De Linkerd-installatie valideren
> * Toegang tot het dashboard
> * Linkerd van AKS verwijderen

## <a name="before-you-begin"></a>Voordat u begint

De stappen in dit artikel gaan ervan uit dat `1.13` u een AKS-cluster hebt gemaakt (Kubernetes en hoger, met RBAC ingeschakeld) en een `kubectl` verbinding met het cluster hebt gemaakt. Als u hulp nodig hebt met een van deze items, zie dan de [AKS snelstart][aks-quickstart].

Alle Linkerd-pods moeten worden gepland om op Linux-knooppunten te draaien - deze instelling is de standaardinstelling in de onderstaande installatiemethode en vereist geen extra configuratie.

Dit artikel scheidt de Linkerd installatiebegeleiding in verscheidene afzonderlijke stappen. Het resultaat is hetzelfde in structuur als de officiële Linkerd getting started [begeleiding][linkerd-getting-started].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>Linkerd installeren op AKS

Voordat we Linkerd installeren, voeren we pre-installatiecontroles uit om te bepalen of het besturingsvlak op ons AKS-cluster kan worden geïnstalleerd:

```console
linkerd check --pre
```

U ziet als volgt iets als het volgende om aan te geven dat uw AKS-cluster een geldig installatiedoel is voor Linkerd:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

Nu is het tijd om de Linkerd componenten te installeren. Gebruik `linkerd` de `kubectl` binaire bestanden om de Linkerd-componenten in uw AKS-cluster te installeren. Er `linkerd` wordt automatisch een naamruimte gemaakt en de componenten worden in deze naamruimte geïnstalleerd.

```console
linkerd install | kubectl apply -f -
```

Linkerd implementeert een aantal objecten. U ziet de lijst van de `linkerd install` uitvoer van uw opdracht hierboven. De implementatie van de Linkerd-componenten duurt ongeveer 1 minuut, afhankelijk van uw clusteromgeving.

Op dit moment hebt u Linkerd geïmplementeerd in uw AKS-cluster. Om ervoor te zorgen dat we een succesvolle implementatie van Linkerd hebben, gaan we verder naar het volgende gedeelte naar [Validate the Linkerd-installatie.](#validate-the-linkerd-installation)

## <a name="validate-the-linkerd-installation"></a>De Linkerd-installatie valideren

Controleer of de resources zijn gemaakt. Gebruik de [kubectl get svc][kubectl-get] en [kubectl][kubectl-get] `linkerd` get pod-opdrachten om de naamruimte `linkerd install` op te vragen, waar de Linkerd-componenten door de opdracht zijn geïnstalleerd:

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

In de volgende voorbeelduitvoer worden de services en pods weergegeven (gepland op Linux-knooppunten) die nu moeten worden uitgevoerd:

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

Linkerd biedt een `linkerd` opdracht via de clientbinaire om te valideren dat het linkerd-controlevlak met succes is geïnstalleerd en geconfigureerd.

```console
linkerd check
```

U ziet als volgt iets als het volgende om aan te geven dat uw installatie succesvol is:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>Toegang tot het dashboard

Linkerd wordt geleverd met een dashboard dat inzicht geeft in de servicemesh en workloads. Als u toegang wilt `linkerd dashboard` krijgen tot het dashboard, gebruikt u de opdracht. Deze opdracht maakt gebruik van [kubectl port-forward][kubectl-port-forward] om een veilige verbinding te maken tussen uw clientmachine en de relevante pods in uw AKS-cluster. Het opent dan automatisch het dashboard in uw standaardbrowser.

```console
linkerd dashboard
```

De opdracht maakt ook een poort-forward en retourneert een koppeling voor de Grafana-dashboards.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>Linkerd van AKS verwijderen

> [!WARNING]
> Het verwijderen van Linkerd uit een lopend systeem kan leiden tot verkeersgerelateerde problemen tussen uw services. Zorg ervoor dat u voorzieningen hebt getroffen om uw systeem nog steeds correct te laten functioneren zonder Linkerd voordat u verdergaat.

Eerst moet je de data plane proxy's verwijderen. Verwijder eventuele [annotaties][linkerd-automatic-proxy-injection] voor automatische proxy-injectie uit naamruimten voor werkbelasting en rol uw werkbelastingimplementaties uit. Uw workloads mogen geen bijbehorende gegevensvlakonderdelen meer bevatten.

Verwijder ten slotte het controlevlak als volgt:

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende officiële linkerd-richtlijnen voor meer installatie- en configuratieopties voor Linkerd:

- [Linkerd - Helm installatie][linkerd-install-with-helm]
- [Linkerd - Multi-stage installatie om te voorzien in rolprivileges][linkerd-multi-stage-installation]

U ook aanvullende scenario's volgen met:

- [Linkerd emojivoto demo][linkerd-demo-emojivoto]
- [Linkerd boeken demo][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
