---
title: Een HTTP-ingress-controller maken met een statisch IP-adres in Azure Kubernetes Service (AKS)
description: Meer informatie over het installeren en configureren van een NGINX-ingress-controller met een statisch openbaar IP-adres in een AKS-cluster (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 10422595b85c71020225df694778e6b8ae7e0185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191347"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Een invallende controller maken met een statisch openbaar IP-adres in Azure Kubernetes Service (AKS)

Een controller voor inkomend verkeer is een stukje software dat omgekeerde proxy’s, configureerbare verkeersroutering en TLS-beëindiging voor Kubernetes-services biedt. Kubernetes-resources voor inkomend verkeer worden gebruikt om de regels en routes voor uitgaand verkeer worden geconfigureerd voor individuele Kubernetes-services. Met behulp van een controller en regels voor inkomend verkeer kan er één enkel IP-adres worden gebruikt voor het routeren van verkeer naar meerdere services in een Kubernetes-cluster.

In dit artikel ziet u hoe u de [NGINX-ingress-controller implementeert][nginx-ingress] in een AKS-cluster (Azure Kubernetes Service). De invallende controller is geconfigureerd met een statisch openbaar IP-adres. Het [cert-manager-project][cert-manager] wordt gebruikt om [Let's Encrypt-certificaten][lets-encrypt] automatisch te genereren en te configureren. Ten slotte worden twee toepassingen uitgevoerd in het AKS-cluster, die elk toegankelijk zijn via één IP-adres.

U kunt ook het volgende doen:

- [Een basis-ingress-controller maken met externe netwerkconnectiviteit][aks-ingress-basic]
- [De invoegtoepassing HTTP-toepassingsroutering inschakelen][aks-http-app-routing]
- [Een ingress-controller maken die uw eigen TLS-certificaten gebruikt][aks-ingress-own-tls]
- [Een invallende controller maken die Let's Encrypt gebruikt om automatisch TLS-certificaten te genereren met een dynamisch openbaar IP-adres][aks-ingress-tls]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

In dit artikel wordt Helm gebruikt om de NGINX-ingress-controller, cert-manager en een voorbeeldweb-app te installeren. Zorg ervoor dat u de nieuwste versie van Helm gebruikt. Zie De [installatiedocumenten van Het helm voor][helm-install]upgrade-instructies . Zie [Toepassingen installeren met Helm in Azure Kubernetes Service (AKS) voor][use-helm]meer informatie over het configureren en gebruiken van Helm.

In dit artikel moet u ook de Azure CLI-versie 2.0.64 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Een invallende controller maken

Standaard wordt een NGINX-ingress-controller gemaakt met een nieuwe openbare IP-adrestoewijzing. Dit openbare IP-adres is alleen statisch voor de levensduur van de invallende controller en gaat verloren als de controller wordt verwijderd en opnieuw wordt gemaakt. Een veelvoorkomende configuratievereiste is om de NGINX-ingress-controller een bestaand statisch openbaar IP-adres te bieden. Het statische openbare IP-adres blijft behouden als de invallende controller wordt verwijderd. Met deze aanpak u bestaande DNS-records en netwerkconfiguraties op een consistente manier gebruiken gedurende de levenscyclus van uw toepassingen.

Als u een statisch openbaar IP-adres wilt maken, krijgt u eerst de naam van de brongroep van het AKS-cluster met de opdracht [AZ AKS Show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Maak vervolgens een openbaar IP-adres met de *statische* toewijzingsmethode met de opdracht [Public-IP create van het AZ-netwerk.][az-network-public-ip-create] In het volgende voorbeeld wordt een openbaar IP-adres met de naam *myAKSPublicIP* gemaakt in de AKS-clusterbrongroep die in de vorige stap is verkregen:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

Implementeer nu de *nginx-ingress* grafiek met Helm. Voeg `--set controller.service.loadBalancerIP` de parameter toe en geef uw eigen openbare IP-adres op dat in de vorige stap is gemaakt. Voor toegevoegde redundantie worden er twee replica's van de NGINX-ingangscontrollers geïmplementeerd met de parameter `--set controller.replicaCount`. Als u optimaal wilt profiteren van het uitvoeren van replica's van de invallende controller, moet u ervoor zorgen dat er meer dan één knooppunt in uw AKS-cluster is.

De ingangscontroller moet ook worden gepland op een Linux-knooppunt. Windows Server-knooppunten (momenteel in preview in AKS) mogen de invallende controller niet uitvoeren. Er wordt een knooppuntselector opgegeven met behulp van de parameter `--set nodeSelector` om de Kubernetes-planner te laten weten dat de NGINX-ingangscontroller moet worden uitgevoerd op een Linux-knooppunt.

> [!TIP]
> In het volgende voorbeeld wordt een Kubernetes-naamruimte voor de invallende resources met de naam *ingress-basic.* Geef indien nodig een naamruimte op voor uw eigen omgeving. Als uw AKS-cluster niet is `--set rbac.create=false` ingeschakeld, voegt u de opdrachten Helm toe.

> [!TIP]
> Als u [IP-behoud van clientbron][client-source-ip] wilt inschakelen voor `--set controller.service.externalTrafficPolicy=Local` aanvragen voor containers in uw cluster, voegt u de installatieopdracht Helm toe. Het IP-adres van de clientbron wordt opgeslagen in de aanvraagkoptekst onder *X-Forwarded-For*. Wanneer een inbinnendringen-controller wordt gebruikt waarbij clientsource IP-behoud is ingeschakeld, werkt SSL-doorgeefservice niet.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
```

Wanneer de Kubernetes load balancer-service wordt gemaakt voor de NGINX-ingress-controller, wordt uw statische IP-adres toegewezen, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
nginx-ingress-controller                    LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
nginx-ingress-default-backend               ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Er zijn nog geen invallenregels gemaakt, dus de standaard 404-pagina van de NGINX-ingress-controller wordt weergegeven als u naar het openbare IP-adres bladert. Invallenregels worden geconfigureerd in de volgende stappen.

## <a name="configure-a-dns-name"></a>Een DNS-naam configureren

Als u wilt dat de HTTPS-certificaten correct werken, configureert u een FQDN voor het IP-adres van de invallende controller. Werk het volgende script bij met het IP-adres van uw invallende controller en een unieke naam die u wilt gebruiken voor de FQDN:

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

De invallende controller is nu toegankelijk via de FQDN.

## <a name="install-cert-manager"></a>Certificaatbeheer installeren

De NGINX-ingangscontroller ondersteunt TLS-beëindiging. Er zijn verschillende manieren om certificaten voor HTTPS op te halen en te configureren. Dit artikel toont het gebruik van [cert-manager][cert-manager], die automatische [Lets Encrypt][lets-encrypt] certificaat generatie en beheer functionaliteit biedt.

> [!NOTE]
> In dit `staging` artikel wordt de omgeving gebruikt voor Let's Encrypt. Gebruik en bij `letsencrypt-prod` het `https://acme-v02.api.letsencrypt.org/directory` installeren van de helmgrafiek in productie-implementaties en in de resourcedefinities.

Als u de cert-managercontroller in een cluster met `helm install` RBAC wilt installeren, gebruikt u de volgende opdracht:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the cert-manager namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.13.0 \
  jetstack/cert-manager
```

Zie voor meer informatie over de configuratie van cert-manager het [cert-manager project.][cert-manager]

## <a name="create-a-ca-cluster-issuer"></a>Een CA-clusteruitgever maken

Voordat certificaten kunnen worden uitgegeven, heeft cert-manager een [emittent][cert-manager-issuer] of [clusterissuer-bron][cert-manager-cluster-issuer] nodig. Deze Kubernetes-bronnen zijn identiek `Issuer` in functionaliteit, werken echter `ClusterIssuer` in één naamruimte en werken in alle naamruimten. Zie voor meer informatie de documentatie van de [cert-manager-emittent.][cert-manager-issuer]

Maak een clusteruitgever, `cluster-issuer.yaml`zoals , met behulp van het volgende voorbeeldmanifest. Werk het e-mailadres bij met een geldig adres van uw organisatie:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
```

Als u de uitgever `kubectl apply -f cluster-issuer.yaml` wilt maken, gebruikt u de opdracht.

```
$ kubectl apply -f cluster-issuer.yaml --namespace ingress-basic

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Demotoepassingen uitvoeren

Een invallende controller en een certificaatbeheeroplossing zijn geconfigureerd. Laten we nu twee demotoepassingen uitvoeren in uw AKS-cluster. In dit voorbeeld wordt Helm gebruikt om twee exemplaren van een eenvoudige 'Hello world'-toepassing te implementeren.

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
helm install aks-helloworld-2 azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Een invallende route maken

Beide toepassingen worden nu uitgevoerd op uw Kubernetes-cluster, maar `ClusterIP`ze zijn geconfigureerd met een service van het type . Als zodanig zijn de toepassingen niet toegankelijk vanaf het internet. Maak een Kubernetes-ingress-bron om ze openbaar te maken. De binnenkomende bron configureert de regels die verkeer doorsturen naar een van de twee toepassingen.

In het volgende voorbeeld wordt `https://demo-aks-ingress.eastus.cloudapp.azure.com/` het verkeer naar `aks-helloworld`het adres doorgestuurd naar de service met de naam . Verkeer naar `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` het adres wordt `ingress-demo` doorgestuurd naar de service. Werk de *hosts* en *host* bij naar de DNS-naam die u in een vorige stap hebt gemaakt.

Maak een `hello-world-ingress.yaml` bestand met de naam en kopie in het volgende voorbeeld YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
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

Maak de binnenkomende `kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic` bron met de opdracht.

```
$ kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Een certificaatobject maken

Vervolgens moet een certificaatbron worden gemaakt. De certificaatbron definieert het gewenste X.509-certificaat. Zie [cert-manager certificaten][cert-manager-certificates]voor meer informatie.

Cert-manager heeft waarschijnlijk automatisch een certificaatobject voor u gemaakt met invallen, dat sinds v0.2.2 automatisch wordt geïmplementeerd bij cert-manager. Zie voor meer informatie de [documentatie van de ingress-shim.][ingress-shim]

Als u wilt controleren of het `kubectl describe certificate tls-secret --namespace ingress-basic` certificaat is gemaakt, gebruikt u de opdracht.

Als het certificaat is uitgegeven, ziet u de uitvoer vergelijkbaar met de volgende:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Als u een extra certificaatbron moet maken, u dit doen met het volgende voorbeeldmanifest. Werk de *dnsNames* en *domeinen* bij naar de DNS-naam die u in een vorige stap hebt gemaakt. Als u een interne interne insingress-controller gebruikt, geeft u de interne DNS-naam voor uw service op.

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

Als u de certificaatbron `kubectl apply -f certificates.yaml` wilt maken, gebruikt u de opdracht.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>De insi-configuratie testen

Open een webbrowser voor de FQDN van uw *https://demo-aks-ingress.eastus.cloudapp.azure.com*Kubernetes-ingress-controller, zoals .

Zoals deze voorbeelden `letsencrypt-staging`gebruiken, wordt het uitgegeven SSL-certificaat niet vertrouwd door de browser. Accepteer de waarschuwingsprompt om door te gaan naar uw aanvraag. De certificaatgegevens tonen dat dit *Fake LE Intermediate X1-certificaat* is uitgegeven door Let's Encrypt. Dit valse `cert-manager` certificaat geeft aan de aanvraag correct te hebben verwerkt en een certificaat van de aanbieder te hebben ontvangen:

![Certificaat voor faseringsfase versleutelen](media/ingress/staging-certificate.png)

Wanneer u Let's Encrypt `prod` wijzigt in gebruik in plaats `staging`van dat er een vertrouwd certificaat wordt gebruikt dat is uitgegeven door Let's Encrypt, wordt een vertrouwde certificaat gebruikt, zoals in het volgende voorbeeld wordt weergegeven:

![Certificaat versleutelen](media/ingress/certificate.png)

De demo-applicatie wordt weergegeven in de webbrowser:

![Voorbeeld van toepassing één](media/ingress/app-one.png)

Voeg nu het */hello-world-two pad* toe aan *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*de FQDN, zoals . De tweede demo-toepassing met de aangepaste titel wordt weergegeven:

![Voorbeeld twee van toepassing](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel is Helm gebruikt om de binnenkomende componenten, certificaten en voorbeeld-apps te installeren. Wanneer u een Helm-diagram implementeert, worden een aantal Kubernetes-bronnen gemaakt. Deze bronnen omvatten pods, implementaties en services. Als u deze resources wilt opschonen, u de volledige naamruimte van het voorbeeld of de afzonderlijke resources verwijderen.

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

Een meer gedetailleerde benadering is ook het verwijderen van de afzonderlijke bronnen die zijn gemaakt. Verwijder eerst de certificaatbronnen:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Vermeld nu de Helm `helm list` releases met het commando. Zoek naar grafieken met de naam *nginx-ingress,* *cert-manager*en *aks-helloworld,* zoals weergegeven in de volgende voorbeelduitvoer:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-11 15:02:21.51172346   deployed        aks-helloworld-0.1.0
aks-helloworld-2        ingress-basic   1               2020-01-11 15:03:10.533465598  deployed        aks-helloworld-0.1.0
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic    1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Verwijder de releases `helm uninstall` met de opdracht. In het volgende voorbeeld worden de NGINX-ingress-implementatie, certificaatbeheer en de twee voorbeeld-AKS hello world-apps verwijderd.

```
$ helm uninstall aks-helloworld aks-helloworld-2 nginx-ingress cert-manager -n ingress-basic

release "aks-helloworld" deleted
release "aks-helloworld-2" deleted
release "nginx-ingress" deleted
release "cert-manager" deleted
```

Verwijder vervolgens de Helm repo voor de AKS hello world app:

```console
helm repo remove azure-samples
```

Verwijder de naamruimte zelf. Gebruik `kubectl delete` de opdracht en geef de naam naam van de naamruimte op:

```console
kubectl delete namespace ingress-basic
```

Verwijder ten slotte het statische openbare IP-adres dat is gemaakt voor de invallende controller. Geef uw *MC_* clusterbrongroepnaam op die in de eerste stap van dit artikel is verkregen, zoals *MC_myResourceGroup_myAKSCluster_eastus:*

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Volgende stappen

Dit artikel bevat een aantal externe componenten van AKS. Zie de volgende projectpagina's voor meer informatie over deze onderdelen:

- [Helm CLI][helm-cli]
- [NGINX-ingress-controller][nginx-ingress]
- [cert-manager][cert-manager]

U kunt ook het volgende doen:

- [Een basis-ingress-controller maken met externe netwerkconnectiviteit][aks-ingress-basic]
- [De invoegtoepassing HTTP-toepassingsroutering inschakelen][aks-http-app-routing]
- [Een invallende controller maken die een intern, privénetwerk en IP-adres gebruikt][aks-ingress-internal]
- [Een ingress-controller maken die uw eigen TLS-certificaten gebruikt][aks-ingress-own-tls]
- [Maak een invallende controller met een dynamisch openbaar IP-adres en configureer Let's Encrypt om automatisch TLS-certificaten te genereren][aks-ingress-tls]

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
