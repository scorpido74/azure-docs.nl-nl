---
title: Een aangepaste traefik-ingress-controller gebruiken en HTTPS configureren
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Meer informatie over het configureren van Azure Dev Spaces om een aangepaste traefik-ingress-controller te gebruiken en HTTPS te configureren met die invallende controller
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: fd11b3bbd3f90b75203084ff0753c1485d57a35b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155426"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Een aangepaste traefik-ingress-controller gebruiken en HTTPS configureren

In dit artikel ziet u hoe u Azure Dev Spaces configureert om een aangepaste traefik-ingress-controller te gebruiken. In dit artikel ziet u ook hoe u die aangepaste ingress-controller configureert om HTTPS te gebruiken.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account aanmaken][azure-account-create].
* [Azure CLI geïnstalleerd][az-cli].
* [Aks-cluster (Azure Kubernetes Service) met Azure Dev Spaces ingeschakeld][qs-cli].
* [kubectl][kubectl] geïnstalleerd.
* [Helm 3 geïnstalleerd][helm-installed].
* [Een aangepast domein][custom-domain] met een [DNS-zone][dns-zone]. In dit artikel wordt ervan uitgegaan dat het aangepaste domein en de DNS-zone zich in dezelfde brongroep bevinden als uw AKS-cluster, maar het is mogelijk om een aangepast domein en DNS-zone in een andere brongroep te gebruiken.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Een aangepaste traefik-ingress-controller configureren

Maak verbinding met uw cluster met behulp van [kubectl][kubectl], de Kubernetes-opdrachtregelclient. Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Voeg de [officiële stabiele Helm repository][helm-stable-repo], die de traefik ingress controller Helm grafiek bevat.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Maak een Kubernetes-naamruimte voor de traefik-ingress-controller en installeer deze met behulp van `helm`.

> [!NOTE]
> Als rbac niet is ingeschakeld voor uw AKS-cluster, verwijdert u de parameter RBAC.enabled=True.If your AKS cluster does not have RBAC enabled, remove the *--set rbac.enabled=true* parameter.

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> In het bovenstaande voorbeeld wordt een openbaar eindpunt voor uw invallende controller gemaakt. Als u in plaats daarvan een privéeindpunt voor uw invallende controller moet gebruiken, voegt u de *-set service.annotaties toe." service\\\\.beta\\.kubernetes .io/azure-load-balancer-internal"=true* parameter to the *helm install* command.
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> Dit privéeindpunt wordt weergegeven in het virtuele netwerk waarin u het AKS-cluster wordt geïmplementeerd.

Krijg het IP-adres van de traefik ingress controller service met behulp van [kubectl krijgen][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

De voorbeelduitvoer toont de IP-adressen voor alle services in de *naamruimte traefik.*

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Voeg een *A-record* toe aan uw DNS-zone met het externe IP-adres van de traefik-service met behulp van [dns-record van het AZ-netwerk.][az-network-dns-record-set-a-add-record]

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

In het bovenstaande voorbeeld wordt een *A-record* toegevoegd aan de *MY_CUSTOM_DOMAIN* DNS-zone.

In dit artikel gebruikt u de [voorbeeldtoepassing Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) om aan te tonen met Azure Dev Spaces. Kloon de toepassing uit GitHub en navigeer naar de bijbehorende map:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Open [values.yaml][values-yaml] en breng de volgende updates uit:
* Vervang alle gevallen van *<REPLACE_ME_WITH_HOST_SUFFIX>* door *traefik. MY_CUSTOM_DOMAIN* uw domein voor *MY_CUSTOM_DOMAIN*. 
* Vervang *kubernetes.io/ingress.class: traefik-azds # Dev Spaces-specifiek* met *kubernetes.io/ingress.class: traefik # Custom Ingress*. 

Hieronder vindt u een `values.yaml` voorbeeld van een bijgewerkt bestand:

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

Sla uw wijzigingen op en sluit het bestand.

Maak de *dev-ruimte* met `azds space select`uw voorbeeldtoepassing met behulp van .

```console
azds space select -n dev -y
```

Implementeer de voorbeeldtoepassing met behulp van `helm install`.

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

In het bovenstaande voorbeeld wordt de voorbeeldtoepassing geïmplementeerd in *de dev-naamruimte.*

Geef de URL's weer `azds list-uris`om toegang te krijgen tot de voorbeeldtoepassing met behulp van .

```console
azds list-uris
```

De onderstaande uitvoer toont `azds list-uris`het voorbeeld URL's van .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navigeer naar de *bikesharingweb-service* door `azds list-uris` de openbare URL van de opdracht te openen. In het bovenstaande voorbeeld is `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`de openbare URL voor de *bikesharingweb-service* .

> [!NOTE]
> Als u een foutpagina ziet in plaats van de *bikesharingweb-service,* controleert u of u **zowel** de kubernetes.io/ingress.class-annotatie als de host in het *bestand values.yaml* hebt bijgewerkt. *kubernetes.io/ingress.class*

Gebruik `azds space select` de opdracht om een onderliggende ruimte onder *dev* te maken en de URL's weer te geven om toegang te krijgen tot de onderliggende dev-ruimte.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

De onderstaande uitvoer toont `azds list-uris` het voorbeeld URL's van de voorbeeldtoepassing om toegang te krijgen tot de voorbeeldtoepassing in de *onderliggende onderliggende ruimte azureuser1.*

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navigeer naar de *bikesharingweb-service* in de *azureuser1* child dev-ruimte door de openbare URL vanuit de `azds list-uris` opdracht te openen. In het bovenstaande voorbeeld is `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`de openbare URL voor de *bikesharingweb-service* in de *azureuser1* child dev-ruimte .

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>De traefik-ingress-controller configureren om HTTPS te gebruiken

Gebruik [cert-manager][cert-manager] om het beheer van het TLS-certificaat te automatiseren bij het configureren van uw traefik-ingress-controller om HTTPS te gebruiken. Gebruik `helm` om de *certmanager-grafiek* te installeren.

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Maak `letsencrypt-clusterissuer.yaml` een bestand en werk het e-mailveld bij met uw e-mailadres.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
      - http01:
          ingress:
            class: traefik
```

> [!NOTE]
> Voor het testen is er ook een [staging server][letsencrypt-staging-issuer] die u gebruiken voor uw *ClusterIssuer.*

Gebruik `kubectl` om `letsencrypt-clusterissuer.yaml`toe te passen .

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

Verwijder de vorige *traefik* *ClusterRole* en *ClusterRoleBinding*en upgrade `helm`vervolgens traefik om HTTPS te gebruiken met behulp van .

> [!NOTE]
> Als rbac niet is ingeschakeld voor uw AKS-cluster, verwijdert u de parameter RBAC.enabled=True.If your AKS cluster does not have RBAC enabled, remove the *--set rbac.enabled=true* parameter.

```console
kubectl delete ClusterRole traefik
kubectl delete ClusterRoleBinding traefik
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

Ontvang het bijgewerkte IP-adres van de traefik-service voor invallende controller met behulp van [kubectl get.][kubectl-get]

```console
kubectl get svc -n traefik --watch
```

De voorbeelduitvoer toont de IP-adressen voor alle services in de *naamruimte traefik.*

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP          PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>            80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_NEW_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Voeg een *A-record* toe aan uw DNS-zone met het nieuwe externe IP-adres van de traefik-service met behulp van [dns-record van het AZ-netwerk][az-network-dns-record-set-a-add-record] en verwijder de vorige *A-record* met behulp van [dns-record-record van het AZ-netwerk.][az-network-dns-record-set-a-remove-record]

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_NEW_EXTERNAL_IP

az network dns record-set a remove-record \
    --resource-group myResourceGroup \
    --zone-name  MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address PREVIOUS_EXTERNAL_IP
```

In het bovenstaande voorbeeld wordt de *A-record* in de *MY_CUSTOM_DOMAIN* DNS-zone bijgewerkt om *PREVIOUS_EXTERNAL_IP*te gebruiken.

Update [values.yaml][values-yaml] om de details voor het gebruik van *cert-manager* en HTTPS op te nemen. Hieronder vindt u een `values.yaml` voorbeeld van een bijgewerkt bestand:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

Upgrade de voorbeeldtoepassing met `helm`:

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Navigeer naar de voorbeeldtoepassing in de onderliggende ruimte *voor dev/azureuser1* en zie dat u wordt doorgestuurd naar https.

> [!IMPORTANT]
> Het kan 30 minuten of langer duren voordat de DNS-wijzigingen zijn voltooid en uw voorbeeldtoepassing toegankelijk is.

Merk ook op dat de pagina laadt, maar de browser toont een aantal fouten. Als u de browserconsole opent, wordt de fout weergegeven op een HTTPS-pagina die HTTP-bronnen probeert te laden. Bijvoorbeeld:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Als u deze fout wilt oplossen, werkt u [BikeSharingWeb/azds.yaml][azds-yaml] bij om *traefik* te gebruiken voor *kubernetes.io/ingress.class* en uw aangepaste domein voor *$(hostSuffix).* Bijvoorbeeld:

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

Update [BikeSharingWeb/package.json][package-json] met een afhankelijkheid voor het *url-pakket.*

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Werk de *getApiHostAsync-methode* in [BikeSharingWeb/lib/helpers.js bij][helpers-js] om HTTPS te gebruiken:

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

Navigeer naar `BikeSharingWeb` de `azds up` map en gebruik om uw bijgewerkte BikeSharingWeb-service uit te voeren.

```console
cd ../BikeSharingWeb/
azds up
```

Navigeer naar de voorbeeldtoepassing in de onderliggende ruimte *voor dev/azureuser1* en merk dat u wordt doorgestuurd naar HTTPS zonder fouten.

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Azure Dev Spaces u helpt complexere toepassingen te ontwikkelen voor meerdere containers en hoe u de samenwerking vereenvoudigen door te werken met verschillende versies of branches van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Teamontwikkeling in Azure Dev Spaces][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[az-network-dns-record-set-a-remove-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-remove-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml