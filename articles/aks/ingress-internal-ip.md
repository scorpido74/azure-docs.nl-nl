---
title: Een invallende controller maken voor een intern netwerk in Azure Kubernetes Service (AKS)
description: Meer informatie over het installeren en configureren van een NGINX-ingress-controller voor een intern privénetwerk in een AKS-cluster (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 8c3eeaf2f9a92f1be9c691091d8e33d09a60b22d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595651"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Een invallende controller maken voor een intern virtueel netwerk in Azure Kubernetes Service (AKS)

Een controller voor inkomend verkeer is een stukje software dat omgekeerde proxy’s, configureerbare verkeersroutering en TLS-beëindiging voor Kubernetes-services biedt. Kubernetes-resources voor inkomend verkeer worden gebruikt om de regels en routes voor uitgaand verkeer worden geconfigureerd voor individuele Kubernetes-services. Met behulp van een controller en regels voor inkomend verkeer kan er één enkel IP-adres worden gebruikt voor het routeren van verkeer naar meerdere services in een Kubernetes-cluster.

In dit artikel ziet u hoe u de [NGINX-ingress-controller implementeert][nginx-ingress] in een AKS-cluster (Azure Kubernetes Service). De ingress-controller is geconfigureerd op een intern, privé virtueel netwerk en IP-adres. Externe toegang is niet toegestaan. Twee toepassingen worden vervolgens uitgevoerd in het AKS-cluster, die elk toegankelijk zijn via het enkele IP-adres.

U kunt ook het volgende doen:

- [Een basis-ingress-controller maken met externe netwerkconnectiviteit][aks-ingress-basic]
- [De invoegtoepassing HTTP-toepassingsroutering inschakelen][aks-http-app-routing]
- [Een ingress-controller maken die uw eigen TLS-certificaten gebruikt][aks-ingress-own-tls]
- Een invallende controller maken die Let's Encrypt gebruikt om automatisch TLS-certificaten te genereren [met een dynamisch openbaar IP-adres][aks-ingress-tls] of met een statisch openbaar [IP-adres][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt Helm gebruikt om de NGINX-ingress-controller, cert-manager en een voorbeeldweb-app te installeren. U moet Helm laten initialiseren binnen uw AKS-cluster en een serviceaccount voor Tiller gebruiken. Zie [Toepassingen installeren met Helm in Azure Kubernetes Service (AKS) voor][use-helm]meer informatie over het configureren en gebruiken van Helm.

In dit artikel moet u ook de Azure CLI-versie 2.0.64 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Een invallende controller maken

Standaard wordt een NGINX-ingress-controller gemaakt met een dynamische openbare IP-adrestoewijzing. Een veelvoorkomende configuratievereiste is het gebruik van een intern, privénetwerk en IP-adres. Met deze aanpak u de toegang tot uw services beperken tot interne gebruikers, zonder externe toegang.

Maak een bestand met de naam *internal-ingress.yaml met de naam internal-ingress.yaml* met behulp van het volgende voorbeeldmanifestbestand. In dit voorbeeld wordt *10.240.0.42* aan de *loadBalancerIP-bron* toegeten. Geef uw eigen interne IP-adres op voor gebruik met de invallende controller. Zorg ervoor dat dit IP-adres nog niet in gebruik is binnen uw virtuele netwerk.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Implementeer nu de *nginx-ingress* grafiek met Helm. Als u het manifestbestand wilt gebruiken `-f internal-ingress.yaml` dat in de vorige stap is gemaakt, voegt u de parameter toe. Voor toegevoegde redundantie worden er twee replica's van de NGINX-ingangscontrollers geïmplementeerd met de parameter `--set controller.replicaCount`. Als u optimaal wilt profiteren van het uitvoeren van replica's van de invallende controller, moet u ervoor zorgen dat er meer dan één knooppunt in uw AKS-cluster is.

De ingangscontroller moet ook worden gepland op een Linux-knooppunt. Windows Server-knooppunten (momenteel in preview in AKS) mogen de invallende controller niet uitvoeren. Er wordt een knooppuntselector opgegeven met behulp van de parameter `--set nodeSelector` om de Kubernetes-planner te laten weten dat de NGINX-ingangscontroller moet worden uitgevoerd op een Linux-knooppunt.

> [!TIP]
> In het volgende voorbeeld wordt een Kubernetes-naamruimte voor de invallende resources met de naam *ingress-basic.* Geef indien nodig een naamruimte op voor uw eigen omgeving. Als uw AKS-cluster niet is `--set rbac.create=false` ingeschakeld, voegt u de opdrachten Helm toe.

> [!TIP]
> Als u [IP-behoud van clientbron][client-source-ip] wilt inschakelen voor `--set controller.service.externalTrafficPolicy=Local` aanvragen voor containers in uw cluster, voegt u de installatieopdracht Helm toe. Het IP-adres van de clientbron wordt opgeslagen in de aanvraagkoptekst onder *X-Forwarded-For*. Wanneer een inbinnendringen-controller wordt gebruikt waarbij clientsource IP-behoud is ingeschakeld, werkt SSL-doorgeefservice niet.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Wanneer de Kubernetes load balancer-service wordt gemaakt voor de NGINX-ingress-controller, wordt uw interne IP-adres toegewezen, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Er zijn nog geen invallenregels gemaakt, dus de standaard 404-pagina van de NGINX-ingress-controller wordt weergegeven als u naar het interne IP-adres bladert. Invallenregels worden geconfigureerd in de volgende stappen.

## <a name="run-demo-applications"></a>Demotoepassingen uitvoeren

Als u de ingress-controller in actie wilt zien, laten we twee demotoepassingen uitvoeren in uw AKS-cluster. In dit voorbeeld wordt Helm gebruikt om twee exemplaren van een eenvoudige 'Hello world'-toepassing te implementeren.

Voordat u de voorbeeldgrafieken van helm installeren, voegt u de Azure-proefopslagplaats als volgt toe aan uw Helm-omgeving:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Maak de eerste demotoepassing vanuit een helmdiagram met de volgende opdracht:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Installeer nu een tweede exemplaar van de demo-applicatie. Voor de tweede instantie geeft u een nieuwe titel op, zodat de twee toepassingen visueel verschillend zijn. U geeft ook een unieke servicenaam op:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Een invallende route maken

Beide toepassingen worden nu uitgevoerd op uw Kubernetes-cluster. Als u verkeer naar elke toepassing wilt routeren, maakt u een Kubernetes-ingress-bron. De binnenkomende bron configureert de regels die verkeer doorsturen naar een van de twee toepassingen.

In het volgende voorbeeld wordt `http://10.240.0.42/` het verkeer naar `aks-helloworld`het adres doorgestuurd naar de service met de naam . Verkeer naar `http://10.240.0.42/hello-world-two` het adres wordt `ingress-demo` doorgestuurd naar de service.

Maak een `hello-world-ingress.yaml` bestand met de naam en kopie in het volgende voorbeeld YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Maak de binnenkomende `kubectl apply -f hello-world-ingress.yaml` bron met de opdracht.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>De invallende controller testen

Als u de routes voor de invallende controller wilt testen, bladert u naar de twee toepassingen met een webclient. Indien nodig u deze interne functionaliteit snel testen vanuit een pod op het AKS-cluster. Maak een testpod en voeg er een terminalsessie aan toe:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Installeer `curl` in de `apt-get`pod met behulp van:

```console
apt-get update && apt-get install -y curl
```

Krijg nu toegang tot het adres `curl`van *http://10.240.0.42*uw Kubernetes-ingress-controller met behulp van , zoals . Geef uw eigen interne IP-adres op dat is opgegeven wanneer u de invallende controller in de eerste stap van dit artikel hebt geïmplementeerd.

```console
curl -L http://10.240.0.42
```

Er is geen extra pad voorzien van het adres, */* dus de insingress-controller wordt standaard op de route weergegeven. De eerste demo-toepassing wordt geretourneerd, zoals wordt weergegeven in de volgende verkorte voorbeelduitvoer:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Voeg nu */hello-world-two* pad toe *http://10.240.0.42/hello-world-two*aan het adres, zoals . De tweede demotoepassing met de aangepaste titel wordt geretourneerd, zoals wordt weergegeven in de volgende verkorte voorbeelduitvoer:

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel is Helm gebruikt om de binnenkomende componenten en voorbeeld-apps te installeren. Wanneer u een Helm-diagram implementeert, worden een aantal Kubernetes-bronnen gemaakt. Deze bronnen omvatten pods, implementaties en services. Als u deze resources wilt opschonen, u de volledige naamruimte van het voorbeeld of de afzonderlijke resources verwijderen.

### <a name="delete-the-sample-namespace-and-all-resources"></a>De voorbeeldnaamruimte en alle bronnen verwijderen

Als u de volledige voorbeeldnaamruimte wilt verwijderen, gebruikt u de `kubectl delete` opdracht en geeft u de naam naam van de naamruimte op. Alle bronnen in de naamruimte worden verwijderd.

```console
kubectl delete namespace ingress-basic
```

Verwijder vervolgens de Helm repo voor de AKS hello world app:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Resources afzonderlijk verwijderen

Een meer gedetailleerde benadering is ook het verwijderen van de afzonderlijke bronnen die zijn gemaakt. Lijst van de `helm list` Helm releases met de opdracht. Zoek naar grafieken met de naam *nginx-ingress* en *aks-helloworld,* zoals weergegeven in de volgende voorbeelduitvoer:

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Verwijder de releases `helm delete` met de opdracht. In het volgende voorbeeld wordt de NGINX-implementatie verwijderd en worden de twee voorbeeld-AKS hello world-apps verwijderd.

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
```

Verwijder vervolgens de Helm repo voor de AKS hello world app:

```console
helm repo remove azure-samples
```

Verwijder de invallende route die verkeer naar de voorbeeld-apps leidde:

```console
kubectl delete -f hello-world-ingress.yaml
```

Ten slotte u de naamruimte zelf verwijderen. Gebruik `kubectl delete` de opdracht en geef de naam naam van de naamruimte op:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Volgende stappen

Dit artikel bevat een aantal externe componenten van AKS. Zie de volgende projectpagina's voor meer informatie over deze onderdelen:

- [Helm CLI][helm-cli]
- [NGINX-ingress-controller][nginx-ingress]

U kunt ook het volgende doen:

- [Een basis-ingress-controller maken met externe netwerkconnectiviteit][aks-ingress-basic]
- [De invoegtoepassing HTTP-toepassingsroutering inschakelen][aks-http-app-routing]
- [Maak een invallende controller met een dynamisch openbaar IP-adres en configureer Let's Encrypt om automatisch TLS-certificaten te genereren][aks-ingress-tls]
- [Een invallende controller maken met een statisch openbaar IP-adres en Let's Encrypt configureren om automatisch TLS-certificaten te genereren][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers