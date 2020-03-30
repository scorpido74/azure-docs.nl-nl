---
title: Aanbevolen procedures voor operatoren - Netwerkconnectiviteit in Azure Kubernetes Services (AKS)
description: Lees de aanbevolen procedures voor de clusteroperator voor virtuele netwerkbronnen en connectiviteit in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 93659a0891b09c83db9f63fe0756fcf4d7e87f6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594682"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Best practices voor netwerkverbinding en -beveiliging in Azure Kubernetes Service (AKS)

Terwijl u clusters maakt en beheert in Azure Kubernetes Service (AKS), biedt u netwerkconnectiviteit voor uw knooppunten en toepassingen. Deze netwerkbronnen omvatten IP-adresbereiken, load balancers en ingress-controllers. Als u een hoge kwaliteit van de service voor uw toepassingen wilt behouden, moet u deze resources plannen en vervolgens configureren.

Dit best practices-artikel richt zich op netwerkconnectiviteit en beveiliging voor clusteroperators. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * De kubenet- en Azure CNI-netwerkmodi vergelijken in AKS
> * Plannen voor vereiste IP-adressering en connectiviteit
> * Verkeer distribueren met behulp van load balancers, ingress-controllers of een firewall voor webtoepassingen (WAF)
> * Veilig verbinding maken met clusterknooppunten

## <a name="choose-the-appropriate-network-model"></a>Kies het juiste netwerkmodel

**Richtlijnen voor aanbevolen procedures** - Voor integratie met bestaande virtuele netwerken of on-premises netwerken gebruikt u Azure CNI-netwerken in AKS. Dit netwerkmodel maakt ook een grotere scheiding van resources en besturingselementen in een bedrijfsomgeving mogelijk.

Virtuele netwerken bieden de basisconnectiviteit voor AKS-knooppunten en klanten om toegang te krijgen tot uw toepassingen. Er zijn twee verschillende manieren om AKS-clusters in virtuele netwerken te implementeren:

* **Kubenet-netwerken** - Azure beheert de virtuele netwerkbronnen terwijl het cluster wordt geïmplementeerd en maakt gebruik van de [kubenet][kubenet] Kubernetes-plug-in.
* **Azure CNI-netwerken** - Implementeert in een bestaand virtueel netwerk en maakt gebruik van de [CNI-plug-in Azure Container Networking Interface (CNI).][cni-networking] Pods ontvangen afzonderlijke IP's die kunnen worden doorgestuurd naar andere netwerkservices of on-premises bronnen.

De Container Networking Interface (CNI) is een leveranciersneutraal protocol waarmee de containerruntime aanvragen kan indienen bij een netwerkprovider. De Azure CNI wijst IP-adressen toe aan pods en knooppunten en biedt IPAM-functies (IP-adresbeheer) terwijl u verbinding maakt met bestaande virtuele Azure-netwerken. Elke knooppunt- en podbron ontvangt een IP-adres in het virtuele Azure-netwerk en er is geen extra routering nodig om te communiceren met andere bronnen of services.

![Diagram met twee knooppunten met bruggen die elk verbinden met één Azure VNet](media/operator-best-practices-network/advanced-networking-diagram.png)

Voor de meeste productie-implementaties moet u Azure CNI-netwerken gebruiken. Dit netwerkmodel maakt het mogelijk om de controle en het beheer van resources te scheiden. Vanuit beveiligingsperspectief wilt u vaak dat verschillende teams deze resources beheren en beveiligen. Met Azure CNI-netwerken u rechtstreeks verbinding maken met bestaande Azure-bronnen, on-premises resources of andere services via IP-adressen die aan elke pod zijn toegewezen.

Wanneer u Azure CNI-netwerken gebruikt, bevindt de virtuele netwerkbron zich in een afzonderlijke brongroep voor het AKS-cluster. Machtigingen delegeren voor de AKS-serviceprincipal om toegang te krijgen tot deze bronnen en deze te beheren. De serviceprincipal die door het AKS-cluster wordt gebruikt, moet ten minste machtigingen voor [netwerkbijdragen in het](../role-based-access-control/built-in-roles.md#network-contributor) subnet binnen uw virtuele netwerk hebben. Als u een [aangepaste rol](../role-based-access-control/custom-roles.md) wilt definiëren in plaats van de ingebouwde rol netwerkbijdrager te gebruiken, zijn de volgende machtigingen vereist:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Zie [Toegang tot andere Azure-bronnen delegeren voor][sp-delegation]meer informatie over de belangrijkste delegatie van AKS-service.

Als elk knooppunt en pod zijn eigen IP-adres ontvangen, plant u de adresbereiken voor de AKS-subnetten. Het subnet moet groot genoeg zijn om IP-adressen te bieden voor elk knooppunt, pods en netwerkbronnen die u implementeert. Elk AKS-cluster moet in zijn eigen subnet worden geplaatst. Als u connectiviteit met on-premises of peered-netwerken in Azure wilt toestaan, gebruikt u geen IP-adresbereiken die overlappen met bestaande netwerkbronnen. Er zijn standaardlimieten voor het aantal pods dat elk knooppunt uitvoert met zowel kubenet- als Azure CNI-netwerken. Als u scale-outgebeurtenissen of clusterupgrades wilt verwerken, hebt u ook extra IP-adressen nodig die beschikbaar zijn voor gebruik in het toegewezen subnet. Deze extra adresruimte is vooral belangrijk als u Windows Server-containers gebruikt (momenteel in preview in AKS), omdat deze knooppuntgroepen een upgrade vereisen om de nieuwste beveiligingspatches toe te passen. Zie [Een knooppuntgroep in AKS upgraden][nodepool-upgrade]voor meer informatie over Windows Server-knooppunten.

Zie [Azure CNI-netwerken configureren in AKS][advanced-networking]als u het vereiste IP-adres wilt berekenen.

### <a name="kubenet-networking"></a>Kubenet netwerken

Hoewel kubenet niet vereist dat u de virtuele netwerken instelt voordat het cluster wordt geïmplementeerd, zijn er nadelen:

* Knooppunten en pods worden op verschillende IP-subnetten geplaatst. User Defined Routing (UDR) en IP forwarding worden gebruikt om verkeer tussen pods en knooppunten te routeren. Deze extra routering kan de netwerkprestaties verminderen.
* Verbindingen met bestaande on-premises netwerken of peering met andere virtuele Azure-netwerken kunnen complex zijn.

Kubenet is geschikt voor kleine ontwikkel- of testworkloads, omdat u het virtuele netwerk en de subnetten niet apart van het AKS-cluster hoeft te maken. Eenvoudige websites met weinig verkeer, of om workloads in containers op te heffen en te verschuiven, kunnen ook profiteren van de eenvoud van AKS-clusters die worden geïmplementeerd met kubenet-netwerken. Voor de meeste productie-implementaties moet u Azure CNI-netwerken plannen en gebruiken. U ook [uw eigen IP-adresbereiken en virtuele netwerken configureren met behulp van kubenet.][aks-configure-kubenet-networking]

## <a name="distribute-ingress-traffic"></a>Ingressverkeer verdelen

**Richtlijnen voor aanbevolen procedures** - Als u HTTP- of HTTPS-verkeer naar uw toepassingen wilt distribueren, gebruikt u invallende resources en controllers. Ingress-controllers bieden extra functies via een gewone Azure-load balancer en kunnen worden beheerd als native Kubernetes-bronnen.

Een Azure load balancer kan klantverkeer distribueren naar toepassingen in uw AKS-cluster, maar het is beperkt in wat het begrijpt over dat verkeer. Een load balancer-bron werkt op laag 4 en verdeelt verkeer op basis van protocol of poorten. De meeste webapplicaties die HTTP of HTTPS gebruiken, moeten gebruik maken van Kuberenetes ingress resources en controllers, die werken op laag 7. Ingress kan verkeer distribueren op basis van de URL van de toepassing en TLS/SSL-beëindiging afhandelen. Deze mogelijkheid vermindert ook het aantal IP-adressen dat u blootstelt en in kaart brengt. Met een load balancer heeft elke toepassing doorgaans een openbaar IP-adres nodig dat is toegewezen en toegewezen aan de service in het AKS-cluster. Met een invallende bron kan één IP-adres verkeer distribueren naar meerdere toepassingen.

![Diagram met instroom in een AKS-cluster](media/operator-best-practices-network/aks-ingress.png)

 Er zijn twee componenten voor binnendringen:

 * Een invallende *bron,* en
 * Een binnendringende *controller*

De invallende bron is een `kind: Ingress` YAML-manifest waarin de host, certificaten en regels worden gedefinieerd om verkeer door te sturen naar services die in uw AKS-cluster worden uitgevoerd. In het volgende voorbeeld yaml-manifest wordt verkeer voor *myapp.com* gedistribueerd naar een van de twee services, *blogservice* of *storeservice.* De klant wordt doorverwezen naar de ene of de andere service op basis van de URL die hij of zij heeft.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

Een invallende controller is een daemon die draait op een AKS-knooppunt en kijkt naar binnenkomende aanvragen. Het verkeer wordt vervolgens verdeeld op basis van de regels die zijn gedefinieerd in de ingress-bron. De meest voorkomende ingress controller is gebaseerd op [NGINX]. AKS beperkt u niet tot een specifieke controller, dus u andere controllers gebruiken, zoals [Contour,][contour] [HAProxy][haproxy]of [Traefik.][traefik]

Invallencontrollers moeten worden gepland op een Linux-knooppunt. Windows Server-knooppunten (momenteel in preview in AKS) mogen de invallende controller niet uitvoeren. Gebruik een knooppuntkiezer in uw YAML-manifest of Helm-grafiekimplementatie om aan te geven dat de resource moet worden uitgevoerd op een op Linux gebaseerd knooppunt. Zie [Knooppuntselectors gebruiken om te bepalen waar pods zijn gepland in AKS][concepts-node-selectors]voor meer informatie.

Er zijn veel scenario's voor binnendringen, waaronder de volgende handleidingen:

* [Een basis-ingress-controller maken met externe netwerkconnectiviteit][aks-ingress-basic]
* [Een invallende controller maken die een intern, privénetwerk en IP-adres gebruikt][aks-ingress-internal]
* [Een ingress-controller maken die uw eigen TLS-certificaten gebruikt][aks-ingress-own-tls]
* Een invallende controller maken die Let's Encrypt gebruikt om automatisch TLS-certificaten te genereren [met een dynamisch openbaar IP-adres][aks-ingress-tls] of met een statisch openbaar [IP-adres][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Veilig verkeer met een firewall voor webtoepassingen (WAF)

**Richtlijnen voor aanbevolen procedures** - Als u binnenkomend verkeer wilt scannen op potentiële aanvallen, gebruikt u een Web Application Firewall (WAF) zoals [Barracuda WAF voor Azure][barracuda-waf] of Azure Application Gateway. Deze meer geavanceerde netwerkbronnen kunnen ook verkeer routeren buiten alleen HTTP- en HTTPS-verbindingen of eenvoudige SSL-beëindiging.

Een invallende controller die verkeer distribueert naar services en toepassingen is meestal een Kubernetes-bron in uw AKS-cluster. De controller draait als een daemon op een AKS-knooppunt en verbruikt een aantal bronnen van het knooppunt, zoals CPU, geheugen en netwerkbandbreedte. In grotere omgevingen wilt u vaak een deel van deze verkeersroutering of TLS-beëindiging losmaken naar een netwerkbron buiten het AKS-cluster. U wilt ook binnenkomend verkeer scannen op mogelijke aanvallen.

![Een Web application firewall (WAF) zoals Azure App Gateway kan verkeer voor uw AKS-cluster beveiligen en distribueren](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Een webapplicatiefirewall (WAF) biedt een extra beveiligingslaag door het binnenkomende verkeer te filteren. Het Open Web Application Security Project (OWASP) biedt een reeks regels om te kijken naar aanvallen zoals cross site scripting of cookie vergiftiging. [Azure Application Gateway][app-gateway] (momenteel in preview in AKS) is een WAF die kan integreren met AKS-clusters om deze beveiligingsfuncties te bieden, voordat het verkeer uw AKS-cluster en -toepassingen bereikt. Andere oplossingen van derden voeren deze functies ook uit, zodat u bestaande investeringen of expertise in een bepaald product blijven gebruiken.

Load balancer- of ingressresources blijven worden uitgevoerd in uw AKS-cluster om de verkeersverdeling verder te verfijnen. App Gateway kan centraal worden beheerd als een inbinnendringen controller met een resource definitie. Maak een [Application Gateway Ingress-controller om][app-gateway-ingress]aan de slag te gaan.

## <a name="control-traffic-flow-with-network-policies"></a>Verkeersstroom regelen met netwerkbeleid

**Richtlijnen voor aanbevolen procedures** : gebruik netwerkbeleid om verkeer toe te staan of te weigeren aan pods. Standaard is al het verkeer toegestaan tussen pods binnen een cluster. Voor een betere beveiliging definieert u regels die podcommunicatie beperken.

Netwerkbeleid is een Kubernetes-functie waarmee u de verkeersstroom tussen pods beheren. U ervoor kiezen om verkeer toe te staan of te weigeren op basis van instellingen zoals toegewezen labels, naamruimte of verkeerspoort. Het gebruik van netwerkbeleid biedt een cloud-native manier om de verkeersstroom te beheren. Aangezien pods dynamisch worden gemaakt in een AKS-cluster, kan het vereiste netwerkbeleid automatisch worden toegepast. Gebruik azure-netwerkbeveiligingsgroepen niet om pod-to-pod-verkeer te beheren, gebruik netwerkbeleid.

Als u het netwerkbeleid wilt gebruiken, moet de functie zijn ingeschakeld wanneer u een AKS-cluster maakt. U het netwerkbeleid voor een bestaand AKS-cluster niet inschakelen. Plan vooruit om ervoor te zorgen dat u netwerkbeleid voor clusters inschakelt en deze indien nodig gebruiken. Netwerkbeleid mag alleen worden gebruikt voor op Linux gebaseerde knooppunten en pods in AKS.

Een netwerkbeleid wordt gemaakt als een Kubernetes-bron met behulp van een YAML-manifest. Het beleid wordt toegepast op gedefinieerde pods en vervolgens bepalen binnenkomende of uitgaande regels hoe het verkeer kan stromen. In het volgende voorbeeld wordt een netwerkbeleid toegepast op pods met de *app: backendlabel* dat erop is toegepast. De in-gress regel staat dan alleen verkeer van pods met de *app toe: frontend* label:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

Zie [Verkeer tussen pods beveiligen met netwerkbeleid in Azure Kubernetes Service (AKS)][use-network-policies]om aan de slag te gaan met beleidsregels.

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Maak veilig verbinding met knooppunten via een bastionhost

**Richtlijnen voor aanbevolen procedures** - Stel externe connectiviteit niet bloot aan uw AKS-knooppunten. Maak een bastionhost of springbox in een virtueel beheernetwerk. Gebruik de bastionhost om verkeer veilig naar uw AKS-cluster te leiden naar beheertaken op afstand.

De meeste bewerkingen in AKS kunnen worden voltooid met behulp van de Azure-beheertools of via de Kubernetes API-server. AKS-knooppunten zijn niet verbonden met het openbare internet en zijn alleen beschikbaar op een privénetwerk. Als u verbinding wilt maken met knooppunten en onderhoudswerkzaamheden wilt uitvoeren of problemen wilt oplossen, u uw verbindingen doorsturen via een bastionhost of een springvak. Deze host moet zich in een apart virtueel beheernetwerk bevinden dat veilig is gekeken naar het virtuele NETWERK van het AKS-cluster.

![Verbinding maken met AKS-knooppunten met behulp van een bastionhost of springvak](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

Het beheernetwerk voor de bastionhost moet ook worden beveiligd. Gebruik een [Azure ExpressRoute-][expressroute] of [VPN-gateway][vpn-gateway] om verbinding te maken met een on-premises netwerk en de toegang te beheren met behulp van netwerkbeveiligingsgroepen.

## <a name="next-steps"></a>Volgende stappen

Dit artikel richtte zich op netwerkconnectiviteit en beveiliging. Zie [Netwerkconcepten voor toepassingen in Azure Kubernetes Service (AKS) voor][aks-concepts-network] meer informatie over de basisprincipes van het netwerk in Kubernetes.

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[Nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool