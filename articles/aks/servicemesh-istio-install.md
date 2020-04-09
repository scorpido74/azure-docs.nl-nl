---
title: Istio installeren in Azure Kubernetes Service (AKS)
description: Meer informatie over het installeren en gebruiken van Istio om een servicemesh te maken in een AKS-cluster (Azure Kubernetes Service)
author: paulbouwer
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d1d02cb42a86023e5c341daab678c39f22f75dda
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877691"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Istio installeren en gebruiken in Azure Kubernetes Service (AKS)

[Istio][istio-github] is een open-source service mesh die een belangrijke set functionaliteit biedt voor de microservices in een Kubernetes-cluster. Deze functies omvatten verkeersbeheer, service-identiteit en -beveiliging, beleidshandhaving en waarneembaarheid. Voor meer informatie over Istio, zie de officiële [Wat is Istio?][istio-docs-concepts] documentatie.

In dit artikel ziet u hoe u Istio installeert. De Istio `istioctl` client binary is geïnstalleerd op uw client machine en de Istio componenten worden geïnstalleerd in een Kubernetes cluster op AKS.

> [!NOTE]
> De volgende instructies verwijzen `1.4.0`naar Istio versie .
>
> `1.4.x` De Istio releases zijn getest door het `1.13`Istio team tegen Kubernetes versies , `1.14`. `1.15` U vindt aanvullende Istio-versies op [GitHub - Istio Releases][istio-github-releases], informatie over elk van de releases op [Istio News][istio-release-notes] en ondersteunde Kubernetes-versies bij [Istio General FAQ][istio-faq].

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * De Istio istioctl client binary downloaden en installeren
> * Istio installeren op AKS
> * De Istio-installatie valideren
> * Toegang tot de invoegtoepassingen
> * Istio van AKS verwijderen

## <a name="before-you-begin"></a>Voordat u begint

De stappen in dit artikel gaan ervan uit dat `1.13` u een AKS-cluster hebt gemaakt (Kubernetes en hoger, met RBAC ingeschakeld) en een `kubectl` verbinding met het cluster hebt gemaakt. Als u hulp nodig hebt met een van deze items, zie dan de [AKS snelstart][aks-quickstart].

Zorg ervoor dat u de documentatie Over prestaties en schaalbaarheid van [Istio](https://istio.io/docs/concepts/performance-and-scalability/) hebt gelezen om inzicht te krijgen in de aanvullende resourcevereisten voor het uitvoeren van Istio in uw AKS-cluster. De kern- en geheugenvereisten variëren afhankelijk van uw specifieke werkbelasting. Kies een geschikt aantal knooppunten en VM-grootte om tegemoet te komen aan uw installatie.

Dit artikel scheidt de Istio installatiebegeleiding in verscheidene afzonderlijke stappen. Het eindresultaat is hetzelfde in structuur als de officiële Istio installatie [begeleiding][istio-install-istioctl].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>De Istio-componenten op AKS installeren

We installeren [Grafana][grafana] en [Kiali][kiali] als onderdeel van onze Istio installatie. Grafana biedt dashboards voor analyse en bewaking en Kiali biedt een dashboard voor het observeren van mesh.Grafana provides analytics and monitoring dashboards, and Kiali provides a service mesh observability dashboard. In onze setup vereist elk van deze componenten referenties die als [geheim][kubernetes-secrets]moeten worden opgegeven.

Voordat we de Istio componenten kunnen installeren, moeten we de geheimen voor zowel Grafana als Kiali creëren. Deze geheimen moeten worden `istio-system` geïnstalleerd in de naamruimte die zal worden gebruikt door Istio, dus we moeten ook de naamruimte te creëren. We moeten de `--save-config` optie gebruiken bij `kubectl create` het maken van de `kubectl apply` naamruimte via, zodat de Istio-installateur in de toekomst op dit object kan draaien.

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

### <a name="install-istio-components"></a>Istio-componenten installeren

Nu we met succes de Grafana en Kiali geheimen in ons AKS-cluster hebben gemaakt, is het tijd om de Istio-componenten te installeren. 

De [Helm][helm] installatieaanpak voor Istio zal in de toekomst worden afgeschaft. De nieuwe installatie benadering voor `istioctl` Istio maakt gebruik van de client binaire, de [Istio configuratieprofielen,][istio-configuration-profiles]en de nieuwe [Istio controle vlak spec en api][istio-control-plane]. Deze nieuwe aanpak is wat we zullen gebruiken om Istio te installeren.

> [!NOTE]
> Istio moet momenteel worden gepland om te draaien op Linux-knooppunten. Als u Windows Server-knooppunten in uw cluster hebt, moet u ervoor zorgen dat de Istio-pods alleen op Linux-knooppunten worden uitgevoerd. We gebruiken [knooppuntselecties][kubernetes-node-selectors] om ervoor te zorgen dat pods zijn gepland op de juiste knooppunten.

> [!CAUTION]
> De [SDS (secret discovery service)][istio-feature-sds] en [Istio CNI][istio-feature-cni] Istio functies zijn momenteel in [Alpha,][istio-feature-stages]dus dacht moet worden gegeven alvorens deze in te schakelen. 
>
> Houd er rekening mee dat de functie Kubernetes met volumeprojectie van [serviceaccounttoken][kubernetes-feature-sa-projected-volume] (een vereiste voor SDS) nu is **ingeschakeld** voor alle Kubernetes 1.13- en hogere versies op AKS.

Maak een `istio.aks.yaml` bestand met de volgende inhoud. Dit bestand bevat de details van het [Istio-controlevlak][istio-control-plane] voor het configureren van Istio.

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

Installeer istio `istioctl apply` met behulp `istio.aks.yaml` van de opdracht en de bovenstaande Istio controle vlak spec bestand als volgt:

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

De installateur implementeert een aantal [CRD's][kubernetes-crd] en beheert vervolgens afhankelijkheden om alle relevante objecten te installeren die zijn gedefinieerd voor deze configuratie van Istio. U ziet iets als het volgende uitvoerfragment.

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

Op dit moment hebt u Istio geïmplementeerd in uw AKS-cluster. Om ervoor te zorgen dat we een succesvolle implementatie van Istio hebben, gaan we verder met de volgende sectie naar [Validate the Istio-installatie.](#validate-the-istio-installation)

## <a name="validate-the-istio-installation"></a>De Istio-installatie valideren

Bevestig eerst dat de verwachte diensten zijn gemaakt. Gebruik de [kubectl get svc-opdracht][kubectl-get] om de lopende services weer te geven. Query `istio-system` de naamruimte, waar de Istio en add-on componenten zijn geïnstalleerd door het `istio` Helm grafiek:

```console
kubectl get svc --namespace istio-system --output wide
```

In de volgende voorbeelduitvoer ziet u de services die nu moeten worden uitgevoerd:

- `istio-*`Diensten
- `jaeger-*`, `tracing`en `zipkin` add-on traceringsservices
- `prometheus`add-on metrics service
- `grafana`add-on analytics en monitoring dashboard service
- `kiali`invoegservice mesh-dashboardservice

Als `istio-ingressgateway` het externe ip `<pending>`van , wacht dan een paar minuten totdat een IP-adres is toegewezen door Azure-netwerken.

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

Controleer vervolgens of de vereiste pods zijn gemaakt. Gebruik de opdracht [kubectl get][kubectl-get] pods `istio-system` en query de naamruimte opnieuw op:

```console
kubectl get pods --namespace istio-system
```

In de volgende voorbeelduitvoer worden de pods weergegeven die worden uitgevoerd:

- de `istio-*` pods
- de `prometheus-*` pod met invoegstatistieken
- de `grafana-*` dashboardpod voor add-on analytics en monitoring
- de `kiali` dashboardpod voor add-on servicemesh

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

Alle pods moeten een status `Running`van . Als uw pods deze statussen niet hebben, wacht dan een minuut of twee tot ze dat doen. Als een pods een probleem melden, gebruikt u de opdracht [kubectl describe pod][kubectl-describe] om hun uitvoer en status te controleren.

## <a name="accessing-the-add-ons"></a>Toegang tot de invoegtoepassingen

Een aantal add-ons zijn door Istio geïnstalleerd in onze setup hierboven die extra functionaliteit bieden. De webapplicaties voor de add-ons worden **niet** openbaar via een extern ip-adres weergegeven. 

Als u toegang wilt krijgen tot `istioctl dashboard` de gebruikersinterfaces voor invoegtoepassing, gebruikt u de opdracht. Deze opdracht maakt gebruik van [kubectl port-forward][kubectl-port-forward] en een willekeurige poort om een veilige verbinding te maken tussen uw clientmachine en de relevante pod in uw AKS-cluster. Vervolgens wordt de add-on webapplicatie automatisch geopend in uw standaardbrowser.

We hebben een extra beveiligingslaag voor Grafana en Kiali toegevoegd door eerder in dit artikel referenties voor hen op te geven.

### <a name="grafana"></a>Grafana

De analytics en monitoring dashboards voor Istio worden geleverd door [Grafana][grafana]. Vergeet niet om de referenties die u hebt gemaakt via de Grafana geheim eerder te gebruiken wanneer gevraagd. Open het Grafana-dashboard als volgt:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Metrics voor Istio worden geleverd door [Prometheus][prometheus]. Open het Prometheus-dashboard als volgt:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Tracing binnen Istio wordt verzorgd door [Jaeger.][jaeger] Open het Jaeger-dashboard als volgt:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali Kiali

Kiali biedt een dashboard [Kiali][kiali]voor het waarnemen van servicemesh. Vergeet niet om de referenties die u hebt gemaakt via de Kiali geheim eerder te gebruiken wanneer gevraagd. Open het Kiali-dashboard als volgt:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Een eenvoudige interface met de [Envoy][envoy] volmachten is beschikbaar. Het biedt configuratie-informatie en statistieken voor een Envoy-proxy die wordt uitgevoerd in een bepaalde pod. Open de Envoy-interface als volgt:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Istio van AKS verwijderen

> [!WARNING]
> Het verwijderen van Istio uit een lopend systeem kan leiden tot verkeersgerelateerde problemen tussen uw services. Zorg ervoor dat u voorzieningen hebt getroffen voor uw systeem om nog steeds correct te werken zonder Istio voordat u verder gaat.

### <a name="remove-istio-components-and-namespace"></a>Istio-componenten en naamruimte verwijderen

Als u Istio uit uw AKS-cluster wilt verwijderen, gebruikt u de `istioctl manifest generate` opdracht met het `istio.aks.yaml` vliegtuigspecificatiebestand istio. Dit zal het geïmplementeerde manifest genereren, `kubectl delete` waar we naar zullen leiden `istio-system` om alle geïnstalleerde componenten en de naamruimte te verwijderen.

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Istio-CRD's en geheimen verwijderen

De bovenstaande commando's verwijderen alle Istio componenten en naamruimte, maar we zijn nog steeds links met gegenereerde Istio geheimen. 

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

In de volgende documentatie wordt beschreven hoe u Istio gebruiken om intelligente routering te bieden om een canary-release uit te rollen:

> [!div class="nextstepaction"]
> [AKS Istio intelligent routeringscenario][istio-scenario-routing]

Zie de volgende officiële Istio-richtlijnen voor meer installatie- en configuratieopties voor Istio:

- [Istio - installatiegidsen][istio-installation-guides]

U ook aanvullende scenario's volgen met:

- [Voorbeeld van istio Bookinfo-toepassing][istio-bookinfo-example]

Zie de volgende Azure Monitor-documentatie voor meer informatie over het controleren van uw AKS-toepassing met Application Insights en Istio:

- [Zero instrumentation application monitoring for Kubernetes hosted applications Zero instrumentation application monitoring for Kubernetes hosted applications Zero instrumentation application monitoring for Kubernetes hosted applications Zero instrumentation][app-insights]

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
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
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
