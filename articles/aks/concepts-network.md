---
title: Concepten - Netwerken in Azure Kubernetes Services (AKS)
description: Meer informatie over netwerken in Azure Kubernetes Service (AKS), waaronder kubenet- en Azure CNI-netwerken, invallende controllers, load balancers en statische IP-adressen.
ms.topic: conceptual
ms.date: 02/28/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 5800254ab44b5b0f1048ce2200f90c06a8d1666a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253934"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Netwerkconcepten voor toepassingen in Azure Kubernetes Service (AKS)

In een op containers gebaseerde microservices-benadering van applicatieontwikkeling moeten toepassingscomponenten samenwerken om hun taken te verwerken. Kubernetes biedt verschillende bronnen die deze toepassingscommunicatie mogelijk maken. U verbinding maken met en toepassingen intern of extern blootstellen. Om zeer beschikbare toepassingen te bouwen, u de balans van uw toepassingen laden. Complexere toepassingen vereisen mogelijk configuratie van binnendringend verkeer voor SSL/TLS-beëindiging of routering van meerdere componenten. Om veiligheidsredenen moet u mogelijk ook de stroom van netwerkverkeer naar of tussen pods en knooppunten beperken.

Dit artikel introduceert de kernconcepten die netwerken bieden aan uw toepassingen in AKS:

- [Services](#services)
- [Virtuele Azure-netwerken](#azure-virtual-networks)
- [Invallende controllers](#ingress-controllers)
- [Netwerkbeleid](#network-policies)

## <a name="kubernetes-basics"></a>Basisbeginselen voor Kubernetes

Om toegang te krijgen tot uw toepassingen of om toepassingscomponenten met elkaar te laten communiceren, biedt Kubernetes een abstractielaag voor virtuele netwerken. Kubernetes-knooppunten zijn verbonden met een virtueel netwerk en kunnen inkomende en uitgaande connectiviteit bieden voor pods. De *kube-proxy component* wordt uitgevoerd op elk knooppunt om deze netwerkfuncties te bieden.

In Kubernetes *groepeert Services* logischerwijs pods om directe toegang mogelijk te maken via een IP-adres of DNS-naam en op een specifieke poort. U ook verkeer distribueren met behulp van een *load balancer.* Meer complexe routing van applicatieverkeer kan ook worden bereikt met *Ingress Controllers*. Beveiliging en filtering van het netwerkverkeer voor pods is mogelijk met *Kubernetes-netwerkbeleid.*

Het Azure-platform helpt ook om virtuele netwerken voor AKS-clusters te vereenvoudigen. Wanneer u een Kubernetes load balancer maakt, wordt de onderliggende Azure load balancer-bron gemaakt en geconfigureerd. Terwijl u netwerkpoorten opent voor pods, worden de bijbehorende Azure-netwerkbeveiligingsgroepregels geconfigureerd. Voor HTTP-toepassingsroutering kan Azure ook *externe DNS* configureren als nieuwe invallende routes zijn geconfigureerd.

## <a name="services"></a>Services

Ter vereenvoudiging van de netwerkconfiguratie maakt Kubernetes gebruik van *Services* om een set pods logisch te groeperen en netwerkconnectiviteit mogelijk te maken. De volgende servicetypen zijn beschikbaar:

- **Cluster-IP** - Hiermee maakt u een intern IP-adres voor gebruik binnen het AKS-cluster. Goed voor interne toepassingen die andere workloads binnen het cluster ondersteunen.

    ![Diagram met cluster-IP-verkeersstroom in een AKS-cluster][aks-clusterip]

- **NodePort** - Hiermee maakt u een poorttoewijzing op het onderliggende knooppunt waarmee de toepassing rechtstreeks kan worden geopend met het IP-adres en de poort van het knooppunt.

    ![Diagram met de verkeersstroom van NodePort in een AKS-cluster][aks-nodeport]

- **LoadBalancer** - Hiermee maakt u een Azure load balancer-bron, configureert u een extern IP-adres en verbindt u de gevraagde pods met de backendpool van de load balancer. Om het verkeer van klanten in staat te stellen de toepassing te bereiken, worden regels voor taakverdeling gemaakt op de gewenste poorten. 

    ![Diagram met de verkeersstroom van load balancer in een AKS-cluster][aks-loadbalancer]

    Voor extra controle en routering van het binnenkomende verkeer u in plaats daarvan een [Ingress-controller](#ingress-controllers)gebruiken.

- **ExternalName** - Hiermee maakt u een specifieke DNS-vermelding voor eenvoudigere toegang tot toepassingen.

Het IP-adres voor load balancers en services kan dynamisch worden toegewezen of u een bestaand statisch IP-adres opgeven dat u wilt gebruiken. Zowel interne als externe statische IP-adressen kunnen worden toegewezen. Dit bestaande statische IP-adres is vaak gekoppeld aan een DNS-vermelding.

Zowel *interne* als *externe* load balancers kunnen worden gemaakt. Interne load balancers krijgen alleen een privé-IP-adres toegewezen, zodat ze niet toegankelijk zijn via internet.

## <a name="azure-virtual-networks"></a>Virtuele netwerken van Azure

In AKS kunt u een cluster implementeren dat gebruikmaakt van een van de volgende twee netwerkmodellen:

- *Kubenet-netwerken* - De netwerkbronnen worden meestal gemaakt en geconfigureerd wanneer het AKS-cluster wordt geïmplementeerd.
- *CNI-netwerken (Azure Container Networking Interface)* - Het AKS-cluster is verbonden met bestaande virtuele netwerkbronnen en -configuraties.

### <a name="kubenet-basic-networking"></a>Kubenet (basis)netwerken

De *kubenet-netwerkoptie* is de standaardconfiguratie voor het maken van AKS-cluster. Met *kubenet*krijgen knooppunten een IP-adres van het subnet azure virtual network. Pods krijgen een IP-adres van een logisch verschillende adresruimte van het subnet van het virtuele Azure-netwerk van de knooppunten. NAT (Network Address Translation) wordt vervolgens zo geconfigureerd dat de pods resources kunnen bereiken in het virtuele Azure-netwerk. Het bron-IP-adres van het verkeer is NAT'd naar het primaire IP-adres van het knooppunt.

Nodes maken gebruik van de [kubenet][kubenet] Kubernetes plugin. U het Azure-platform de virtuele netwerken voor u laten maken en configureren, of ervoor kiezen om uw AKS-cluster te implementeren in een bestaand virtueel netwerksubnet. Nogmaals, alleen de knooppunten ontvangen een routeerbaar IP-adres en de pods gebruiken NAT om te communiceren met andere bronnen buiten het AKS-cluster. Deze aanpak vermindert het aantal IP-adressen dat u moet reserveren in uw netwerkruimte voor pods om te gebruiken.

Zie [Kubenet-netwerken configureren voor een AKS-cluster voor][aks-configure-kubenet-networking]meer informatie.

### <a name="azure-cni-advanced-networking"></a>Azure CNI (geavanceerde) netwerken

Met Azure CNI krijgt elke pod een IP-adres van het subnet en kan deze rechtstreeks worden geopend. Deze IP-adressen moeten uniek zijn in uw netwerkruimte en moeten van tevoren worden gepland. Elk knooppunt heeft een configuratieparameter voor het maximum aantal pods dat het ondersteunt. Het equivalentaantal IP-adressen per knooppunt wordt dan vooraf gereserveerd voor dat knooppunt. Deze aanpak vereist meer planning, zoals anders kan leiden tot IP-adres uitputting of de noodzaak om clusters te herbouwen in een groter subnet als uw toepassing eisen groeien.

Knooppunten maken gebruik van de [CNI-plug-in Azure Container Networking Interface (CNI).][cni-networking]

![Diagram met twee knooppunten met bruggen die elk verbinden met één Azure VNet][advanced-networking-diagram]

Zie [Azure CNI configureren voor een AKS-cluster voor][aks-configure-advanced-networking]meer informatie.

### <a name="compare-network-models"></a>Netwerkmodellen vergelijken

Zowel kubenet als Azure CNI bieden netwerkconnectiviteit voor uw AKS-clusters. Echter, er zijn voor- en nadelen aan elk. Op hoog niveau gelden de volgende overwegingen:

* **kubenet kubenet**
    * Bespaart IP-adresruimte.
    * Gebruikt Kubernetes interne of externe load balancer om pods van buiten het cluster te bereiken.
    * U moet handmatig door de gebruiker gedefinieerde routes (UDR's) beheren en onderhouden.
    * Maximaal 400 knooppunten per cluster.
* **Azure CNI**
    * Pods krijgen volledige virtuele netwerkconnectiviteit en zijn rechtstreeks bereikbaar via hun privé-IP-adres via verbonden netwerken.
    * Vereist meer IP-adresruimte.

De volgende gedragsverschillen bestaan tussen kubenet en Azure CNI:

| Mogelijkheid                                                                                   | Kubenet Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Cluster implementeren in bestaand of nieuw virtueel netwerk                                            | Ondersteund - UDR's handmatig toegepast | Ondersteund |
| Pod-pod-connectiviteit                                                                         | Ondersteund | Ondersteund |
| Pod-VM-connectiviteit; VM in hetzelfde virtuele netwerk                                          | Werkt per pod | Werkt in beide richtingen |
| Pod-VM-connectiviteit; VM in peered virtueel netwerk                                            | Werkt per pod | Werkt in beide richtingen |
| On-premises toegang via VPN of Express Route                                                | Werkt per pod | Werkt in beide richtingen |
| Toegang tot bronnen die zijn beveiligd door serviceeindpunten                                             | Ondersteund | Ondersteund |
| Kubernetes-services blootstellen met behulp van een load balancer-service, App Gateway of ingress-controller | Ondersteund | Ondersteund |
| Standaard Azure DNS- en privézones                                                          | Ondersteund | Ondersteund |

Met betrekking tot DNS, met zowel kubenet en Azure CNI plugins DNS wordt aangeboden door CoreDNS, een daemon set draait in AKS. Zie [DNS Service aanpassen](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/)voor meer informatie over CoreDNS op Kubernetes. CoreDNS is standaard geconfigureerd om onbekende domeinen door te sturen naar de DNS-servers van het knooppunt, met andere woorden, naar de DNS-functionaliteit van het Azure Virtual Network waar het AKS-cluster wordt geïmplementeerd. Azure DNS en Private Zones werken daarom voor pods die in AKS worden uitgevoerd.

### <a name="support-scope-between-network-models"></a>Ondersteuningsbereik tussen netwerkmodellen

Ongeacht het netwerkmodel dat u gebruikt, kunnen zowel kubenet als Azure CNI op een van de volgende manieren worden geïmplementeerd:

* Het Azure-platform kan automatisch de virtuele netwerkbronnen maken en configureren wanneer u een AKS-cluster maakt.
* U de virtuele netwerkbronnen handmatig maken en configureren en deze resources koppelen wanneer u uw AKS-cluster maakt.

Hoewel mogelijkheden zoals serviceeindpunten of UDR's worden ondersteund met zowel kubenet als Azure CNI, bepaalt het [ondersteuningsbeleid voor AKS][support-policies] welke wijzigingen u aanbrengen. Bijvoorbeeld:

* Als u handmatig de virtuele netwerkbronnen voor een AKS-cluster maakt, wordt u ondersteund bij het configureren van uw eigen UDR's of serviceeindpunten.
* Als het Azure-platform automatisch de virtuele netwerkbronnen voor uw AKS-cluster maakt, wordt het niet ondersteund om deze AKS-beheerde resources handmatig te wijzigen om uw eigen UDR's of serviceeindpunten te configureren.

## <a name="ingress-controllers"></a>controllers van inkomend verkeer

Wanneer u een LoadBalancer-typeservice maakt, wordt een onderliggende Azure load balancer-bron gemaakt. De load balancer is geconfigureerd om verkeer te distribueren naar de pods in uw Service op een bepaalde poort. De LoadBalancer werkt alleen op laag 4 - de Service is niet op de hoogte van de werkelijke toepassingen en kan geen extra routeringsoverwegingen maken.

*Invallencontrollers* werken op laag 7 en kunnen intelligentere regels gebruiken om toepassingsverkeer te distribueren. Een veelgebruikt gebruik van een Ingress-controller is het routeren van HTTP-verkeer naar verschillende toepassingen op basis van de binnenkomende URL.

![Diagram met instroom in een AKS-cluster][aks-ingress]

In AKS u een Ingress-bron maken met iets als NGINX of de functie AKS HTTP-toepassingsroutering gebruiken. Wanneer u HTTP-toepassingsroutering inschakelt voor een AKS-cluster, maakt het Azure-platform de Ingress-controller en een *Extern-DNS-controller.* Als er nieuwe Ingress-bronnen worden gemaakt in Kubernetes, worden de vereiste DNS A-records gemaakt in een clusterspecifieke DNS-zone. Zie [HTTP-toepassingsroutering implementeren voor][aks-http-routing]meer informatie .

Een ander gemeenschappelijk kenmerk van Ingress is SSL / TLS beëindiging. Bij grote webapplicaties die via HTTPS worden geopend, kan de TLS-beëindiging worden afgehandeld door de Ingress-bron in plaats van binnen de toepassing zelf. Als u automatische TLS-certificering en -configuratie wilt bieden, u de Ingress-bron configureren om providers zoals Let's Encrypt te gebruiken. Zie [Ingress en TLS][aks-ingress-tls]voor meer informatie over het configureren van een NGINX Ingress-controller met Let's Encrypt.

U ook uw invallende controller configureren om het IP-adres van de clientbron te behouden op aanvragen voor containers in uw AKS-cluster. Wanneer het verzoek van een client via uw invallende controller naar een container in uw AKS-cluster wordt doorgestuurd, is het oorspronkelijke bron-IP van die aanvraag niet beschikbaar voor de doelcontainer. Wanneer u *ip-conservering van clientbron*inschakelt, is het bron-IP voor de client beschikbaar in de aanvraagkoptekst onder *X-Forwarded-For*. Als u IP-behoud van clientbronnen gebruikt op uw invallende controller, u ssl-doorgang niet gebruiken. Clientbron IP-behoud en SSL-pass-through kunnen worden gebruikt met andere services, zoals het *type LoadBalancer.*

## <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen

Een netwerkbeveiligingsgroep filtert verkeer voor VM's, zoals de AKS-knooppunten. Terwijl u Services maakt, zoals een LoadBalancer, configureert het Azure-platform automatisch alle regels voor netwerkbeveiligingsgroepen die nodig zijn. Configureer niet handmatig regels voor netwerkbeveiliging om verkeer te filteren op pods in een AKS-cluster. Definieer alle vereiste poorten en doorsturen als onderdeel van uw Kubernetes Service-manifesten en laat het Azure-platform de juiste regels maken of bijwerken. U ook netwerkbeleid gebruiken, zoals besproken in de volgende sectie, om automatisch verkeersfilterregels toe te passen op pods.

## <a name="network-policies"></a>Netwerkbeleid

Standaard kunnen alle pods in een AKS-cluster zonder beperkingen verkeer verzenden en ontvangen. Voor een betere beveiliging u regels definiëren die de verkeersstroom regelen. Backend-toepassingen worden vaak alleen blootgesteld aan vereiste frontendservices of databasecomponenten zijn alleen toegankelijk voor de toepassingslagen die ermee verbinding maken.

Netwerkbeleid is een Kubernetes-functie die beschikbaar is in AKS waarmee u de verkeersstroom tussen pods beheren. U ervoor kiezen om verkeer toe te staan of te weigeren op basis van instellingen zoals toegewezen labels, naamruimte of verkeerspoort. Netwerkbeveiligingsgroepen zijn meer voor de AKS-knooppunten, niet voor pods. Het gebruik van netwerkbeleid is een meer geschikte, cloud-native manier om de verkeersstroom te regelen. Aangezien pods dynamisch worden gemaakt in een AKS-cluster, kan het vereiste netwerkbeleid automatisch worden toegepast.

Zie [Verkeer tussen pods beveiligen met netwerkbeleid in Azure Kubernetes Service (AKS)][use-network-policies]voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt met AKS-netwerken, maakt en configureert u een AKS-cluster met uw eigen IP-adresbereiken met [kubenet][aks-configure-kubenet-networking] of [Azure CNI.][aks-configure-advanced-networking]

Zie [Aanbevolen procedures voor netwerkconnectiviteit en beveiliging in AKS voor][operator-best-practices-network]bijbehorende aanbevolen procedures.

Zie de volgende artikelen voor meer informatie over de belangrijkste Kubernetes- en AKS-concepten:

- [Kubernetes / AKS-clusters en workloads][aks-concepts-clusters-workloads]
- [Kubernetes / AKS toegang en identiteit][aks-concepts-identity]
- [Kubernetes / AKS-beveiliging][aks-concepts-security]
- [Kubernetes / AKS-opslag][aks-concepts-storage]
- [Kubernetes / AKS-schaal][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ingress.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
