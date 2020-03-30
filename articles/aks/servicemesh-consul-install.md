---
title: Consul installeren in Azure Kubernetes Service (AKS)
description: Meer informatie over het installeren en gebruiken van Consul om een servicemesh te maken in een AKS-cluster (Azure Kubernetes Service)
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 1601ab6d81b888fd2247e95f22c58e1fc91df698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273738"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Consul installeren en gebruiken in Azure Kubernetes Service (AKS)

[Consul][consul-github] is een open-source service mesh die een belangrijke set functionaliteit biedt voor de microservices in een Kubernetes-cluster. Deze functies omvatten servicedetectie, statuscontrole, servicesegmentatie en waarneembaarheid. Zie voor meer informatie over consul de officiële [Wat is Consul?][consul-docs-concepts] documentatie.

In dit artikel ziet u hoe u Consul installeert. De Consul-componenten worden geïnstalleerd in een Kubernetes-cluster op AKS.

> [!NOTE]
> Deze instructies verwijzen `1.6.0`consul versie, en `2.14.2`gebruik ten minste Helm versie .
>
> De `1.6.x` Consul-releases kunnen worden `1.13+`uitgevoerd tegen Kubernetes-versies. U vindt aanvullende Consul-versies op [GitHub - Consul Releases][consul-github-releases] en informatie over elk van de releases op [Consul- Release Notes][consul-release-notes].

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Installeer de Consul-componenten op AKS
> * De Consul-installatie valideren
> * Consul van AKS verwijderen

## <a name="before-you-begin"></a>Voordat u begint

De stappen in dit artikel gaan ervan uit dat `1.13` u een AKS-cluster hebt gemaakt (Kubernetes en hoger, met RBAC ingeschakeld) en een `kubectl` verbinding met het cluster hebt gemaakt. Als u hulp nodig hebt met een van deze items, zie dan de [AKS snelstart][aks-quickstart]. Zorg ervoor dat uw cluster ten minste 3 knooppunten in de Linux-knooppuntpool heeft.

Je hebt [Helm][helm] nodig om deze instructies op te volgen en Consul te installeren. Het wordt aanbevolen dat u de nieuwste stabiele versie correct hebt geïnstalleerd en geconfigureerd in uw cluster. Als u hulp nodig hebt bij het installeren van Helm, raadpleegt u de [AKS Helm installatiebegeleiding.][helm-install] Alle Consul pods moeten ook worden gepland om te draaien op Linux-knooppunten.

Dit artikel scheidt de consul installatiebegeleiding in verscheidene afzonderlijke stappen. Het eindresultaat is hetzelfde in structuur als de officiële Consul installatie [begeleiding][consul-install-k8].

### <a name="install-the-consul-components-on-aks"></a>Installeer de Consul-componenten op AKS

We beginnen met het `v0.10.0` downloaden van de versie van de Consul Helm grafiek. Deze versie van de `1.6.0`grafiek bevat Consul versie .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Gebruik Helm en `consul-helm` de gedownloade grafiek om `consul` de Consul-componenten te installeren in de naamruimte in uw AKS-cluster. 

> [!NOTE]
> **Installatieopties**
> 
> We gebruiken de volgende opties als onderdeel van onze installatie:
> - `connectInject.enabled=true`- proxies in te injecteren in peulen
> - `client.enabled=true`- consulcliënten in staat stellen om op elk knooppunt te draaien
> - `client.grpc=true`- gRPC-listener inschakelen voor connectInject
> - `syncCatalog.enabled=true`- kubernetes- en consul-services synchroniseren
>
> **Knooppuntselecties**
>
> Consul moet momenteel worden gepland om te draaien op Linux-knooppunten. Als u Windows Server-knooppunten in uw cluster hebt, moet u ervoor zorgen dat de Consul-pods alleen op Linux-knooppunten worden uitgevoerd. We gebruiken [knooppuntselecties][kubernetes-node-selectors] om ervoor te zorgen dat pods zijn gepland op de juiste knooppunten.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

In `Consul` de grafiek Helm worden een aantal objecten geïmplementeerd. U de lijst zien `helm install` in de uitvoer van uw opdracht hierboven. De implementatie van de Consul-componenten kan ongeveer 3 minuten in beslag nemen, afhankelijk van uw clusteromgeving.

Op dit moment hebt u Consul ingezet op uw AKS-cluster. Om ervoor te zorgen dat we een succesvolle inzet van Consul hebben, gaan we naar de volgende sectie om de consul-installatie te valideren.

## <a name="validate-the-consul-installation"></a>De Consul-installatie valideren

Controleer of de resources zijn gemaakt. Gebruik de [kubectl get svc][kubectl-get] en [kubectl][kubectl-get] `consul` get pod-opdrachten om de naamruimte op te vragen, waar de Consul-componenten door de `helm install` opdracht zijn geïnstalleerd:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

In de volgende voorbeelduitvoer worden de services en pods weergegeven (gepland op Linux-knooppunten) die nu moeten worden uitgevoerd:

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

Alle pods moeten een status `Running`van . Als uw pods deze statussen niet hebben, wacht dan een minuut of twee tot ze dat doen. Als een pods een probleem melden, gebruikt u de opdracht [kubectl describe pod][kubectl-describe] om hun uitvoer en status te controleren.

## <a name="accessing-the-consul-ui"></a>Toegang tot de Consul UI

De Consul UI is geïnstalleerd in onze setup hierboven en biedt ui gebaseerde configuratie voor Consul. De gebruikersinterface voor consul wordt niet openbaar gemaakt via een extern ip-adres. Als u toegang wilt krijgen tot de gebruikersinterface van Consul, gebruikt u de opdracht [kubectl port forward.][kubectl-port-forward] Met deze opdracht wordt een beveiligde verbinding gemaakt tussen uw clientmachine en de relevante pod in uw AKS-cluster.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

U nu een browser `http://localhost:8080/ui` openen en deze aanwijzen om de Consul UI te openen. U ziet het volgende wanneer u de gebruikersinterface opent:

![Consul UI](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Consul van AKS verwijderen

> [!WARNING]
> Het verwijderen van Consul uit een lopend systeem kan leiden tot verkeersgerelateerde problemen tussen uw services. Zorg ervoor dat u voorzieningen hebt getroffen om uw systeem nog steeds correct te laten functioneren zonder Consul voordat u verdergaat.

### <a name="remove-consul-components-and-namespace"></a>Consul-onderdelen en naamruimte verwijderen

Als u Consul uit uw AKS-cluster wilt verwijderen, gebruikt u de volgende opdrachten. De `helm delete` opdrachten verwijderen `consul` de grafiek `kubectl delete namespace` en de `consul` opdracht verwijdert de naamruimte.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende officiële consul-artikelen voor meer installatie- en configuratieopties voor Consul:

- [Consul - Helm installatiegids][consul-install-k8]
- [Consul - Helm installatiemogelijkheden][consul-install-helm-options]

U ook aanvullende scenario's volgen met:

- [Consul Voorbeeld aanvraag][consul-app-example]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
