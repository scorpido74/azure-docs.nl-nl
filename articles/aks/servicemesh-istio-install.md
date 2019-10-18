---
title: Istio installeren in azure Kubernetes service (AKS)
description: Meer informatie over het installeren en gebruiken van Istio voor het maken van een service-net in een Azure Kubernetes service-cluster (AKS)
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 9c9dcd567b8632626bf4b1f0bf2ef6b5e69b8a9d
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530444"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Istio installeren en gebruiken in azure Kubernetes service (AKS)

[Istio][istio-github] is een open-source service-net dat een belang rijke set functionaliteit biedt voor de micro Services in een Kubernetes-cluster. Deze functies omvatten verkeer beheer, service-identiteit en-beveiliging, afdwinging van beleid en de bekrachtiging. Zie voor meer informatie over Istio de officiële [What is Istio?][istio-docs-concepts] -documentatie.

In dit artikel wordt beschreven hoe u Istio installeert. De Istio `istioctl` binaire client wordt geïnstalleerd op de client computer en de Istio-onderdelen worden geïnstalleerd in een Kubernetes-cluster op AKS.

> [!NOTE]
> Deze instructies verwijzen naar Istio versie `1.3.2` en gebruiken ten minste helm versie `2.14.2`.
>
> De Istio-`1.3.x` releases zijn getest door het Istio-team tegen Kubernetes-versies `1.13`, `1.14` `1.15`. U kunt aanvullende Istio-versies vinden op [github-Istio-releases][istio-github-releases], informatie over elk van de releases van [Istio News][istio-release-notes] en ondersteunde Kubernetes-versies op [Istio algemene veelgestelde vragen][istio-faq].

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Down load en installeer het binaire bestand voor de Istio istioctl-client
> * Istio installeren op AKS
> * De Istio-installatie valideren
> * Toegang tot de invoeg toepassingen
> * Istio verwijderen uit AKS

## <a name="before-you-begin"></a>Voordat u begint

Voor de stappen die in dit artikel worden beschreven, wordt ervan uitgegaan dat u een AKS-cluster hebt gemaakt (Kubernetes `1.13` en hoger, waarbij RBAC is ingeschakeld) en een `kubectl`-verbinding met het cluster heeft gemaakt. Als u hulp nodig hebt bij een van deze items, raadpleegt u de [AKS Quick][aks-quickstart]start.

U hebt [helm][helm] nodig om deze instructies te volgen en Istio te installeren. Het is raadzaam dat u de nieuwste stabiele versie correct hebt geïnstalleerd en geconfigureerd in uw cluster. Als u hulp nodig hebt bij het installeren van helm, raadpleegt u de [AKS helm Installation guidance][helm-install](Engelstalig). Alle Istio-peulen moeten ook worden gepland om te worden uitgevoerd op Linux-knoop punten.

Zorg ervoor dat u de Istio-documentatie over [prestaties en schaal baarheid](https://istio.io/docs/concepts/performance-and-scalability/) hebt gelezen om inzicht te krijgen in de aanvullende bron vereisten voor het uitvoeren van Istio in uw AKS-cluster. De kern-en geheugen vereisten variëren op basis van uw specifieke werk belasting. Kies het juiste aantal knoop punten en de VM-grootte voor uw installatie.

In dit artikel worden de Istio-installatie richtlijnen gescheiden in verschillende afzonderlijke stappen. Het eind resultaat is hetzelfde als de officiële Istio-installatie [richtlijnen][istio-install-helm].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="add-the-istio-helm-chart-repository"></a>De Istio helm-grafiek opslagplaats toevoegen

Voeg de Istio helm-grafiek opslagplaats toe voor de Istio-release. Zorg ervoor dat u de `helm repo update` uitvoert om uw lokale gegevens voor de grafiek opslagplaats bij te werken.

```azurecli
helm repo add istio.io https://storage.googleapis.com/istio-release/releases/$ISTIO_VERSION/charts/
helm repo update
```

## <a name="install-the-istio-crds-on-aks"></a>Installeer de Istio-CRDs op AKS

Istio maakt gebruik van [aangepaste resource definities (CRDs)][kubernetes-crd] voor het beheren van de runtime configuratie. De Istio CRDs moet eerst worden geïnstalleerd, omdat de Istio-onderdelen hiervan afhankelijk zijn. Gebruik helm en de `istio-init` grafiek om de Istio-CRDs te installeren in de `istio-system` naam ruimte in uw AKS-cluster:

```azurecli
helm install istio.io/istio-init --name istio-init --namespace istio-system
```

[Taken][kubernetes-jobs] worden geïmplementeerd als onderdeel van de `istio-init` helm-grafiek om de CRDs te installeren. Het volt ooien van deze taken moet minder dan 20s duren, afhankelijk van uw cluster omgeving. U kunt als volgt controleren of de taken zijn voltooid:

```azurecli
kubectl get jobs -n istio-system
```

In de volgende voorbeeld uitvoer ziet u de voltooide taken.

```console
NAME                      COMPLETIONS   DURATION   AGE
istio-init-crd-10-1.3.2   1/1           14s        14s
istio-init-crd-11-1.3.2   1/1           12s        14s
istio-init-crd-12-1.3.2   1/1           14s        14s
```

Nu we hebben bevestigd dat de taken zijn voltooid, gaan we controleren of het juiste aantal Istio CRDs is geïnstalleerd. U kunt controleren of alle 23 Istio CRDs zijn geïnstalleerd door de volgende opdracht uit te voeren. De opdracht moet het getal `23` retour neren.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - check CRD count](includes/servicemesh/istio/install-check-crd-count-powershell.md)]

::: zone-end

Als u op dit punt hebt geklikt, betekent dit dat de Istio CRDs is geïnstalleerd. Ga nu verder met de volgende sectie om [de Istio-onderdelen op AKS te installeren](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>De Istio-onderdelen installeren op AKS

[Grafana][grafana] en [Kiali][kiali] worden geïnstalleerd als onderdeel van onze Istio-installatie. Grafana biedt analyse-en bewakings dashboards en Kiali biedt een service-net-waarneembaar dash board. In de installatie zijn voor elk van deze onderdelen referenties vereist die als [geheim][kubernetes-secrets]moeten worden verschaft.

Voordat we de Istio-onderdelen kunnen installeren, moeten we de geheimen voor Grafana en Kiali maken. 

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

Nu we de Grafana-en Kiali-geheimen in het AKS-cluster hebben gemaakt, is het tijd om de Istio-onderdelen te installeren. Gebruik helm en de `istio` grafiek om de Istio-onderdelen te installeren in de naam ruimte `istio-system` in uw AKS-cluster. 

> [!NOTE]
> **Installatie opties**
> 
> We gebruiken de volgende opties als onderdeel van onze installatie:
> - `global.controlPlaneSecurityEnabled=true`-wederzijdse TLS ingeschakeld voor het besturings vlak
> - `global.mtls.enabled=true`-vereisen dat alle service-to-service-communicatie mtls
> - `grafana.enabled=true`-Grafana-implementatie inschakelen voor analyse-en bewakings dashboards
> - `grafana.security.enabled=true`-verificatie inschakelen voor Grafana
> - `tracing.enabled=true`-de Jaeger-implementatie voor tracering inschakelen
> - `kiali.enabled=true`-de Kiali-implementatie inschakelen voor een dash board met Service-Mesh
>
> **Knooppunt selecties**
>
> Istio moet momenteel worden ingepland om te worden uitgevoerd op Linux-knoop punten. Als u Windows Server-knoop punten in uw cluster hebt, moet u ervoor zorgen dat de Istio van de peulen enkel worden uitgevoerd op Linux-knoop punten. We gebruiken [knooppunt selecties][kubernetes-node-selectors] om ervoor te zorgen dat de juiste knoop punten van peulen worden gepland.

> [!CAUTION]
> De [SDS-functies (service voor geheime detectie)][istio-feature-sds] en [Istio cni][istio-feature-cni] Istio zijn op dit moment in [alpha][istio-feature-stages]. u moet er dus rekening mee doen voordat u deze inschakelt. Daarnaast is de functie voor volume projectie Kubernetes van het [Service account][kubernetes-feature-sa-projected-volume] (een vereiste voor SDS) niet ingeschakeld in huidige AKS-versies.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/istio/install-components-powershell.md)]

::: zone-end

In het helm-diagram van `istio` wordt een groot aantal objecten geïmplementeerd. U kunt de lijst weer geven uit de uitvoer van uw `helm install`-opdracht hierboven. Het volt ooien van de implementatie van de Istio-onderdelen duurt minder dan twee minuten, afhankelijk van uw cluster omgeving.

Op dit moment hebt u Istio geïmplementeerd op uw AKS-cluster. Om ervoor te zorgen dat we een geslaagde implementatie van Istio hebben, gaan we verder met de volgende sectie om [de Istio-installatie te valideren](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>De Istio-installatie valideren

Controleer eerst of de verwachte services zijn gemaakt. Gebruik de opdracht [kubectl Get SVC][kubectl-get] om de actieve services weer te geven. Voer een query uit op de naam ruimte `istio-system`, waarbij de Istio en invoeg toepassings onderdelen zijn geïnstalleerd door de `istio` helm-grafiek:

```console
kubectl get svc --namespace istio-system --output wide
```

In de volgende voorbeeld uitvoer ziet u de services die nu moeten worden uitgevoerd:

- `istio-*` Services
- `jaeger-*`, `tracing` en `zipkin` invoeg toepassing voor tracering van services
- service voor metrische gegevens van `prometheus` toevoegen
- dash board-service voor `grafana`-invoeg toepassingen en-bewaking
- dash board service voor de `kiali`-invoeg service

Als de `istio-ingressgateway` een extern IP-adres van `<pending>` bevat, wacht u enkele minuten totdat er een IP-adressen zijn toegewezen door Azure-netwerken.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.164.244   <none>           3000/TCP                                                                                                                                     53s   app=grafana
istio-citadel            ClusterIP      10.0.49.16     <none>           8060/TCP,15014/TCP                                                                                                                           53s   istio=citadel
istio-galley             ClusterIP      10.0.175.173   <none>           443/TCP,15014/TCP,9901/TCP                                                                                                                   53s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.226.151   20.188.221.111   15020:31128/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:30817/TCP,15030:30436/TCP,15031:32485/TCP,15032:30980/TCP,15443:30124/TCP   53s   app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.102.158   <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       53s   istio=pilot
istio-policy             ClusterIP      10.0.234.53    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                                 53s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.216.8     <none>           443/TCP,15014/TCP                                                                                                                            53s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.154.215   <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       53s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                                   52s   app=jaeger
jaeger-collector         ClusterIP      10.0.26.109    <none>           14267/TCP,14268/TCP                                                                                                                          52s   app=jaeger
jaeger-query             ClusterIP      10.0.70.55     <none>           16686/TCP                                                                                                                                    52s   app=jaeger
kiali                    ClusterIP      10.0.36.206    <none>           20001/TCP                                                                                                                                    53s   app=kiali
prometheus               ClusterIP      10.0.236.99    <none>           9090/TCP                                                                                                                                     53s   app=prometheus
tracing                  ClusterIP      10.0.83.152    <none>           80/TCP                                                                                                                                       52s   app=jaeger
zipkin                   ClusterIP      10.0.25.86     <none>           9411/TCP                                                                                                                                     52s   app=jaeger
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
NAME                                     READY   STATUS      RESTARTS   AGE
grafana-7c48555456-msl7b                 1/1     Running     0          88s
istio-citadel-566fc66db7-m8wgl           1/1     Running     0          87s
istio-galley-5746db8d56-pl5gg            1/1     Running     0          88s
istio-ingressgateway-6c94f7c9bf-f5lt5    1/1     Running     0          88s
istio-init-crd-10-1.3.2-xw9g2            0/1     Completed   0          92m
istio-init-crd-11-1.3.2-54rz8            0/1     Completed   0          92m
istio-init-crd-12-1.3.2-789qj            0/1     Completed   0          92m
istio-pilot-6748968b6d-rvdfx             2/2     Running     0          87s
istio-policy-7576bbbcf7-2stft            2/2     Running     0          87s
istio-sidecar-injector-76d79d494-7jk9n   1/1     Running     0          87s
istio-telemetry-74b7bf676d-tfrcl         2/2     Running     0          88s
istio-tracing-655d9588bc-d2htg           1/1     Running     0          86s
kiali-65d55bcfb8-tqrfk                   1/1     Running     0          88s
prometheus-846f9849bd-br8kp              1/1     Running     0          87s
```

Er moeten drie `istio-init-crd-*` een Peul met een `Completed` status. Deze peulen zijn verantwoordelijk voor het uitvoeren van de taken die de CRDs in een eerdere stap hebben gemaakt. Alle andere peulen moeten de status van `Running` weer geven. Als uw peul niet over deze status beschikt, moet u een paar minuten wachten totdat dit het geval is. Als een van de peulen een probleem rapporteert, gebruikt u de [kubectl pod][kubectl-describe] opdracht om de uitvoer en status te controleren.

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

Gebruik de volgende opdrachten om Istio te verwijderen uit uw AKS-cluster. Met de `helm delete`-opdrachten worden de `istio` en `istio-init` grafieken verwijderd en met de opdracht `kubectl delete namespace` wordt de `istio-system` naam ruimte verwijderd.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete namespace istio-system
```

### <a name="remove-istio-crds-and-secrets"></a>Istio CRDs en geheimen verwijderen

Met de bovenstaande opdrachten worden alle Istio-onderdelen en-naam ruimten verwijderd, maar er zijn nog steeds de Istio-CRDs en-geheimen actief. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Volgende stappen

In de volgende documentatie wordt beschreven hoe u Istio kunt gebruiken om intelligente route ring te bieden voor het implementeren van een distributie release:

> [!div class="nextstepaction"]
> [AKS Istio intelligent routerings scenario][istio-scenario-routing]

Als u meer wilt weten over de installatie-en configuratie opties voor Istio, raadpleegt u de volgende officiële Istio-artikelen:

- [Installatie handleiding voor Istio-helm][istio-install-helm]
- [Installatie opties voor Istio-helm][istio-install-helm-options]

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
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/en-us/azure/aks/concepts-clusters-workloads#node-selectors
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
[helm-install]: ./kubernetes-helm.md
