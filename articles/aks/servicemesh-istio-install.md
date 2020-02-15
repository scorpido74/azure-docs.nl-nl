---
title: Istio installeren in azure Kubernetes service (AKS)
description: Meer informatie over het installeren en gebruiken van Istio voor het maken van een service-net in een Azure Kubernetes service-cluster (AKS)
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 11/15/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d886205e88db780a7a09554391bd975f57eebfe7
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251733"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Istio installeren en gebruiken in azure Kubernetes service (AKS)

[Istio][istio-github] is een open-source service-net dat een belang rijke set functionaliteit biedt voor de micro Services in een Kubernetes-cluster. Deze functies omvatten verkeer beheer, service-identiteit en-beveiliging, afdwinging van beleid en de bekrachtiging. Zie voor meer informatie over Istio de officiële [What is Istio?][istio-docs-concepts] -documentatie.

In dit artikel wordt beschreven hoe u Istio installeert. De Istio `istioctl` binaire client wordt geïnstalleerd op de client computer en de Istio-onderdelen worden geïnstalleerd in een Kubernetes-cluster op AKS.

> [!NOTE]
> De volgende instructies verwijzen naar Istio-versie `1.4.0`.
>
> De Istio-`1.4.x` releases zijn getest door het Istio-team tegen Kubernetes-versies `1.13`, `1.14``1.15`. U kunt aanvullende Istio-versies vinden op [github-Istio-releases][istio-github-releases], informatie over elk van de releases van [Istio News][istio-release-notes] en ondersteunde Kubernetes-versies op [Istio algemene veelgestelde vragen][istio-faq].

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Down load en installeer het binaire bestand voor de Istio istioctl-client
> * Istio installeren op AKS
> * De Istio-installatie valideren
> * Toegang tot de invoeg toepassingen
> * Istio verwijderen uit AKS

## <a name="before-you-begin"></a>Voordat u begint

Voor de stappen die in dit artikel worden beschreven, wordt ervan uitgegaan dat u een AKS-cluster hebt gemaakt (Kubernetes `1.13` en hoger, waarbij RBAC is ingeschakeld) en een `kubectl`-verbinding met het cluster heeft gemaakt. Als u hulp nodig hebt bij een van deze items, raadpleegt u de [AKS Quick][aks-quickstart]start.

Zorg ervoor dat u de Istio-documentatie over [prestaties en schaal baarheid](https://istio.io/docs/concepts/performance-and-scalability/) hebt gelezen om inzicht te krijgen in de aanvullende bron vereisten voor het uitvoeren van Istio in uw AKS-cluster. De kern-en geheugen vereisten variëren op basis van uw specifieke werk belasting. Kies het juiste aantal knoop punten en de VM-grootte voor uw installatie.

In dit artikel worden de Istio-installatie richtlijnen gescheiden in verschillende afzonderlijke stappen. Het eind resultaat is hetzelfde als de officiële Istio-installatie [richtlijnen][istio-install-istioctl].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>De Istio-onderdelen installeren op AKS

[Grafana][grafana] en [Kiali][kiali] worden geïnstalleerd als onderdeel van onze Istio-installatie. Grafana biedt analyse-en bewakings dashboards en Kiali biedt een service-net-waarneembaar dash board. In de installatie zijn voor elk van deze onderdelen referenties vereist die als [geheim][kubernetes-secrets]moeten worden verschaft.

Voordat we de Istio-onderdelen kunnen installeren, moeten we de geheimen voor Grafana en Kiali maken. Deze geheimen moeten worden geïnstalleerd in de naam ruimte `istio-system` die wordt gebruikt door Istio. Daarom moet u de naam ruimte ook maken. U moet de `--save-config` optie gebruiken bij het maken van de naam ruimte via `kubectl create`, zodat het installatie programma voor Istio `kubectl apply` in de toekomst kan worden uitgevoerd op dit object.

```console
kubectl create namespace istio-system --save-config
```

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Istio-onderdelen installeren

Nu we de Grafana-en Kiali-geheimen in het AKS-cluster hebben gemaakt, is het tijd om de Istio-onderdelen te installeren. 

De [helm][helm] -installatie benadering voor Istio zal in de toekomst worden afgeschaft. De nieuwe methode voor het installeren van Istio maakt gebruik van de `istioctl` binaire client, het [Istio-configuratie profiel][istio-configuration-profiles]en de nieuwe [Istio Control vlak-specificatie en API][istio-control-plane]. Deze nieuwe methode is wat we gebruiken om Istio te installeren.

> [!NOTE]
> Istio moet momenteel worden ingepland om te worden uitgevoerd op Linux-knoop punten. Als u Windows Server-knoop punten in uw cluster hebt, moet u ervoor zorgen dat de Istio van de peulen enkel worden uitgevoerd op Linux-knoop punten. We gebruiken [knooppunt selecties][kubernetes-node-selectors] om ervoor te zorgen dat de juiste knoop punten van peulen worden gepland.

> [!CAUTION]
> De [SDS-functies (service voor geheime detectie)][istio-feature-sds] en [Istio cni][istio-feature-cni] Istio zijn op dit moment in [alpha][istio-feature-stages]. u moet er dus rekening mee doen voordat u deze inschakelt. 
>
> Houd er rekening mee dat de [Service account token volume projectie][kubernetes-feature-sa-projected-volume] Kubernetes-functie (een vereiste voor SDS) nu is **ingeschakeld** voor alle Kubernetes 1,13 en hogere versies op AKS.

Maak een bestand met de naam `istio.aks.yaml` met de volgende inhoud. Dit bestand bevat de specificatie details van het [Istio-besturings vlak][istio-control-plane] voor het configureren van Istio.

```yaml
apiVersion: install.istio.io/v1alpha2
kind: IstioControlPlane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
      # Enable mutual TLS for the control plane
      controlPlaneSecurityEnabled: true
      mtls:
        # Require all service to service communication to have mtls
        enabled: false
    grafana:
      # Enable Grafana deployment for analytics and monitoring dashboards
      enabled: true
      security:
        # Enable authentication for Grafana
        enabled: true
    kiali:
      # Enable the Kiali deployment for a service mesh observability dashboard
      enabled: true
    tracing:
      # Enable the Jaeger deployment for tracing
      enabled: true
```

Installeer istio met behulp van de `istioctl apply` opdracht en de bovenstaande `istio.aks.yaml` Istio Control vlak-specificatie bestand als volgt:

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

Het installatie programma implementeert een aantal [CRDs][kubernetes-crd] en beheert vervolgens afhankelijkheden om alle relevante objecten te installeren die zijn gedefinieerd voor deze configuratie van Istio. Als het goed is, ziet u iets zoals in het volgende uitvoer fragment.

```console
Applying manifests for these components:
- Tracing
- EgressGateway
- NodeAgent
- Grafana
- Policy
- Citadel
- CertManager
- IngressGateway
- Injector
- Prometheus
- PrometheusOperator
- Kiali
- Telemetry
- Galley
- Cni
- Pilot
- Base
- CoreDNS
NodeAgent is waiting on a prerequisite...
Telemetry is waiting on a prerequisite...
Galley is waiting on a prerequisite...
Cni is waiting on a prerequisite...
Grafana is waiting on a prerequisite...
Policy is waiting on a prerequisite...
Citadel is waiting on a prerequisite...
EgressGateway is waiting on a prerequisite...
Tracing is waiting on a prerequisite...
Kiali is waiting on a prerequisite...
PrometheusOperator is waiting on a prerequisite...
IngressGateway is waiting on a prerequisite...
Prometheus is waiting on a prerequisite...
CertManager is waiting on a prerequisite...
Injector is waiting on a prerequisite...
Pilot is waiting on a prerequisite...
Applying manifest for component Base
Waiting for CRDs to be applied.
CRDs applied.
Finished applying manifest for component Base
Prerequisite for Tracing has completed, proceeding with install.
Prerequisite for Injector has completed, proceeding with install.
Prerequisite for Telemetry has completed, proceeding with install.
Prerequisite for Policy has completed, proceeding with install.
Prerequisite for PrometheusOperator has completed, proceeding with install.
Prerequisite for NodeAgent has completed, proceeding with install.
Prerequisite for IngressGateway has completed, proceeding with install.
Prerequisite for Kiali has completed, proceeding with install.
Prerequisite for EgressGateway has completed, proceeding with install.
Prerequisite for Galley has completed, proceeding with install.
Prerequisite for Grafana has completed, proceeding with install.
Prerequisite for Cni has completed, proceeding with install.
Prerequisite for Citadel has completed, proceeding with install.
Applying manifest for component Tracing
Prerequisite for Prometheus has completed, proceeding with install.
Prerequisite for Pilot has completed, proceeding with install.
Prerequisite for CertManager has completed, proceeding with install.
Applying manifest for component Kiali
Applying manifest for component Prometheus
Applying manifest for component IngressGateway
Applying manifest for component Policy
Applying manifest for component Telemetry
Applying manifest for component Citadel
Applying manifest for component Galley
Applying manifest for component Pilot
Applying manifest for component Injector
Applying manifest for component Grafana
Finished applying manifest for component Kiali
Finished applying manifest for component Tracing
Finished applying manifest for component Prometheus
Finished applying manifest for component Citadel
Finished applying manifest for component Policy
Finished applying manifest for component IngressGateway
Finished applying manifest for component Injector
Finished applying manifest for component Galley
Finished applying manifest for component Pilot
Finished applying manifest for component Grafana
Finished applying manifest for component Telemetry

Component IngressGateway installed successfully:
================================================

serviceaccount/istio-ingressgateway-service-account created
deployment.apps/istio-ingressgateway created
gateway.networking.istio.io/ingressgateway created
sidecar.networking.istio.io/default created
poddisruptionbudget.policy/ingressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
service/istio-ingressgateway created

...
```

Op dit moment hebt u Istio geïmplementeerd op uw AKS-cluster. Om ervoor te zorgen dat we een geslaagde implementatie van Istio hebben, gaan we verder met de volgende sectie om [de Istio-installatie te valideren](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>De Istio-installatie valideren

Controleer eerst of de verwachte services zijn gemaakt. Gebruik de opdracht [kubectl Get SVC][kubectl-get] om de actieve services weer te geven. Voer een query uit op de naam ruimte `istio-system`, waarbij de Istio en invoeg toepassings onderdelen zijn geïnstalleerd door de `istio` helm-grafiek:

```console
kubectl get svc --namespace istio-system --output wide
```

In de volgende voorbeeld uitvoer ziet u de services die nu moeten worden uitgevoerd:

- `istio-*` Services
- `jaeger-*`, `tracing`en `zipkin` invoeg toepassing voor tracering van services
- service voor metrische gegevens van `prometheus` toevoegen
- dash board-service voor `grafana`-invoeg toepassingen en-bewaking
- dash board service voor de `kiali`-invoeg service

Als de `istio-ingressgateway` een extern IP-adres van `<pending>`bevat, wacht u enkele minuten totdat er een IP-adressen zijn toegewezen door Azure-netwerken.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.116.147   <none>           3000/TCP                                                                                                                     92s   app=grafana
istio-citadel            ClusterIP      10.0.248.152   <none>           8060/TCP,15014/TCP                                                                                                           94s   app=citadel
istio-galley             ClusterIP      10.0.50.100    <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         93s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.36.213    20.188.221.111   15020:30369/TCP,80:31368/TCP,443:30045/TCP,15029:32011/TCP,15030:31212/TCP,15031:32411/TCP,15032:30009/TCP,15443:30010/TCP   93s   app=istio-ingressgateway
istio-pilot              ClusterIP      10.0.23.222    <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       93s   istio=pilot
istio-policy             ClusterIP      10.0.59.250    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 93s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.123.219   <none>           443/TCP                                                                                                                      93s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.216.9     <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       89s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   96s   app=jaeger
jaeger-collector         ClusterIP      10.0.221.24    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                95s   app=jaeger
jaeger-query             ClusterIP      10.0.46.154    <none>           16686/TCP                                                                                                                    95s   app=jaeger
kiali                    ClusterIP      10.0.174.97    <none>           20001/TCP                                                                                                                    94s   app=kiali
prometheus               ClusterIP      10.0.245.226   <none>           9090/TCP                                                                                                                     94s   app=prometheus
tracing                  ClusterIP      10.0.249.95    <none>           9411/TCP                                                                                                                     95s   app=jaeger
zipkin                   ClusterIP      10.0.154.89    <none>           9411/TCP                                                                                                                     94s   app=jaeger
```

Controleer vervolgens of het vereiste peul is gemaakt. Gebruik de opdracht [kubectl Get peul][kubectl-get] en voer de query opnieuw uit op de `istio-system` naam ruimte:

```console
kubectl get pods --namespace istio-system
```

In de volgende voorbeeld uitvoer ziet u de peulen die worden uitgevoerd:

- het `istio-*`e peul
- de metrische gegevens voor de `prometheus-*`-invoeg toepassing pod
- de pod-invoeg toepassing voor analyse en bewaking van het dash board `grafana-*`
- het Pod-dash board voor de `kiali`-invoeg toepassing

```console
NAME                                          READY   STATUS    RESTARTS   AGE
grafana-6bc97ff99-k9sk4                       1/1     Running   0          92s
istio-citadel-6b5c754454-tb8nf                1/1     Running   0          94s
istio-galley-7d6d78d7c5-zshsd                 2/2     Running   0          94s
istio-ingressgateway-85869c5cc7-x5d76         1/1     Running   0          95s
istio-pilot-787d6995b5-n5vrj                  2/2     Running   0          94s
istio-policy-6cf4fbc8dc-sdsg5                 2/2     Running   2          94s
istio-sidecar-injector-5d5b978668-wrz2s       1/1     Running   0          94s
istio-telemetry-5498db684-6kdnw               2/2     Running   1          94s
istio-tracing-78548677bc-74tx6                1/1     Running   0          96s
kiali-59b7fd7f68-92zrh                        1/1     Running   0          95s
prometheus-7c7cf9dbd6-rjxcv                   1/1     Running   0          94s
```

Alle peulen moeten de status van `Running`weer geven. Als uw peul niet over deze status beschikt, moet u een paar minuten wachten totdat dit het geval is. Als een van de peulen een probleem rapporteert, gebruikt u de [kubectl pod][kubectl-describe] opdracht om de uitvoer en status te controleren.

## <a name="accessing-the-add-ons"></a>De invoeg toepassingen openen

Er zijn een aantal invoeg toepassingen geïnstalleerd door Istio in onze installatie hierboven die extra functionaliteit bieden. De webtoepassingen voor de invoeg toepassingen worden **niet** openbaar weer gegeven via een extern IP-adres. 

Gebruik de opdracht `istioctl dashboard` om toegang te krijgen tot de gebruikers interfaces van de invoeg toepassing. Met deze opdracht maakt u gebruik van [kubectl-poort-voorwaarts][kubectl-port-forward] en een wille keurige poort om een beveiligde verbinding te maken tussen uw client computer en de relevante pod in uw AKS-cluster. De invoeg toepassing wordt vervolgens automatisch geopend in de standaard browser.

We hebben een extra beveiligingslaag voor Grafana en Kiali toegevoegd door in dit artikel eerder referenties op te geven.

### <a name="grafana"></a>Grafana

De analyse-en bewakings dashboards voor Istio worden gegeven door [Grafana][grafana]. Vergeet niet om de referenties te gebruiken die u eerder hebt gemaakt via het Grafana-geheim wanneer u hierom wordt gevraagd. Open het Grafana-dash board veilig als volgt:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Metrische gegevens voor Istio worden gegeven door [Prometheus][prometheus]. Open het Prometheus-dash board veilig als volgt:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Tracering binnen Istio wordt verschaft door [Jaeger][jaeger]. Open het Jaeger-dash board veilig als volgt:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

Een dash board met Service-net-behulp van [Kiali][kiali]. Vergeet niet om de referenties te gebruiken die u eerder hebt gemaakt via het Kiali-geheim wanneer u hierom wordt gevraagd. Open het Kiali-dash board veilig als volgt:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Er is een eenvoudige interface beschikbaar voor de [Envoy][envoy] -proxy's. Het bevat configuratie-informatie en metrische gegevens voor een Envoy-proxy die wordt uitgevoerd in een opgegeven pod. Open de Envoy-interface veilig als volgt:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Istio verwijderen uit AKS

> [!WARNING]
> Het verwijderen van Istio van een actief systeem kan leiden tot problemen met verkeer tussen uw services. Zorg ervoor dat u voor het systeem nog steeds goed werkt zonder Istio voordat u doorgaat.

### <a name="remove-istio-components-and-namespace"></a>Istio-onderdelen en naam ruimte verwijderen

Als u Istio wilt verwijderen uit uw AKS-cluster, gebruikt u de `istioctl manifest generate` opdracht met het bestand met de `istio.aks.yaml` Istio Control vlak. Hiermee wordt het geïmplementeerde manifest gegenereerd, waarna we naar `kubectl delete` gaan om alle geïnstalleerde onderdelen en de `istio-system` naam ruimte te verwijderen.

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Istio CRDs en geheimen verwijderen

Met de bovenstaande opdrachten worden alle Istio-onderdelen en-naam ruimten verwijderd, maar er zijn nog steeds gegenereerde Istio geheimen. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Volgende stappen

In de volgende documentatie wordt beschreven hoe u Istio kunt gebruiken om intelligente route ring te bieden voor het implementeren van een distributie release:

> [!div class="nextstepaction"]
> [AKS Istio intelligent routerings scenario][istio-scenario-routing]

Als u meer wilt weten over de installatie-en configuratie opties voor Istio, raadpleegt u de volgende officiële Istio-richt lijnen:

- [Istio-installatie handleidingen][istio-installation-guides]

U kunt ook aanvullende scenario's volgen met behulp van:

- [Voor beeld van Istio Bookinfo-toepassing][istio-bookinfo-example]

Raadpleeg de volgende Azure Monitor documentatie voor meer informatie over het bewaken van uw AKS-toepassing met behulp van Application Insights en Istio:

- [Controle van toepassings bewaking op nul voor Kubernetes-gehoste toepassingen][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha12.pb/#IstioControlPlane
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
