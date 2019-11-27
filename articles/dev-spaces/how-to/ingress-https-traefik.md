---
title: Een aangepaste traefik ingangs controller gebruiken en HTTPS configureren
services: azure-dev-spaces
ms.date: 08/13/2019
ms.topic: conceptual
description: Meer informatie over het configureren van Azure dev Spaces voor het gebruik van een aangepaste traefik ingress-controller en het configureren van HTTPS met deze ingangs controller
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S
ms.openlocfilehash: 8ddaa7b3e982cb85428a7faef20b59525a175778
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482533"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Een aangepaste traefik ingangs controller gebruiken en HTTPS configureren

In dit artikel leest u hoe u Azure dev Spaces configureert voor het gebruik van een aangepaste traefik ingress-controller. In dit artikel leest u ook hoe u die aangepaste ingangs controller configureert voor het gebruik van HTTPS.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account aanmaken][azure-account-create].
* [Azure CLI geïnstalleerd][az-cli].
* [Azure Kubernetes service (AKS)-cluster waarvoor Azure dev Spaces zijn ingeschakeld][qs-cli].
* [kubectl][kubectl] is geïnstalleerd.
* [Helm 2,13-2,16 geïnstalleerd][helm-installed].
* [Een aangepast domein][custom-domain] met een [DNS-zone][dns-zone] in dezelfde resource groep als uw AKS-cluster.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Een aangepaste traefik ingress-controller configureren

Maak verbinding met uw cluster met behulp van [kubectl][kubectl], de Kubernetes-opdracht regel-client. Gebruik de opdracht `kubectl`az aks get-credentials[ om ][az-aks-get-credentials] zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Maak een Kubernetes-naam ruimte voor de traefik ingangs controller en installeer deze met behulp van `helm`.

```console
kubectl create ns traefik
helm init --wait
helm install stable/traefik --name traefik --namespace traefik --set kubernetes.ingressClass=traefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true
```

Haal het IP-adres van de traefik ingress-controller service op met behulp van [kubectl Get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

In de voorbeeld uitvoer ziet u de IP-adressen voor alle services in de *traefik* -naam ruimte.

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Voeg een *A* -record toe aan uw DNS-zone met het externe IP-adres van de traefik-service met [AZ Network DNS record-set A add-record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

In het bovenstaande voor beeld wordt een *A* -record toegevoegd aan de DNS-zone *MY_CUSTOM_DOMAIN* .

In dit artikel gebruikt u de [voorbeeld toepassing delen van Azure dev Spaces Bike](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) om te demonstreren hoe u Azure dev Spaces gebruikt. Kloon de toepassing uit GitHub en navigeer naar de bijbehorende map:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Open [Values. yaml][values-yaml] en vervang alle exemplaren van *< REPLACE_ME_WITH_HOST_SUFFIX >* met *traefik. MY_CUSTOM_DOMAIN* uw domein gebruiken voor *MY_CUSTOM_DOMAIN*. Vervang ook *kubernetes.io/ingress.class: traefik-azds # dev Spaces-specifiek* met *kubernetes.io/ingress.class: traefik # aangepaste*inkomend verkeer. Hieronder ziet u een voor beeld van een bijgewerkt `values.yaml`-bestand:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Sla de wijzigingen op en sluit het bestand.

Implementeer de voorbeeld toepassing met behulp van `helm install`.

```console
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

In het bovenstaande voor beeld wordt de voorbeeld toepassing geïmplementeerd in de naam ruimte voor *ontwikkel aars* .

Selecteer de ruimte voor de *ontwikkeling* van uw voorbeeld toepassing met behulp van `azds space select` en geef de url's weer voor toegang tot de voorbeeld toepassing met behulp van `azds list-uris`.

```console
azds space select -n dev
azds list-uris
```

De onderstaande uitvoer toont de voor beeld-Url's van `azds list-uris`.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Ga naar de *bikesharingweb* -service door de open bare URL te openen via de `azds list-uris` opdracht. In het bovenstaande voor beeld is de open bare URL voor de *bikesharingweb* -service `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

Gebruik de `azds space select` opdracht om een onderliggende ruimte onder *dev* te maken en geef de url's weer voor toegang tot de onderliggende ontwikkel ruimte.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

De onderstaande uitvoer toont de voor beeld-Url's van `azds list-uris` om toegang te krijgen tot de voorbeeld toepassing in de *azureuser1* onderliggende ontwikkel ruimte.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Ga naar de *bikesharingweb* -service in de *azureuser1* -onderliggende ontwikkel ruimte door de open bare URL te openen via de `azds list-uris` opdracht. In het bovenstaande voor beeld is de open bare URL voor de *bikesharingweb* -service in de *azureuser1* -onderliggende ontwikkel ruimte `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>De traefik ingress-controller configureren voor het gebruik van HTTPS

Maak een `dev-spaces/samples/BikeSharingApp/traefik-values.yaml`-bestand dat vergelijkbaar is met het onderstaande voor beeld. Werk de *e-mail* waarde bij met uw eigen e-mail adres, die wordt gebruikt om het certificaat te genereren met de code ring.

```yaml
fullnameOverride: traefik
replicas: 1
cpuLimit: 400m
memoryRequest: 200Mi
memoryLimit: 500Mi
externalTrafficPolicy: Local
kubernetes:
  ingressClass: traefik
  ingressEndpoint:
    useDefaultPublishedService: true
dashboard:
  enabled: false
debug:
  enabled: false
accessLogs:
  enabled: true
  fields:
    defaultMode: keep
    headers:
      defaultMode: keep
      names:
        Authorization: redact
acme:
  enabled: true
  email: "someone@example.com"
  staging: false
  challengeType: tls-alpn-01
ssl:
  enabled: true
  enforced: true
  permanentRedirect: true
  tlsMinVersion: VersionTLS12
  cipherSuites:
    - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_RSA_WITH_AES_128_GCM_SHA256
    - TLS_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
```

Werk uw *traefik* -service bij met `helm repo update` en met inbegrip van het *traefik-waarden. yaml-* bestand dat u hebt gemaakt.

```console
cd ..
helm upgrade traefik stable/traefik --namespace traefik --values traefik-values.yaml
```

Met de bovenstaande opdracht wordt een nieuwe versie van de traefik-service uitgevoerd met de waarden van *traefik. yaml* en wordt de vorige service verwijderd. De traefik-service maakt ook een TLS-certificaat met behulp van de code ring en het omleiden van webverkeer voor gebruik van HTTPS.

> [!NOTE]
> Het kan enkele minuten duren voordat de nieuwe versie van de traefik-service wordt gestart. U kunt de voortgang controleren met behulp van `kubectl get pods --namespace traefik --watch`.

Ga naar de voorbeeld toepassing in de *azureuser1 voor ontwikkel aars/* onderliggende ruimte en Let op dat u wordt omgeleid om HTTPS te gebruiken. U ziet ook dat de pagina wordt geladen, maar de browser bevat een aantal fouten. Wanneer u de browser console opent, wordt de fout weer gegeven in verband met een HTTPS-pagina voor het laden van HTTP-resources. Bijvoorbeeld:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Om deze fout op te lossen, werkt u [BikeSharingWeb/azds. yaml][azds-yaml] bij om *traefik* voor *kubernetes.io/ingress.class* en uw aangepaste domein voor *$ (hostSuffix)* te gebruiken. Bijvoorbeeld:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

Werk [BikeSharingWeb/package. json][package-json] bij met een afhankelijkheid van het *URL* -pakket.

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Werk de methode *getApiHostAsync* in [BikeSharingWeb/pages/helpers. js][helpers-js] bij om HTTPS te gebruiken:

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

Ga naar de map `BikeSharingWeb` en gebruik `azds up` om uw bijgewerkte BikeSharingWeb-service uit te voeren.

```console
cd BikeSharingWeb/
azds up
```

Ga naar de voorbeeld toepassing in de *azureuser1 voor ontwikkel aars/* onderliggende ruimte en Let op dat u wordt omgeleid om HTTPS zonder fouten te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure dev Spaces u helpt om complexere toepassingen te ontwikkelen in meerdere containers en hoe u samenwerkings ontwikkeling kunt vereenvoudigen door te werken met verschillende versies of vertakkingen van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Team ontwikkeling in azure dev Spaces][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[helm-installed]: https://v2.helm.sh/docs/using_helm/#installing-helm
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml