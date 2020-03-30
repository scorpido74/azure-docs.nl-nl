---
title: CoreDNS aanpassen voor Azure Kubernetes Service (AKS)
description: Meer informatie over het aanpassen van CoreDNS om subdomeinen toe te voegen of aangepaste DNS-eindpunten uit te breiden met Azure Kubernetes Service (AKS)
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: 78132a53313f4a8ee5c10af340c8dab08c3e42c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595821"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>CoreDNS aanpassen met Azure Kubernetes Service

Azure Kubernetes Service (AKS) maakt gebruik van het [CoreDNS-project][coredns] voor cluster DNS-beheer en -resolutie met alle *1.12.x* en hogere clusters. Voorheen werd het kube-dns-project gebruikt. Dit kube-dns project is nu afgeschaft. Voor meer informatie over CoreDNS maatwerk en Kubernetes, zie de [officiële upstream documentatie][corednsk8s].

Aangezien AKS een beheerde service is, u de hoofdconfiguratie voor CoreDNS (een *CoreFile)* niet wijzigen. In plaats daarvan gebruikt u een Kubernetes *ConfigMap* om de standaardinstellingen te overschrijven. Als u de standaard AKS CoreDNS `kubectl get configmaps --namespace=kube-system coredns -o yaml` ConfigMaps wilt bekijken, gebruikt u de opdracht.

In dit artikel ziet u hoe u ConfigMaps gebruiken voor basisaanpassingsopties van CoreDNS in AKS. Deze benadering verschilt van het configureren van CoreDNS in andere contexten, zoals het gebruik van het CoreFile. Controleer de versie van CoreDNS die u uitvoert, omdat de configuratiewaarden tussen versies kunnen veranderen.

> [!NOTE]
> `kube-dns`aangeboden verschillende [aanpassingsopties][kubednsblog] via een Kubernetes config kaart. CoreDNS is **niet** backwards compatible met kube-dns. Alle aanpassingen die u eerder hebt gebruikt, moeten worden bijgewerkt voor gebruik met CoreDNS.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

## <a name="what-is-supportedunsupported"></a>Wat wordt ondersteund/niet ondersteund

Alle ingebouwde CoreDNS-plug-ins worden ondersteund. Er worden geen plug-ins van derden ondersteund.

## <a name="rewrite-dns"></a>DNS herschrijven

Een scenario dat je hebt is het uitvoeren van on-the-fly DNS-naam herschrijft. Vervang in het `<domain to be written>` volgende voorbeeld uw eigen volledig gekwalificeerde domeinnaam. Maak een `corednsms.yaml` bestand met de naam en plak de volgende voorbeeldconfiguratie:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Maak de ConfigMap met de [kubectl apply configmap][kubectl-apply] opdracht en geef de naam van uw YAML manifest op:

```console
kubectl apply -f corednsms.yaml
```

Als u wilt controleren of de aanpassingen zijn toegepast, gebruikt u de [kubectl-configmaps][kubectl-get] en geeft u uw *coredns-aangepaste* ConfigMap op:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Dwing Nu CoreDNS om de ConfigMap opnieuw te laden. De [opdracht kubectl delete pod][kubectl delete] is niet destructief en veroorzaakt geen tijd voor downtime. De `kube-dns` pods worden verwijderd en de Kubernetes Scheduler maakt ze vervolgens opnieuw. Deze nieuwe pods bevatten de wijziging in ttl-waarde.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> De bovenstaande opdracht is correct. Terwijl we veranderen, `coredns`is de implementatie onder de **kube-dns** naam.

## <a name="custom-forward-server"></a>Aangepaste forwardserver

Als u een forward server voor uw netwerkverkeer moet opgeven, u een ConfigMap maken om DNS aan te passen. Werk in het volgende `forward` voorbeeld de naam en het adres bij met de waarden voor uw eigen omgeving. Maak een `corednsms.yaml` bestand met de naam en plak de volgende voorbeeldconfiguratie:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Net als in de vorige voorbeelden maakt u de ConfigMap met de [kubectl-opdracht configmap toepassen][kubectl-apply] en geeft u de naam van uw YAML-manifest op. Dwing CoreDNS vervolgens om de ConfigMap opnieuw te laden met behulp van de [kubectl delete pod][kubectl delete] voor de Kubernetes Scheduler om ze opnieuw te maken:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Aangepaste domeinen gebruiken

U aangepaste domeinen configureren die alleen intern kunnen worden opgelost. U bijvoorbeeld het aangepaste domein *puglife.local*oplossen, wat geen geldig domein op het hoogste niveau is. Zonder een aangepaste domeinConfigMap kan het AKS-cluster het adres niet oplossen.

Werk in het volgende voorbeeld het aangepaste domein en IP-adres bij om verkeer naar de waarden voor uw eigen omgeving te leiden. Maak een `corednsms.yaml` bestand met de naam en plak de volgende voorbeeldconfiguratie:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Net als in de vorige voorbeelden maakt u de ConfigMap met de [kubectl-opdracht configmap toepassen][kubectl-apply] en geeft u de naam van uw YAML-manifest op. Dwing CoreDNS vervolgens om de ConfigMap opnieuw te laden met behulp van de [kubectl delete pod][kubectl delete] voor de Kubernetes Scheduler om ze opnieuw te maken:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Strookdomeinen

CoreDNS kan ook worden gebruikt om stub-domeinen te configureren. Werk in het volgende voorbeeld de aangepaste domeinen en IP-adressen bij met de waarden voor uw eigen omgeving. Maak een `corednsms.yaml` bestand met de naam en plak de volgende voorbeeldconfiguratie:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Net als in de vorige voorbeelden maakt u de ConfigMap met de [kubectl-opdracht configmap toepassen][kubectl-apply] en geeft u de naam van uw YAML-manifest op. Dwing CoreDNS vervolgens om de ConfigMap opnieuw te laden met behulp van de [kubectl delete pod][kubectl delete] voor de Kubernetes Scheduler om ze opnieuw te maken:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Hosts-plug-in

Aangezien alle ingebouwde plug-ins worden ondersteund, [Hosts][coredns hosts] betekent dit dat de CoreDNS Hosts-plug-in ook beschikbaar is om aan te passen:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>Logboekregistratie inschakelen voor het debuggen van DNS-query's 

Als u DNS-querylogboekregistratie wilt inschakelen, past u de volgende configuratie toe in uw configMap die op coredns is aangepast:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: |
        log
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel worden enkele voorbeeldscenario's voor CoreDNS-aanpassing getoond. Zie [de projectpagina coreDNS upstream-project][coredns]voor informatie over het CoreDNS-project.

Zie [Netwerkconcepten voor toepassingen in AKS voor][concepts-network]meer informatie over kernnetwerkconcepten.

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
