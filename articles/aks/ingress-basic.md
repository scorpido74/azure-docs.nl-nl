---
title: Een basiscontroller voor binnenvallen maken in Azure Kubernetes Service (AKS)
description: Meer informatie over het installeren en configureren van een basis-NGINX-ingress-controller in een AKS-cluster (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 12/20/2019
ms.openlocfilehash: e37f7aa677be129aa9fe568880c53cc860947e30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595634"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Een invallende controller maken in Azure Kubernetes Service (AKS)

Een controller voor inkomend verkeer is een stukje software dat omgekeerde proxy’s, configureerbare verkeersroutering en TLS-beëindiging voor Kubernetes-services biedt. Kubernetes-resources voor inkomend verkeer worden gebruikt om de regels en routes voor uitgaand verkeer worden geconfigureerd voor individuele Kubernetes-services. Met behulp van een controller en regels voor inkomend verkeer kan er één enkel IP-adres worden gebruikt voor het routeren van verkeer naar meerdere services in een Kubernetes-cluster.

In dit artikel ziet u hoe u de [NGINX-ingress-controller implementeert][nginx-ingress] in een AKS-cluster (Azure Kubernetes Service). Twee toepassingen worden vervolgens uitgevoerd in het AKS-cluster, die elk toegankelijk zijn via het enkele IP-adres.

U kunt ook het volgende doen:

- [De invoegtoepassing HTTP-toepassingsroutering inschakelen][aks-http-app-routing]
- [Een invallende controller maken die een intern, privénetwerk en IP-adres gebruikt][aks-ingress-internal]
- [Een ingress-controller maken die uw eigen TLS-certificaten gebruikt][aks-ingress-own-tls]
- Een invallende controller maken die Let's Encrypt gebruikt om automatisch TLS-certificaten te genereren [met een dynamisch openbaar IP-adres][aks-ingress-tls] of met een statisch openbaar [IP-adres][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt Helm gebruikt om de NGINX-ingress-controller en een voorbeeldweb-app te installeren.

In dit artikel moet u ook de Azure CLI-versie 2.0.64 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Een invallende controller maken

Als u de insteekcontroller wilt maken, gebruikt u Helm om *nginx-ingress*te installeren. Voor toegevoegde redundantie worden er twee replica's van de NGINX-ingangscontrollers geïmplementeerd met de parameter `--set controller.replicaCount`. Als u optimaal wilt profiteren van het uitvoeren van replica's van de invallende controller, moet u ervoor zorgen dat er meer dan één knooppunt in uw AKS-cluster is.

De ingangscontroller moet ook worden gepland op een Linux-knooppunt. Windows Server-knooppunten (momenteel in preview in AKS) mogen de invallende controller niet uitvoeren. Er wordt een knooppuntselector opgegeven met behulp van de parameter `--set nodeSelector` om de Kubernetes-planner te laten weten dat de NGINX-ingangscontroller moet worden uitgevoerd op een Linux-knooppunt.

> [!TIP]
> In het volgende voorbeeld wordt een Kubernetes-naamruimte voor de invallende resources met de naam *ingress-basic.* Geef indien nodig een naamruimte op voor uw eigen omgeving.

> [!TIP]
> Als u [IP-behoud van clientbron][client-source-ip] wilt inschakelen voor `--set controller.service.externalTrafficPolicy=Local` aanvragen voor containers in uw cluster, voegt u de installatieopdracht Helm toe. Het IP-adres van de clientbron wordt opgeslagen in de aanvraagkoptekst onder *X-Forwarded-For*. Wanneer een inbinnendringen-controller wordt gebruikt waarbij clientsource IP-behoud is ingeschakeld, werkt SSL-doorgeefservice niet.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Wanneer de Kubernetes load balancer-service wordt gemaakt voor de NGINX-ingress-controller, wordt een dynamisch openbaar IP-adres toegewezen, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-controller         LoadBalancer   10.0.61.144    EXTERNAL_IP   80:30386/TCP,443:32276/TCP   6m2s
nginx-ingress-default-backend    ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Er zijn nog geen invallenregels gemaakt, dus de standaard 404-pagina van de NGINX-ingress-controller wordt weergegeven als u naar het interne IP-adres bladert. Invallenregels worden geconfigureerd in de volgende stappen.

## <a name="run-demo-applications"></a>Demotoepassingen uitvoeren

Als u de ingress-controller in actie wilt zien, laten we twee demotoepassingen uitvoeren in uw AKS-cluster. In dit voorbeeld wordt Helm gebruikt om twee exemplaren van een eenvoudige *Hello-wereldtoepassing* te implementeren.

Voordat u de voorbeeldgrafieken van helm installeren, voegt u de Azure-proefopslagplaats als volgt toe aan uw Helm-omgeving:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Maak de eerste demotoepassing vanuit een helmdiagram met de volgende opdracht:

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Installeer nu een tweede exemplaar van de demo-applicatie. Voor de tweede instantie geeft u een nieuwe titel op, zodat de twee toepassingen visueel verschillend zijn. U geeft ook een unieke servicenaam op:

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Een invallende route maken

Beide toepassingen worden nu uitgevoerd op uw Kubernetes-cluster. Als u verkeer naar elke toepassing wilt routeren, maakt u een Kubernetes-ingress-bron. De binnenkomende bron configureert de regels die verkeer doorsturen naar een van de twee toepassingen.

In het volgende voorbeeld *EXTERNAL_IP* wordt verkeer naar EXTERNAL_IP `aks-helloworld`doorgestuurd naar de service met de naam . Verkeer naar *EXTERNAL_IP/hello-world-two* wordt `aks-helloworld-two` doorgestuurd naar de service. Verkeer naar *EXTERNAL_IP/statisch* wordt doorgestuurd `aks-helloworld` naar de service die is vernoemd naar statische elementen.

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
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

Maak de binnenkomende `kubectl apply -f hello-world-ingress.yaml` bron met de opdracht.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
ingress.extensions/hello-world-ingress-static created
```

## <a name="test-the-ingress-controller"></a>De invallende controller testen

Als u de routes voor de invallende controller wilt testen, bladert u naar de twee toepassingen. Open een webbrowser naar het IP-adres van uw NGINX-ingress-controller, zoals *EXTERNAL_IP.* De eerste demo-toepassing wordt weergegeven in de webbrowser, zoals in het volgende voorbeeld wordt weergegeven:

![Eerste app achter de ingevallen controller](media/ingress-basic/app-one.png)

Voeg nu het */hello-world-twee pad* toe aan het IP-adres, zoals *EXTERNAL_IP/hello-world-two.* De tweede demotoepassing met de aangepaste titel wordt weergegeven:

![Tweede app achter de ingevallen controller](media/ingress-basic/app-two.png)

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
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-06 19:57:00.131576 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-06 19:57:10.971365 -0600 CST    deployed        aks-helloworld-0.1.0               
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Verwijder de releases `helm delete` met de opdracht. In het volgende voorbeeld wordt de NGINX-implementatie verwijderd en worden de twee voorbeeld-AKS hello world-apps verwijderd.

```
$ helm delete aks-helloworld aks-helloworld-two nginx-ingress --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "nginx-ingress" uninstalled
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

- [De invoegtoepassing HTTP-toepassingsroutering inschakelen][aks-http-app-routing]
- [Een invallende controller maken die een intern, privénetwerk en IP-adres gebruikt][aks-ingress-internal]
- [Een ingress-controller maken die uw eigen TLS-certificaten gebruikt][aks-ingress-own-tls]
- Een invallende controller maken die Let's Encrypt gebruikt om automatisch TLS-certificaten te genereren [met een dynamisch openbaar IP-adres][aks-ingress-tls] of met een statisch openbaar [IP-adres][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
