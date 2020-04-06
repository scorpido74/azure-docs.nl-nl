---
title: Uw TLS-certificaten gebruiken voor binnendringen
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het installeren en configureren van een NGINX-ingress-controller die uw eigen certificaten gebruikt in een AKS-cluster (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: ec6a398b52424c142b3d7ee82625c10c733456ab
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668459"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>HTTPS-controller voor inkomend verkeer maken en uw eigen TLS-certificaten gebruiken in Azure Kubernetes Service (AKS)

Een controller voor inkomend verkeer is een stukje software dat omgekeerde proxy’s, configureerbare verkeersroutering en TLS-beëindiging voor Kubernetes-services biedt. Kubernetes-resources voor inkomend verkeer worden gebruikt om de regels en routes voor uitgaand verkeer worden geconfigureerd voor individuele Kubernetes-services. Met behulp van een controller en regels voor inkomend verkeer kan er één enkel IP-adres worden gebruikt voor het routeren van verkeer naar meerdere services in een Kubernetes-cluster.

In dit artikel ziet u hoe u de [NGINX-ingress-controller implementeert][nginx-ingress] in een AKS-cluster (Azure Kubernetes Service). U genereert uw eigen certificaten en maakt een Kubernetes-geheim voor gebruik met de invallende route. Ten slotte worden twee toepassingen uitgevoerd in het AKS-cluster, die elk toegankelijk zijn via één IP-adres.

U kunt ook het volgende doen:

- [Een basis-ingress-controller maken met externe netwerkconnectiviteit][aks-ingress-basic]
- [De invoegtoepassing HTTP-toepassingsroutering inschakelen][aks-http-app-routing]
- [Een invallende controller maken die een intern, privénetwerk en IP-adres gebruikt][aks-ingress-internal]
- Een invallende controller maken die Let's Encrypt gebruikt om automatisch TLS-certificaten te genereren [met een dynamisch openbaar IP-adres][aks-ingress-tls] of met een statisch openbaar [IP-adres][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt Helm gebruikt om de NGINX-ingress-controller en een voorbeeldweb-app te installeren. U moet Helm laten initialiseren binnen uw AKS-cluster en een serviceaccount voor Tiller gebruiken. Zorg ervoor dat u de nieuwste versie van Helm gebruikt. Zie De [installatiedocumenten van Het helm voor][helm-install]upgrade-instructies . Zie [Toepassingen installeren met Helm in Azure Kubernetes Service (AKS) voor][use-helm]meer informatie over het configureren en gebruiken van Helm.

In dit artikel moet u ook de Azure CLI-versie 2.0.64 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Een invallende controller maken

Als u de insteekcontroller wilt maken, gebruikt u `Helm` om *nginx-ingress*te installeren. Voor toegevoegde redundantie worden er twee replica's van de NGINX-ingangscontrollers geïmplementeerd met de parameter `--set controller.replicaCount`. Als u optimaal wilt profiteren van het uitvoeren van replica's van de invallende controller, moet u ervoor zorgen dat er meer dan één knooppunt in uw AKS-cluster is.

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
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Tijdens de installatie wordt een Openbaar IP-adres van Azure gemaakt voor de invallende controller. Dit openbare IP-adres is statisch voor de levensduur van de invallende controller. Als u de invallende controller verwijdert, gaat de toewijzing van het openbare IP-adres verloren. Als u vervolgens een extra invallende controller maakt, wordt een nieuw openbaar IP-adres toegewezen. Als u het gebruik van het openbare IP-adres wilt behouden, u in plaats daarvan [een invallende controller maken met een statisch openbaar IP-adres.][aks-ingress-static-tls]

Als u het openbare IP-adres wilt krijgen, gebruikt u de `kubectl get service` opdracht. Het duurt enkele minuten voordat het IP-adres aan de service is toegewezen.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Noteer dit openbare IP-adres, omdat het in de laatste stap wordt gebruikt om de implementatie te testen.

Er zijn nog geen invallenregels gemaakt. Als u naar het openbare IP-adres bladert, wordt de standaard 404-pagina van de NGINX-ingress-controller weergegeven.

## <a name="generate-tls-certificates"></a>TLS-certificaten genereren

Voor dit artikel, laten we het genereren `openssl`van een zelf ondertekend certificaat met . Voor productiegebruik moet u een vertrouwd, ondertekend certificaat aanvragen via een provider of uw eigen certificaatautoriteit (CA). In de volgende stap genereert u een Kubernetes *Secret* met behulp van het TLS-certificaat en de privésleutel gegenereerd door OpenSSL.

In het volgende voorbeeld wordt een RSA X509-certificaat met een 2048-bits RSA X509-certificaat gegenereerd dat 365 dagen geldig is met de naam *aks-ingress-tls.crt.* Het private key-bestand heeft de naam *aks-ingress-tls.key.* Een Kubernetes TLS geheim vereist beide bestanden.

Dit artikel werkt met de *demo.azure.com* algemene naam van het onderwerp en hoeft niet te worden gewijzigd. Geef voor productiegebruik uw eigen `-subj` organisatiewaarden voor de parameter op:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Kubernetes-geheim maken voor TLS-certificaat

Als u Kubernetes de TLS-certificaat en de privésleutel voor de ingress-controller wilt laten gebruiken, maakt en gebruikt u een Secret. Het geheim wordt eenmaal gedefinieerd en gebruikt het certificaat en het sleutelbestand dat in de vorige stap is gemaakt. U verwijst dan naar dit geheim wanneer u invallenroutes definieert.

In het volgende voorbeeld wordt een geheime naam *aks-ingress-tls*gemaakt:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Demotoepassingen uitvoeren

Een invallende controller en een geheim met uw certificaat zijn geconfigureerd. Laten we nu twee demotoepassingen uitvoeren in uw AKS-cluster. In dit voorbeeld wordt Helm gebruikt om twee exemplaren van een eenvoudige 'Hello world'-toepassing te implementeren.

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

Beide toepassingen worden nu uitgevoerd op uw Kubernetes-cluster, maar `ClusterIP`ze zijn geconfigureerd met een service van het type . Als zodanig zijn de toepassingen niet toegankelijk vanaf het internet. Maak een Kubernetes-ingress-bron om ze openbaar te maken. De binnenkomende bron configureert de regels die verkeer doorsturen naar een van de twee toepassingen.

In het volgende voorbeeld wordt `https://demo.azure.com/` het verkeer naar `aks-helloworld`het adres doorgestuurd naar de service met de naam . Verkeer naar `https://demo.azure.com/hello-world-two` het adres wordt `ingress-demo` doorgestuurd naar de service. Voor dit artikel hoeft u deze demohostnamen niet te wijzigen. Geef voor productiegebruik de namen op die zijn opgegeven als onderdeel van het certificaataanvraag- en generatieproces.

> [!TIP]
> Als de hostnaam die is opgegeven tijdens het certificaataanvraagproces, de CN-naam, niet overeenkomt met de host die is gedefinieerd in uw invallenroute, geeft u een waarschuwing voor een *Valse certificaatwaarschuwing voor Kubernetes Ingress Controller* weer. Zorg ervoor dat uw certificaat en invallende routehostnamen overeenkomen.

De *tls-sectie* vertelt de invallende route om de Secret genaamd *aks-ingress-tls* te gebruiken voor de host *demo.azure.com*. Nogmaals, voor productiegebruik, geef uw eigen hostadres op.

Maak een `hello-world-ingress.yaml` bestand met de naam en kopie in het volgende voorbeeld YAML.

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

Maak de binnenkomende `kubectl apply -f hello-world-ingress.yaml` bron met de opdracht.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>De insi-configuratie testen

Als u de certificaten wilt testen `curl` met onze *nep-demo.azure.com* host, gebruikt en specificeert u de parameter *--resolve.* Met deze parameter u de *demo.azure.com* naam toewijzen aan het openbare IP-adres van uw in- en invalcontroller. Geef het openbare IP-adres van uw eigen ingress-controller op, zoals in het volgende voorbeeld wordt weergegeven:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Er is geen extra pad voorzien van het adres, */* dus de insingress-controller wordt standaard op de route weergegeven. De eerste demo-toepassing wordt geretourneerd, zoals wordt weergegeven in de volgende verkorte voorbeelduitvoer:

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

De *parameter -v* in onze `curl` opdrachtvoert verbose informatie uit, inclusief het ontvangen TLS-certificaat. Halverwege je kruluitvoer u controleren of uw eigen TLS-certificaat is gebruikt. De parameter *-k* blijft de pagina laden, ook al gebruiken we een zelfondertekend certificaat. In het volgende voorbeeld ziet u dat de *uitgever: CN=demo.azure.com; O=aks-ingress-tls-certificaat* werd gebruikt:

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

Voeg nu */hello-world-two* pad toe `https://demo.azure.com/hello-world-two`aan het adres, zoals . De tweede demotoepassing met de aangepaste titel wordt geretourneerd, zoals wordt weergegeven in de volgende verkorte voorbeelduitvoer:

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

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Verwijder de releases `helm delete` met de opdracht. In het volgende voorbeeld worden de NGINX-ingress-implementatie en de twee voorbeeld-AKS hello world-apps verwijderd.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Verwijder vervolgens de Helm repo voor de AKS hello world app:

```console
helm repo remove azure-samples
```

Verwijder de invallende route die verkeer naar de voorbeeld-apps leidde:

```console
kubectl delete -f hello-world-ingress.yaml
```

Verwijder het certificaat Geheim:

```console
kubectl delete secret aks-ingress-tls
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
- [Een invallende controller maken die een intern, privénetwerk en IP-adres gebruikt][aks-ingress-internal]
- Een invallende controller maken die Let's Encrypt gebruikt om automatisch TLS-certificaten te genereren [met een dynamisch openbaar IP-adres][aks-ingress-tls] of met een statisch openbaar [IP-adres][aks-ingress-static-tls]

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
