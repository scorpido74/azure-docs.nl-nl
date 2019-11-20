---
title: Een HTTP ingress-controller maken met een statisch IP-adres in azure Kubernetes service (AKS)
description: Meer informatie over het installeren en configureren van een NGINX ingress-controller met een statisch openbaar IP-adres in een Azure Kubernetes service-cluster (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 7e390ed1151c45ca9a325b1795a8fbcad74cdfdb
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74194732"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Een ingangs controller maken met een statisch openbaar IP-adres in azure Kubernetes service (AKS)

Een controller voor inkomend verkeer is een stukje software dat omgekeerde proxy’s, configureerbare verkeersroutering en TLS-beëindiging voor Kubernetes-services biedt. Kubernetes-resources voor inkomend verkeer worden gebruikt om de regels en routes voor uitgaand verkeer worden geconfigureerd voor individuele Kubernetes-services. Met behulp van een controller en regels voor inkomend verkeer kan er één enkel IP-adres worden gebruikt voor het routeren van verkeer naar meerdere services in een Kubernetes-cluster.

In dit artikel wordt beschreven hoe u de [NGINX ingress-controller][nginx-ingress] implementeert in een Azure Kubernetes service-cluster (AKS). De ingangs controller is geconfigureerd met een statisch openbaar IP-adres. Het [CERT-beheer][cert-manager] project wordt gebruikt om certificaten automatisch te genereren en [te configureren.][lets-encrypt] Ten slotte worden er twee toepassingen uitgevoerd in het AKS-cluster, die allemaal toegankelijk zijn via één IP-adres.

U kunt ook het volgende doen:

- [Een eenvoudige ingangs controller met externe netwerk verbinding maken][aks-ingress-basic]
- [De invoeg toepassing voor het routeren van HTTP-toepassingen inschakelen][aks-http-app-routing]
- [Een ingangs controller maken die gebruikmaakt van uw eigen TLS-certificaten][aks-ingress-own-tls]
- [Een ingangs controller maken die gebruikmaakt van de code ring om automatisch TLS-certificaten te genereren met een dynamisch openbaar IP-adres][aks-ingress-tls]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

In dit artikel wordt gebruikgemaakt van helm voor het installeren van de NGINX ingress-controller, CERT-Manager en een voor beeld-web-app. U moet helm hebben geïnitialiseerd binnen uw AKS-cluster en gebruikmaken van een service account voor Tiller. Zorg ervoor dat u de nieuwste versie van helm gebruikt. Zie [helm install docs][helm-install](Engelstalig) voor upgrade-instructies. Zie [Installing Applications with helm in azure Kubernetes service (AKS) (Engelstalig)][use-helm]voor meer informatie over het configureren en gebruiken van helm.

Voor dit artikel moet u ook de Azure CLI-versie 2.0.64 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-an-ingress-controller"></a>Een ingangs controller maken

Standaard wordt een NGINX ingress-controller gemaakt met een nieuwe open bare IP-adres toewijzing. Dit open bare IP-adres is alleen statisch voor de levens duur van de ingangs controller en gaat verloren als de controller wordt verwijderd en opnieuw wordt gemaakt. Een algemene configuratie vereiste is om de NGINX ingress controller een bestaand statisch openbaar IP-adres te bieden. Het statische open bare IP-adres blijft aanwezig als de ingangs controller wordt verwijderd. Met deze aanpak kunt u bestaande DNS-records en netwerk configuraties op consistente wijze gebruiken gedurende de levens cyclus van uw toepassingen.

Als u een statisch openbaar IP-adres wilt maken, moet u eerst de naam van de resource groep van het AKS-cluster ophalen met de opdracht [AZ AKS show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Maak vervolgens een openbaar IP-adres met de *statische* toewijzings methode met behulp van de opdracht [AZ Network Public-IP Create][az-network-public-ip-create] . In het volgende voor beeld wordt een openbaar IP-adres gemaakt met de naam *myAKSPublicIP* in de AKS-cluster resource groep die u in de vorige stap hebt verkregen:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

Implementeer nu het *nginx-ingress-* grafiek met helm. Voeg de para meter `--set controller.service.loadBalancerIP` toe en geef uw eigen open bare IP-adres op dat u in de vorige stap hebt gemaakt. Voor toegevoegde redundantie worden twee replica's van de NGINX ingress-controllers geïmplementeerd met de para meter `--set controller.replicaCount`. Om volledig te profiteren van het uitvoeren van replica's van de ingangs controller, moet u ervoor zorgen dat er meer dan één knoop punt in uw AKS-cluster is.

De ingangs controller moet ook worden gepland op een Linux-knoop punt. Windows Server-knoop punten (momenteel in de preview-versie van AKS) mogen de ingangs controller niet uitvoeren. Een knooppunt kiezer wordt opgegeven met behulp van de para meter `--set nodeSelector` om de Kubernetes scheduler te laten weten dat de NGINX ingangs controller moet worden uitgevoerd op een Linux-knoop punt.

> [!TIP]
> In het volgende voor beeld wordt een Kubernetes-naam ruimte gemaakt voor de ingangs resources met de naam *ingress-Basic*. Geef waar nodig een naam ruimte op voor uw eigen omgeving. Als op uw AKS-cluster geen RBAC is ingeschakeld, voegt u `--set rbac.create=false` toe aan de helm-opdrachten.

> [!TIP]
> Als u [IP-behoud van client bronnen][client-source-ip] wilt inschakelen voor aanvragen voor containers in uw cluster, voegt u `--set controller.service.externalTrafficPolicy=Local` toe aan de helm-installatie opdracht. Het bron-IP-adres van de client wordt opgeslagen in de aanvraag header onder *X-doorgestuurd-voor*. Bij gebruik van een ingangs controller waarvoor IP-behoud door client bronnen is ingeschakeld, werkt SSL Pass-Through niet.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
```

Wanneer de Kubernetes-load balancer service is gemaakt voor de NGINX ingress-controller, wordt uw statische IP-adres toegewezen, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
dinky-panda-nginx-ingress-controller        LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
dinky-panda-nginx-ingress-default-backend   ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Er zijn nog geen regels voor binnenkomend verkeer gemaakt, dus de standaard 404-pagina van de NGINX ingress-controller wordt weer gegeven als u naar het open bare IP-adres bladert. Ingangs regels worden in de volgende stappen geconfigureerd.

## <a name="configure-a-dns-name"></a>Een DNS-naam configureren

Configureer een FQDN voor het IP-adres van de ingangs controller om de HTTPS-certificaten correct te laten werken. Werk het volgende script bij met het IP-adres van uw ingangs controller en een unieke naam die u voor de FQDN wilt gebruiken:

```azurecli-interactive
#!/bin/bash

# Public IP address of your ingress controller
IP="40.121.63.72"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

De ingangs controller is nu toegankelijk via de FQDN.

## <a name="install-cert-manager"></a>Certificaat beheerder installeren

De NGINX ingress-controller ondersteunt TLS-beëindiging. Er zijn verschillende manieren om certificaten voor HTTPS op te halen en te configureren. In dit artikel wordt gedemonstreerd met behulp van [CERT-beheer][cert-manager], waarmee automatisch de functionaliteit voor het genereren en beheren van certificaten [kan worden versleuteld][lets-encrypt] .

> [!NOTE]
> In dit artikel wordt gebruikgemaakt van de `staging`-omgeving om te versleutelen. In productie-implementaties gebruikt u `letsencrypt-prod` en `https://acme-v02.api.letsencrypt.org/directory` in de resource definities en bij de installatie van de helm-grafiek.

Gebruik de volgende `helm install` opdracht voor het installeren van de CERT-beheer controller in een cluster met RBAC-functionaliteit:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.11/deploy/manifests/00-crds.yaml

# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  --name cert-manager \
  --namespace cert-manager \
  --version v0.11.0 \
  jetstack/cert-manager
```

Zie het [project certificaat beheer][cert-manager]voor meer informatie over de configuratie van certificaat beheer.

## <a name="create-a-ca-cluster-issuer"></a>Een certificerings instantie voor een CA-cluster maken

Voordat certificaten kunnen worden verleend, vereist CERT-Manager een [verlener][cert-manager-issuer] -of [ClusterIssuer][cert-manager-cluster-issuer] -resource. Deze Kubernetes-resources zijn identiek in de functionaliteit, maar `Issuer` werkt in één naam ruimte en `ClusterIssuer` werkt in alle naam ruimten. Zie de documentatie van de [certificaat beheerder][cert-manager-issuer] voor meer informatie.

Maak een cluster Uitgever, zoals `cluster-issuer.yaml`, met behulp van het volgende voor beeld-manifest. Werk het e-mail adres bij met een geldig adres van uw organisatie:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
  namespace: ingress-basic
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Gebruik de opdracht `kubectl apply -f cluster-issuer.yaml` om de certificaat verlener te maken.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Demo toepassingen uitvoeren

Er is een ingangs controller en een oplossing voor certificaat beheer geconfigureerd. We gaan nu twee demonstratie toepassingen uitvoeren in uw AKS-cluster. In dit voor beeld wordt helm gebruikt om twee exemplaren van een eenvoudige ' Hallo wereld '-toepassing te implementeren.

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

Beide toepassingen worden nu uitgevoerd op uw Kubernetes-cluster, maar ze zijn geconfigureerd met een service van het type `ClusterIP`. De toepassingen zijn dus niet toegankelijk via internet. Als u ze openbaar beschikbaar wilt maken, maakt u een Kubernetes-ingangs bron. De ingangs resource configureert de regels die verkeer routeren naar een van de twee toepassingen.

In het volgende voor beeld wordt verkeer naar het adres `https://demo-aks-ingress.eastus.cloudapp.azure.com/` doorgestuurd naar de service met de naam `aks-helloworld`. Verkeer naar het adres `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` wordt doorgestuurd naar de `ingress-demo`-service. Werk de *hosts* en *host* bij naar de DNS-naam die u in een vorige stap hebt gemaakt.

Maak een bestand met de naam `hello-world-ingress.yaml` en kopieer het in het volgende voor beeld YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
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

Maak de ingangs resource met behulp van de opdracht `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Een certificaat object maken

Vervolgens moet er een certificaat bron worden gemaakt. De certificaat resource definieert het gewenste X. 509-certificaat. Zie [certificaten certificaat beheer][cert-manager-certificates]voor meer informatie.

CERT-Manager heeft waarschijnlijk automatisch een certificaat object voor u gemaakt met behulp van ingress-Shim, dat automatisch wordt geïmplementeerd met CERT-Manager sinds v 0.2.2. Zie de documentatie van de inkomende [shim][ingress-shim]voor meer informatie.

Gebruik de opdracht `kubectl describe certificate tls-secret --namespace ingress-basic` om te controleren of het certificaat is gemaakt.

Als het certificaat is uitgegeven, ziet u uitvoer die vergelijkbaar is met de volgende:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Als u een extra certificaat bron wilt maken, kunt u dit doen met het volgende voor beeld-manifest. Werk de *dnsNames* en *domeinen* bij naar de DNS-naam die u in een vorige stap hebt gemaakt. Als u een interne ingangs controller gebruikt, geeft u de interne DNS-naam voor uw service op.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Gebruik de opdracht `kubectl apply -f certificates.yaml` om de certificaat bron te maken.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>De ingangs configuratie testen

Open een webbrowser naar de FQDN van uw Kubernetes ingress-controller, zoals *https://demo-aks-ingress.eastus.cloudapp.azure.com* .

Zoals deze voor beelden `letsencrypt-staging`gebruiken, wordt het uitgegeven SSL-certificaat niet vertrouwd door de browser. Accepteer de waarschuwing om door te gaan naar uw toepassing. In de certificaat informatie ziet u dat dit *valse Le-tussenliggend x1* -certificaat wordt uitgegeven door de code ring. Dit valse certificaat geeft aan `cert-manager` de aanvraag correct verwerkt en een certificaat van de provider ontvangen:

![Laten we het faserings certificaat versleutelen](media/ingress/staging-certificate.png)

Wanneer u het versleutelen van de code voor het gebruik van `prod` in plaats van `staging`wijzigt, wordt een vertrouwd certificaat gebruikt dat is uitgegeven door de versleutelings module, zoals wordt weer gegeven in het volgende voor beeld:

![Certificaat versleutelen](media/ingress/certificate.png)

De demo toepassing wordt weer gegeven in de webbrowser:

![Voor beeld van toepassing 1](media/ingress/app-one.png)

Voeg nu het */Hello-World-Two* -pad toe aan de FQDN, zoals *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two* . De tweede demo toepassing met de aangepaste titel wordt weer gegeven:

![Voor beeld van toepassing twee](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel wordt helm gebruikt om de ingangs onderdelen, certificaten en voor beeld-apps te installeren. Wanneer u een helm-grafiek implementeert, worden er een aantal Kubernetes-resources gemaakt. Deze resources omvatten peulen, implementaties en services. Als u deze resources wilt opschonen, kunt u de volledige voorbeeld naam ruimte of de afzonderlijke resources verwijderen.

### <a name="delete-the-sample-namespace-and-all-resources"></a>De voorbeeld naam ruimte en alle resources verwijderen

Als u de volledige voorbeeld naam ruimte wilt verwijderen, gebruikt u de opdracht `kubectl delete` en geeft u de naam van de naam ruimte op. Alle resources in de naam ruimte worden verwijderd.

```console
kubectl delete namespace ingress-basic
```

Verwijder vervolgens de helm-opslag plaats voor de AKS Hallo wereld-app:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Resources afzonderlijk verwijderen

U kunt ook een nauw keurigere benadering van de gemaakte afzonderlijke resources verwijderen. Verwijder eerst de certificaat resources:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Vermeld nu de helm-releases met de opdracht `helm list`. Zoek naar grafieken met de naam *nginx-ingang*, *CERT-Manager*en *AKS-HelloWorld*, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
waxen-hamster           1           Wed Mar  6 23:16:00 2019    DEPLOYED    nginx-ingress-1.3.1   0.22.0        kube-system
alliterating-peacock    1           Wed Mar  6 23:17:37 2019    DEPLOYED    cert-manager-v0.6.6     v0.6.2      kube-system
mollified-armadillo     1           Wed Mar  6 23:26:04 2019    DEPLOYED    aks-helloworld-0.1.0                default
wondering-clam          1           Wed Mar  6 23:26:07 2019    DEPLOYED    aks-helloworld-0.1.0                default
```

Verwijder de releases met de opdracht `helm delete`. In het volgende voor beeld worden de NGINX ingress-implementatie, certificaat beheerder en de twee voor beelden van de AKS Hello World-apps verwijderd.

```
$ helm delete waxen-hamster alliterating-peacock mollified-armadillo wondering-clam

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Verwijder vervolgens de helm-opslag plaats voor de AKS Hallo wereld-app:

```console
helm repo remove azure-samples
```

Verwijder de ingangs route die het verkeer naar de voor beeld-apps doorstuurt:

```console
kubectl delete -f hello-world-ingress.yaml
```

Verwijder de zelf naam ruimte. Gebruik de `kubectl delete` opdracht en geef de naam van de naam ruimte op:

```console
kubectl delete namespace ingress-basic
```

Ten slotte verwijdert u het statische open bare IP-adres dat is gemaakt voor de ingangs controller. Geef de naam van uw *MC_* cluster resource groep op die u in de eerste stap van dit artikel hebt verkregen, zoals *MC_myResourceGroup_myAKSCluster_eastus*:

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel zijn enkele externe onderdelen opgenomen in AKS. Zie de volgende project pagina's voor meer informatie over deze onderdelen:

- [Helm CLI][helm-cli]
- [NGINX ingress-controller][nginx-ingress]
- [cert-manager][cert-manager]

U kunt ook het volgende doen:

- [Een eenvoudige ingangs controller met externe netwerk verbinding maken][aks-ingress-basic]
- [De invoeg toepassing voor het routeren van HTTP-toepassingen inschakelen][aks-http-app-routing]
- [Een ingangs controller maken die gebruikmaakt van een intern, privé netwerk en IP-adres][aks-ingress-internal]
- [Een ingangs controller maken die gebruikmaakt van uw eigen TLS-certificaten][aks-ingress-own-tls]
- [Een ingangs controller maken met een dynamisch openbaar IP-adres en configureren laten versleutelen om automatisch TLS-certificaten te genereren][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
