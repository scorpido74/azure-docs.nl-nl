---
title: Uw eigen TLS-certificaten gebruiken voor inkomend verkeer met Azure Kubernetes service-cluster (AKS)
description: Meer informatie over het installeren en configureren van een NGINX ingress-controller die gebruikmaakt van uw eigen certificaten in een Azure Kubernetes service-cluster (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 2b30ade9971ede6f9544b618504033553392e9bd
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "67615443"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Een HTTPS ingress-controller maken en uw eigen TLS-certificaten gebruiken op de Azure Kubernetes-service (AKS)

Een controller voor inkomend verkeer is een stukje software dat omgekeerde proxy’s, configureerbare verkeersroutering en TLS-beëindiging voor Kubernetes-services biedt. Kubernetes-resources voor inkomend verkeer worden gebruikt om de regels en routes voor uitgaand verkeer worden geconfigureerd voor individuele Kubernetes-services. Met behulp van een controller en regels voor inkomend verkeer kan er één enkel IP-adres worden gebruikt voor het routeren van verkeer naar meerdere services in een Kubernetes-cluster.

In dit artikel wordt beschreven hoe u de [NGINX ingress-controller][nginx-ingress] implementeert in een Azure Kubernetes service-cluster (AKS). U genereert uw eigen certificaten en maakt een Kubernetes-geheim voor gebruik met de ingangs route. Ten slotte worden er twee toepassingen uitgevoerd in het AKS-cluster, die allemaal toegankelijk zijn via één IP-adres.

U kunt ook het volgende doen:

- [Een eenvoudige ingangs controller met externe netwerk verbinding maken][aks-ingress-basic]
- [De invoeg toepassing voor het routeren van HTTP-toepassingen inschakelen][aks-http-app-routing]
- [Een ingangs controller maken die gebruikmaakt van een intern, privé netwerk en IP-adres][aks-ingress-internal]
- Een ingangs controller maken die gebruikmaakt van een versleuteling om automatisch TLS-certificaten te genereren [met een dynamisch openbaar IP-adres][aks-ingress-tls] of [met een statisch openbaar IP-adres][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt helm gebruikt om de NGINX ingangs controller en een voor beeld-web-app te installeren. U moet helm hebben geïnitialiseerd binnen uw AKS-cluster en gebruikmaken van een service account voor Tiller. Zorg ervoor dat u de nieuwste versie van helm gebruikt. Zie [helm install docs][helm-install](Engelstalig) voor upgrade-instructies. Zie [Installing Applications with helm in azure Kubernetes service (AKS) (Engelstalig)][use-helm]voor meer informatie over het configureren en gebruiken van helm.

Voor dit artikel moet u ook de Azure CLI-versie 2.0.64 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-an-ingress-controller"></a>Een ingangs controller maken

Als u de ingangs controller wilt maken `Helm` , gebruikt u voor het installeren van *nginx-ingang*. Voor toegevoegde redundantie worden twee replica's van de NGINX ingress-controllers geïmplementeerd met de `--set controller.replicaCount` para meter. Om volledig te profiteren van het uitvoeren van replica's van de ingangs controller, moet u ervoor zorgen dat er meer dan één knoop punt in uw AKS-cluster is.

De ingangs controller moet ook worden gepland op een Linux-knoop punt. Windows Server-knoop punten (momenteel in de preview-versie van AKS) mogen de ingangs controller niet uitvoeren. Een knooppunt kiezer wordt opgegeven met behulp `--set nodeSelector` van de para meter om aan te geven dat de Kubernetes scheduler de NGINX ingangs controller moet uitvoeren op een Linux-knoop punt.

> [!TIP]
> In het volgende voor beeld wordt een Kubernetes-naam ruimte gemaakt voor de ingangs resources met de naam *ingress-Basic*. Geef waar nodig een naam ruimte op voor uw eigen omgeving. Als op uw AKS-cluster geen RBAC is ingeschakeld `--set rbac.create=false` , voegt u toe aan de helm-opdrachten.

> [!TIP]
> Als u [IP-behoud van client bronnen][client-source-ip] wilt inschakelen voor aanvragen voor containers in uw cluster, voegt `--set controller.service.externalTrafficPolicy=Local` u toe aan de helm-installatie opdracht. Het bron-IP-adres van de client wordt opgeslagen in de aanvraag header onder *X-doorgestuurd-voor*. Bij gebruik van een ingangs controller waarvoor IP-behoud door client bronnen is ingeschakeld, werkt SSL Pass-Through niet.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Tijdens de installatie wordt een openbaar IP-adres van Azure gemaakt voor de ingangs controller. Dit open bare IP-adres is statisch voor de levens duur van de ingangs controller. Als u de ingangs controller verwijdert, gaat de toewijzing van het open bare IP-adres verloren. Als u vervolgens een extra ingangs controller maakt, wordt er een nieuw openbaar IP-adres toegewezen. Als u het gebruik van het open bare IP-adres wilt behouden, kunt u in plaats daarvan [een ingangs controller met een statisch openbaar IP-adres maken][aks-ingress-static-tls].

Gebruik de `kubectl get service` opdracht om het open bare IP-adres op te halen. Het duurt enkele minuten voordat het IP-adres is toegewezen aan de service.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Noteer dit open bare IP-adres, zoals het in de laatste stap wordt gebruikt om de implementatie te testen.

Er zijn nog geen inkomende regels gemaakt. Als u naar het open bare IP-adres bladert, wordt de standaard 404-pagina van de NGINX ingress-controller weer gegeven.

## <a name="generate-tls-certificates"></a>TLS-certificaten genereren

Voor dit artikel genereren we een zelfondertekend certificaat met `openssl`. Voor productie gebruik moet u een vertrouwd, ondertekend certificaat aanvragen via een provider of uw eigen certificerings instantie (CA). In de volgende stap genereert u een Kubernetes- *geheim* met behulp van het TLS-certificaat en de persoonlijke sleutel die door openssl worden gegenereerd.

In het volgende voor beeld wordt een 2048-bits RSA x509-certificaat gegenereerd dat geldig is voor 365 dagen met de naam *AKS-ingress-TLS. CRT*. Het persoonlijke sleutel bestand heeft de naam *AKS-ingress-TLS. key*. Voor een Kubernetes TLS-geheim zijn beide bestanden vereist.

Dit artikel werkt met de algemene naam van de *demo.Azure.com* -onderwerp en hoeft niet te worden gewijzigd. Geef voor productie gebruik uw eigen organisatie waarden op voor de `-subj` para meter:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Kubernetes-geheim voor het TLS-certificaat maken

U kunt een geheim maken en gebruiken om Kubernetes toe te staan het TLS-certificaat en de persoonlijke sleutel te gebruiken voor de ingangs controller. Het geheim wordt eenmaal gedefinieerd en gebruikt het certificaat en sleutel bestand dat u in de vorige stap hebt gemaakt. Vervolgens verwijst u naar dit geheim wanneer u ingangs routes definieert.

In het volgende voor beeld wordt een geheime naam *AKS-ingangs-TLS*gemaakt:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Demo toepassingen uitvoeren

Een ingangs controller en een geheim met uw certificaat zijn geconfigureerd. We gaan nu twee demonstratie toepassingen uitvoeren in uw AKS-cluster. In dit voor beeld wordt helm gebruikt om twee exemplaren van een eenvoudige ' Hallo wereld '-toepassing te implementeren.

Voordat u de voor beelden van helm-grafieken kunt installeren, moet u de opslag plaats voor Azure samples als volgt toevoegen aan uw helm-omgeving:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Maak de eerste demo toepassing vanuit een helm-diagram met de volgende opdracht:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Installeer nu een tweede exemplaar van de voorbeeld toepassing. Voor het tweede exemplaar geeft u een nieuwe titel op zodat de twee toepassingen visueel worden onderscheiden. U geeft ook een unieke service naam op:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Een ingangs route maken

Beide toepassingen worden nu uitgevoerd op uw Kubernetes-cluster, maar ze zijn geconfigureerd met een service van `ClusterIP`het type. De toepassingen zijn dus niet toegankelijk via internet. Als u ze openbaar beschikbaar wilt maken, maakt u een Kubernetes-ingangs bron. De ingangs resource configureert de regels die verkeer routeren naar een van de twee toepassingen.

In het volgende voor beeld wordt verkeer naar het `https://demo.azure.com/` adres gerouteerd naar de service met de `aks-helloworld`naam. Verkeer naar het adres `https://demo.azure.com/hello-world-two` wordt doorgestuurd naar de `ingress-demo` service. Voor dit artikel hoeft u deze namen van de demo-hosts niet te wijzigen. Geef voor gebruik van productie de namen op die zijn opgegeven als onderdeel van de certificaat aanvraag en het generatie proces.

> [!TIP]
> Als de hostnaam die tijdens het proces van de certificaat aanvraag is opgegeven, de CN-naam niet overeenkomt met de host die is gedefinieerd in de ingangs route, geeft de controller een *vervalst certificaat*van de Kubernetes ingress-controller weer. Zorg ervoor dat uw certificaat en ingangen voor route-hostnamen overeenkomen.

De sectie *TLS* vertelt de ingangs route om het geheim met de naam *AKS-ingress-TLS* te gebruiken voor de *demo.Azure.com*van de host. Geef het adres van uw eigen host op voor productie gebruik.

Maak een bestand met `hello-world-ingress.yaml` de naam en kopieer het in het volgende voor beeld YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
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

Maak de ingangs resource met behulp van de `kubectl apply -f hello-world-ingress.yaml` opdracht.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>De ingangs configuratie testen

Als u de certificaten met onze valse *demo.Azure.com* -host wilt `curl` testen, gebruikt u en geeft u de para meter *--* Resolve op. Met deze para meter kunt u de naam van de *demo.Azure.com* toewijzen aan het open bare IP-adres van uw ingangs controller. Geef het open bare IP-adres van uw eigen ingangs controller op, zoals wordt weer gegeven in het volgende voor beeld:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Er is geen extra pad met het adres meegeleverd, dus de ingangs controller wordt standaard */* ingesteld op de route. De eerste demo toepassing wordt geretourneerd, zoals wordt weer gegeven in de volgende verkorte voorbeeld uitvoer:

```
$ curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

De para meter *-v* in `curl` de opdracht voert uitgebreide informatie uit, met inbegrip van het ontvangen TLS-certificaat. U kunt uw eigen TLS-certificaat gebruiken om te zien hoe u in de richting van de krul in de weg bent. De para meter *-k* gaat door met het laden van de pagina, zelfs als we een zelfondertekend certificaat gebruiken. In het volgende voor beeld ziet *u dat de certificaat verlener: CN = demo. Azure. com; O = AKS-ingangs-TLS* -certificaat is gebruikt:

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

Voeg nu */Hello-World-Two* -pad toe aan het adres, `https://demo.azure.com/hello-world-two`zoals. De tweede demo toepassing met de aangepaste titel wordt geretourneerd, zoals wordt weer gegeven in de volgende verkorte voorbeeld uitvoer:

```
$ curl -v -k --resolve demo.azure.com:443:137.117.36.18 https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel wordt helm gebruikt voor het installeren van de ingangs onderdelen en voor beeld-apps. Wanneer u een helm-grafiek implementeert, worden er een aantal Kubernetes-resources gemaakt. Deze resources omvatten Peul, implementaties en services. Als u deze resources wilt opschonen, kunt u de volledige voorbeeld naam ruimte of de afzonderlijke resources verwijderen.

### <a name="delete-the-sample-namespace-and-all-resources"></a>De voorbeeld naam ruimte en alle resources verwijderen

Als u de volledige voorbeeld naam ruimte wilt verwijderen `kubectl delete` , gebruikt u de opdracht en geeft u de naam van de naam ruimte op. Alle resources in de naam ruimte worden verwijderd.

```console
kubectl delete namespace ingress-basic
```

Verwijder vervolgens de helm-opslag plaats voor de AKS Hallo wereld-app:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Resources afzonderlijk verwijderen

U kunt ook een nauw keurigere benadering van de gemaakte afzonderlijke resources verwijderen. Vermeld de helm-releases met `helm list` de opdracht. Zoek naar grafieken met de naam *nginx-ingangs* en *AKS-HelloWorld*, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```
$ helm list

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Verwijder de releases met de `helm delete` opdracht. In het volgende voor beeld worden de NGINX ingress-implementatie en de twee voor beelden van de AKS Hello World-apps verwijderd.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Verwijder vervolgens de helm-opslag plaats voor de AKS Hallo wereld-app:

```console
helm repo remove azure-samples
```

Verwijder de ingangs route die het verkeer naar de voor beeld-apps doorstuurt:

```console
kubectl delete -f hello-world-ingress.yaml
```

Het certificaat geheim verwijderen:

```console
kubectl delete secret aks-ingress-tls
```

Ten slotte kunt u de zelf naam ruimte verwijderen. Gebruik de `kubectl delete` opdracht en geef de naam van de naam ruimte op:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel zijn enkele externe onderdelen opgenomen in AKS. Zie de volgende project pagina's voor meer informatie over deze onderdelen:

- [Helm CLI][helm-cli]
- [NGINX ingress-controller][nginx-ingress]

U kunt ook het volgende doen:

- [Een eenvoudige ingangs controller met externe netwerk verbinding maken][aks-ingress-basic]
- [De invoeg toepassing voor het routeren van HTTP-toepassingen inschakelen][aks-http-app-routing]
- [Een ingangs controller maken die gebruikmaakt van een intern, privé netwerk en IP-adres][aks-ingress-internal]
- Een ingangs controller maken die gebruikmaakt van een versleuteling om automatisch TLS-certificaten te genereren [met een dynamisch openbaar IP-adres][aks-ingress-tls] of [met een statisch openbaar IP-adres][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers