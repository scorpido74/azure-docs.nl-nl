---
title: Een HTTPS-binnendringen maken met het AKS-cluster (Azure Kubernetes Service)
description: Meer informatie over het installeren en configureren van een NGINX-ingress-controller die Let's Encrypt gebruikt voor het genereren van automatische TLS-certificaten in een AKS-cluster (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: ece117d60ae3d7fd70c0972bb463340c5d38c9e1
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637266"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Een HTTPS-ingress-controller maken op Azure Kubernetes Service (AKS)

Een controller voor inkomend verkeer is een stukje software dat omgekeerde proxy’s, configureerbare verkeersroutering en TLS-beëindiging voor Kubernetes-services biedt. Kubernetes-resources voor inkomend verkeer worden gebruikt om de regels en routes voor uitgaand verkeer worden geconfigureerd voor individuele Kubernetes-services. Met behulp van een controller en regels voor inkomend verkeer kan er één enkel IP-adres worden gebruikt voor het routeren van verkeer naar meerdere services in een Kubernetes-cluster.

In dit artikel ziet u hoe u de [NGINX-ingress-controller implementeert][nginx-ingress] in een AKS-cluster (Azure Kubernetes Service). Het [cert-manager-project][cert-manager] wordt gebruikt om [Let's Encrypt-certificaten][lets-encrypt] automatisch te genereren en te configureren. Ten slotte worden twee toepassingen uitgevoerd in het AKS-cluster, die elk toegankelijk zijn via één IP-adres.

U kunt ook het volgende doen:

- [Een basis-ingress-controller maken met externe netwerkconnectiviteit][aks-ingress-basic]
- [De invoegtoepassing HTTP-toepassingsroutering inschakelen][aks-http-app-routing]
- [Een invallende controller maken die een intern, privénetwerk en IP-adres gebruikt][aks-ingress-internal]
- [Een ingress-controller maken die uw eigen TLS-certificaten gebruikt][aks-ingress-own-tls]
- [Een invallende controller maken die Let's Encrypt gebruikt om automatisch TLS-certificaten te genereren met een statisch openbaar IP-adres][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

In dit artikel wordt ook ervan uitgegaan dat u [een aangepast domein][custom-domain] hebt met een [DNS-zone][dns-zone] in dezelfde brongroep als uw AKS-cluster.

In dit artikel wordt Helm gebruikt om de NGINX-ingress-controller, cert-manager en een voorbeeldweb-app te installeren. Zorg ervoor dat u de nieuwste versie van Helm gebruikt. Zie De [installatiedocumenten van Het helm voor][helm-install]upgrade-instructies . Zie [Toepassingen installeren met Helm in Azure Kubernetes Service (AKS) voor][use-helm]meer informatie over het configureren en gebruiken van Helm.

In dit artikel moet u ook de Azure CLI-versie 2.0.64 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Een invallende controller maken

Als u de insteekcontroller `helm` wilt maken, gebruikt u de opdracht om *nginx-ingress*te installeren. Voor toegevoegde redundantie worden er twee replica's van de NGINX-ingangscontrollers geïmplementeerd met de parameter `--set controller.replicaCount`. Als u optimaal wilt profiteren van het uitvoeren van replica's van de invallende controller, moet u ervoor zorgen dat er meer dan één knooppunt in uw AKS-cluster is.

De ingangscontroller moet ook worden gepland op een Linux-knooppunt. Windows Server-knooppunten (momenteel in preview in AKS) mogen de invallende controller niet uitvoeren. Er wordt een knooppuntselector opgegeven met behulp van de parameter `--set nodeSelector` om de Kubernetes-planner te laten weten dat de NGINX-ingangscontroller moet worden uitgevoerd op een Linux-knooppunt.

> [!TIP]
> In het volgende voorbeeld wordt een Kubernetes-naamruimte voor de invallende resources met de naam *ingress-basic.* Geef indien nodig een naamruimte op voor uw eigen omgeving.

> [!TIP]
> Als u [IP-behoud van clientbron][client-source-ip] wilt inschakelen voor `--set controller.service.externalTrafficPolicy=Local` aanvragen voor containers in uw cluster, voegt u de installatieopdracht Helm toe. Het IP-adres van de clientbron wordt opgeslagen in de aanvraagkoptekst onder *X-Forwarded-For*. Wanneer een inbinnendringen-controller wordt gebruikt waarbij clientsource IP-behoud is ingeschakeld, werkt SSL-doorgeefservice niet.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repo
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Tijdens de installatie wordt een Openbaar IP-adres van Azure gemaakt voor de invallende controller. Dit openbare IP-adres is statisch voor de levensduur van de invallende controller. Als u de invallende controller verwijdert, gaat de toewijzing van het openbare IP-adres verloren. Als u vervolgens een extra invallende controller maakt, wordt een nieuw openbaar IP-adres toegewezen. Als u het gebruik van het openbare IP-adres wilt behouden, u in plaats daarvan [een invallende controller maken met een statisch openbaar IP-adres.][aks-ingress-static-tls]

Als u het openbare IP-adres wilt krijgen, gebruikt u de `kubectl get service` opdracht. Het duurt enkele minuten voordat het IP-adres aan de service is toegewezen.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
nginx-ingress-controller                         LoadBalancer   10.0.182.160   MY_EXTERNAL_IP  80:30920/TCP,443:30426/TCP   20m
nginx-ingress-default-backend                    ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Er zijn nog geen invallenregels gemaakt. Als u naar het openbare IP-adres bladert, wordt de standaard 404-pagina van de NGINX-ingress-controller weergegeven.

## <a name="add-an-a-record-to-your-dns-zone"></a>Een A-record toevoegen aan uw DNS-zone

Voeg een *A-record* toe aan uw DNS-zone met het externe IP-adres van de NGINX-service met behulp van [dns-record van het AZ-netwerk.][az-network-dns-record-set-a-add-record]

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name '*' \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Optioneel u een FQDN configureren voor het IP-adres van de invallende controller in plaats van een aangepast domein. Houd er rekening mee dat dit monster is voor een Bash shell.
> 
> ```azurecli-interactive
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>Certificaatbeheer installeren

De NGINX-ingangscontroller ondersteunt TLS-beëindiging. Er zijn verschillende manieren om certificaten voor HTTPS op te halen en te configureren. Dit artikel toont het gebruik van [cert-manager][cert-manager], die automatische [Lets Encrypt][lets-encrypt] certificaat generatie en beheer functionaliteit biedt.

Ga als u de cert-managercontroller installeren:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the ingress-basic namespace to disable resource validation
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
          class: nginx
```

Als u de uitgever `kubectl apply` wilt maken, gebruikt u de opdracht.

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Demotoepassingen uitvoeren

Een invallende controller en een certificaatbeheeroplossing zijn geconfigureerd. Laten we nu twee demotoepassingen uitvoeren in uw AKS-cluster. In dit voorbeeld wordt Helm gebruikt om twee exemplaren van een eenvoudige *Hello-wereldtoepassing* te implementeren.

Voordat u de voorbeeldgrafieken van helm installeren, voegt u de Azure-objectschatarchief toe aan uw Helm-omgeving.

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Maak een demo-applicatie met de naam *aks-helloworld* met behulp van de *azure-samples/aks-helloworld* Helm grafiek.

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Maak een tweede instantie van de demo-applicatie met de naam *aks-helloworld-two*. Geef een nieuwe titel en unieke servicenaam op, zodat de twee toepassingen visueel verschillend zijn met behulp van *--set*.

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Een invallende route maken

Beide toepassingen worden nu uitgevoerd op uw Kubernetes-cluster. Ze zijn echter geconfigureerd met een `ClusterIP` service van het type en zijn niet toegankelijk vanaf het internet. Maak een Kubernetes-ingress-bron om ze openbaar te maken. De binnenkomende bron configureert de regels die verkeer doorsturen naar een van de twee toepassingen.

In het volgende voorbeeld gaat u naar het adres *hello-world-ingress. MY_CUSTOM_DOMAIN* wordt doorgestuurd naar de *aks-helloworld* service. Verkeer naar het adres *hello-world-ingress. MY_CUSTOM_DOMAIN/hello-world-two* wordt doorgestuurd naar de *aks-helloworld-twee* service. Verkeer naar *hello-world-ingress. MY_CUSTOM_DOMAIN/statisch* wordt doorgestuurd naar de service met de naam *aks-helloworld* voor statische assets.

Maak een `hello-world-ingress.yaml` bestand met de naam met de onderstaande voorbeeld YAML. Werk de *hosts* en *host* bij naar de DNS-naam die u in een vorige stap hebt gemaakt.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
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
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

Maak de binnenkomende `kubectl apply` bron met de opdracht.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Controleren of er een certificaatobject is gemaakt

Vervolgens moet een certificaatbron worden gemaakt. De certificaatbron definieert het gewenste X.509-certificaat. Zie [cert-manager certificaten][cert-manager-certificates]voor meer informatie. Cert-manager heeft automatisch een certificaatobject voor u gemaakt met invallen, dat sinds v0.2.2 automatisch wordt geïmplementeerd bij cert-manager. Zie voor meer informatie de [documentatie van de ingress-shim.][ingress-shim]

Als u wilt controleren of het `kubectl get certificate --namespace ingress-basic` certificaat is gemaakt, gebruikt u de opdracht en controleert *u dat READY* true is *,* wat enkele minuten kan duren.

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>De insi-configuratie testen

Open een webbrowser voor *hello-world-ingress. MY_CUSTOM_DOMAIN* van uw Kubernetes ingress controller. Merk op dat u https wilt gebruiken en het certificaat wordt vertrouwd en de demo-toepassing wordt weergegeven in de webbrowser. Voeg het */hello-world-two pad* toe en let op dat de tweede demotoepassing met de aangepaste titel wordt weergegeven.

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

Een meer gedetailleerde benadering is ook het verwijderen van de afzonderlijke bronnen die zijn gemaakt. Verwijder eerst de bronnen van de clusteruitgevende instelling:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Lijst van de `helm list` Helm releases met de opdracht. Zoek naar grafieken met de naam *nginx-ingress* en *aks-helloworld,* zoals weergegeven in de volgende voorbeelduitvoer:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-15 10:24:32.054871 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-15 10:24:37.671667 -0600 CST    deployed        aks-helloworld-0.1.0               
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Verwijder de releases `helm delete` met de opdracht. In het volgende voorbeeld wordt de NGINX-implementatie verwijderd en worden de twee voorbeeld-AKS hello world-apps verwijderd.

```
$ helm uninstall aks-helloworld aks-helloworld-two cert-manager nginx --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "cert-manager" uninstalled
release "nginx" uninstalled
```

Verwijder vervolgens de Helm repo voor de AKS hello world app:

```console
helm repo remove azure-samples
```

Verwijder de invallende route die verkeer naar de voorbeeld-apps leidde:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Ten slotte u de naamruimte zelf verwijderen. Gebruik `kubectl delete` de opdracht en geef de naam naam van de naamruimte op:

```console
kubectl delete namespace ingress-basic
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
- [Een invallende controller maken die Let's Encrypt gebruikt om automatisch TLS-certificaten te genereren met een statisch openbaar IP-adres][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
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
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
