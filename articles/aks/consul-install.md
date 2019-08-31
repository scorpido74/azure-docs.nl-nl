---
title: Hashicorp consul installeren in azure Kubernetes service (AKS)
description: Meer informatie over het installeren en gebruiken van consul voor het maken van een service-net in een Azure Kubernetes service-cluster (AKS)
services: container-service
author: dstrebel
ms.service: container-service
ms.topic: article
ms.date: 06/19/2019
ms.author: dastrebe
ms.openlocfilehash: 7acd2533079499091427c7e63741b9c587ee29e5
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70189222"
---
# <a name="install-and-use-consul-connect-in-azure-kubernetes-service-aks"></a>Consul Connect installeren en gebruiken in azure Kubernetes service (AKS)

[Consul][consul-github] is een open-source service-net dat een belang rijke set functionaliteit biedt voor de micro Services in een Kubernetes-cluster. Deze functies omvatten service detectie, status controle, service segmentatie en waarneembaarheid. Zie voor meer informatie over consul de officiële [What is consul?][consul-docs-concepts] -documentatie.

In dit artikel wordt beschreven hoe u consul installeert. De consul-onderdelen worden in een Kubernetes-cluster op AKS geïnstalleerd.

> [!NOTE]
> Deze instructies verwijzen naar consul `1.5`-versie.
>
> De consul `1.5.x` -releases zijn getest door het Hashicorp-team op Kubernetes `1.12`- `1.14`versies `1.14`,,. U kunt aanvullende consul-versies vinden op [github-consul releases][consul-github-releases] en informatie over elk van de releases op [consul-release opmerkingen][consul-release-notes].

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * De consul-onderdelen installeren op AKS
> * De consul-installatie valideren
> * Consul verwijderen uit AKS

## <a name="before-you-begin"></a>Voordat u begint

Voor de stappen in dit artikel wordt ervan uitgegaan dat u een AKS-cluster `1.11` hebt gemaakt (Kubernetes en hoger, met RBAC ingeschakeld) `kubectl` en een verbinding met het cluster tot stand hebt gebracht. Als u hulp nodig hebt bij een van deze items, raadpleegt u de [AKS Quick][aks-quickstart]start.

U hebt [helm][helm] nodig om deze instructies te volgen en consul te installeren. Het is raadzaam dat u versie `2.12.2` of hoger hebt geïnstalleerd en geconfigureerd in uw cluster. Als u hulp nodig hebt bij het installeren van helm, raadpleegt u de [AKS helm Installation guidance][helm-install](Engelstalig). Alle consul-peulen moeten ook worden gepland om te worden uitgevoerd op Linux-knoop punten.

In dit artikel worden de consul-installatie richtlijnen gescheiden in verschillende afzonderlijke stappen. Het eind resultaat is hetzelfde als de officiële consul-installatie [richtlijnen][consul-install-k8].

### <a name="install-the-consul-components-on-aks"></a>De consul-onderdelen installeren op AKS

Eerst worden de officiële HashiCorp-consul op Kubernetes GitHub opslag plaats gekloond.

```bash
git clone https://github.com/hashicorp/consul-helm.git
cd consul-helm
```

Vervolgens moet er een aangepast helm-waarden bestand worden gemaakt voor de consul-installatie. Maak het volgende bestand met aangepaste waarden voordat u consul installeert.

```bash
vim consul-values.yaml
```

Kopieer en plak de volgende waarden in het consul-values. yaml-bestand.

```yaml
# Sets datacenter name and version Consul to use
global:
  datacenter: dc1
  image: "consul:1.5.2"

# Enables proxies to be injected into pods
connectInject:
  enabled: true

# Enables UI on ClusterIP
ui:
  service:
    type: "ClusterIP"

# Enables GRCP that is required for connectInject
client:
  enabled: true
  grpc: true

# Sets replicase to 3 for HA
server:
  replicas: 3
  bootstrapExpect: 1
  disruptionBudget:
    enabled: true
    maxUnavailable: 1

# Syncs Kubernetes service discovery with Consul
syncCatalog:
  enabled: true
```

Nu we het bestand met aangepaste waarden hebben, kunnen we consul in het AKS-cluster installeren

Bash

```bash
helm install -f consul-values.yaml --name consul --namespace consul .
```
Het `Consul` helm-diagram implementeert een aantal objecten. U kunt de lijst zien in de uitvoer van `helm install` de bovenstaande opdracht. De implementatie van de consul-onderdelen kan vier tot vijf minuten duren, afhankelijk van uw cluster omgeving.

> [!NOTE]
> Alle consul-peulen moeten worden gepland om te worden uitgevoerd op Linux-knoop punten. Als u naast Linux-knooppunt Pools in uw cluster ook Windows Server-knooppunt groepen hebt, moet u controleren of alle consul van het hele micro maal zijn gepland voor uitvoering op Linux-knoop punten.

Op dit moment hebt u consul geïmplementeerd op uw AKS-cluster. Om ervoor te zorgen dat we een geslaagde implementatie van consul hebben, gaan we verder met de volgende sectie om de consul-installatie te valideren.

## <a name="validate-the-consul-installation"></a>De consul-installatie valideren

Controleer eerst of de verwachte services zijn gemaakt. Gebruik de opdracht [kubectl Get SVC][kubectl-get] om de actieve services weer te geven. Voer een `consul` query uit op de naam ruimte, waarbij de consul `consul` -onderdelen zijn geïnstalleerd door de helm-grafiek:

```console
kubectl get svc --namespace consul
```

In de volgende voorbeeld uitvoer ziet u de services die nu moeten worden uitgevoerd:

```console
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP             PORT(S)                                                                   AGE
consul                               ExternalName   <none>         consul.service.consul   <none>                                                                    38m
consul-consul-connect-injector-svc   ClusterIP      10.0.89.113    <none>                  443/TCP                                                                   40m
consul-consul-dns                    ClusterIP      10.0.166.82    <none>                  53/TCP,53/UDP                                                             40m
consul-consul-server                 ClusterIP      None           <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   40m
consul-consul-ui                     ClusterIP      10.0.117.164   <none>                  80/TCP                                                                    40m
```

Controleer vervolgens of het vereiste peul is gemaakt. Gebruik de opdracht [kubectl Get peul][kubectl-get] en voer een query uit `consul` op de naam ruimte:

```console
kubectl get pods --namespace consul
```

In de volgende voorbeeld uitvoer ziet u de peulen die worden uitgevoerd:

```console
NAME                                                              READY   STATUS    RESTARTS   AGE
consul-consul-7cc9v                                               1/1     Running   0          37m
consul-consul-7klg7                                               1/1     Running   0          37m
consul-consul-connect-injector-webhook-deployment-57f88df8hgmfs   1/1     Running   0          37m
consul-consul-lq8qb                                               1/1     Running   0          37m
consul-consul-server-0                                            1/1     Running   0          37m
consul-consul-server-1                                            1/1     Running   0          36m
consul-consul-server-2                                            1/1     Running   0          36m
consul-consul-sync-catalog-7cf7d5bfff-jjbjv                       1/1     Running   2          37m
```

 Alle peulen moeten de status van `Running`hebben. Als uw peul niet over deze status beschikt, moet u een paar minuten wachten totdat dit het geval is. Als een van de peulen een probleem rapporteert, gebruikt u de [kubectl pod][kubectl-describe] opdracht om de uitvoer en status te controleren.

## <a name="accessing-the-consul-ui"></a>Toegang tot de consul-gebruikers interface

De consul-gebruikers interface is geïnstalleerd in onze installatie hierboven die configuratie op basis van de gebruikers interface biedt voor consul. De gebruikers interface voor consul wordt niet openbaar weer gegeven via een extern IP-adres. Als u toegang wilt krijgen tot de gebruikers interfaces van de invoeg toepassing, gebruikt u de opdracht [kubectl-poort-door sturen][kubectl-port-forward] . Met deze opdracht maakt u een beveiligde verbinding tussen uw client computer en de relevante pod in uw AKS-cluster.

```bash
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```
U kunt nu een browser openen en deze `http://localhost:8080/ui` aanwijzen om de consul-gebruikers interface te openen. Wanneer u de gebruikers interface opent, ziet u het volgende:

![Consul-gebruikers interface](./media/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Consul verwijderen uit AKS

> [!WARNING]
> Het verwijderen van consul van een actief systeem kan leiden tot problemen met verkeer tussen uw services. Zorg ervoor dat u voor het systeem nog steeds goed werkt zonder consul voordat u doorgaat.

### <a name="remove-consul-components-and-namespace"></a>Consul-onderdelen en naam ruimte verwijderen

Gebruik de volgende opdrachten om consul te verwijderen uit uw AKS-cluster. Met `helm delete` de opdrachten wordt de `consul` grafiek verwijderd en wordt `kubectl delete ns` de `consul` naam ruimte verwijderd met de opdracht.

```bash
helm delete --purge consul
kubectl delete ns consul
```

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over de installatie-en configuratie opties voor consul, raadpleegt u de volgende officiële consul-artikelen:

- [Installatie handleiding voor consul-helm][consul-install-k8]
- [Installatie opties voor consul-helm][consul-install-helm-options]

U kunt ook aanvullende scenario's volgen met behulp van de [consul-voorbeeld toepassing][consul-app-example].

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

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
