---
title: Overzicht van Azure Networking Services
description: Meer informatie over netwerk services in azure, waaronder connectiviteit, toepassings beveiliging, levering van toepassingen en netwerk bewakings Services.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: 39601da6cc8e10ba8f4a24eeddb97cfe677c031f
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651732"
---
# <a name="azure-networking-services-overview"></a>Overzicht van Azure Networking Services

De netwerk services in azure bieden diverse netwerk mogelijkheden die samen of afzonderlijk kunnen worden gebruikt. Klik op een van de volgende belang rijke mogelijkheden om meer te weten te komen over:
- [**Connectiviteits Services**](#connect): Azure-resources en on-premises resources verbinden met een of meer van deze netwerk services in Azure-Virtual Network (VNet), Virtual WAN, ExpressRoute, VPN gateway, virtueel netwerk NAT Gateway, Azure DNS, peering-service en Azure Bastion.
- [**Application Protection Services**](#protect): Bescherm uw toepassingen met behulp van een of een combi natie van deze netwerk services in azure-private link, DDoS Protection, firewall, netwerk beveiligings groepen, Web Application Firewall en Virtual Network-eind punten.
- [**Services**](#deliver)voor het leveren van toepassingen: toepassingen leveren in het Azure-netwerk met behulp van een of een combi natie van deze netwerk services in azure-Content Delivery Network (CDN), Azure front-deur Service, Traffic Manager, Application Gateway, Internet Analyzer en Load Balancer.
- [**Netwerk bewaking**](#monitor): Controleer uw netwerk bronnen met behulp van een of een combi natie van deze netwerk services in Azure-Network Watcher, ExpressRoute Monitor, Azure monitor of VNet-Terminal toegangs punt (tik).

## <a name="connectivity-services"></a><a name="connect"></a>Connectiviteitsservices
 
In deze sectie worden de services beschreven die verbinding bieden tussen Azure-resources, connectiviteit van een on-premises netwerk met Azure-resources en vertakking naar vertakkings connectiviteit in azure-Virtual Network (VNet), Virtual WAN, ExpressRoute, VPN Gateway, virtueel netwerk NAT gateway, Azure DNS, Azure peering-service en Azure Bastion.

|Service|Waarom gebruiken?|Scenario's|
|---|---|---|
|[Virtueel netwerk](#vnet)|Hiermee kunnen Azure-bronnen veilig communiceren met elkaar, Internet en on-premises netwerken.| <p>[Netwerkverkeer filteren](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Netwerkverkeer routeren](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Netwerktoegang tot resources beperken](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Virtuele netwerken met elkaar verbinden](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|Breidt uw on-premises netwerken uit in de micro soft-Cloud via een persoonlijke verbinding die wordt vereenvoudigd door een connectiviteits provider.|<p>[Een ExpressRoute-circuit maken en wijzigen](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Peering voor een ExpressRoute-circuit maken en wijzigen](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Een VNet koppelen aan een ExpressRoute-circuit](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Route filters configureren en beheren voor ExpressRoute-circuits](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN Gateway](#vpngateway)|Versleuteld verkeer verzenden tussen een virtueel Azure-netwerk en een on-premises locatie via het open bare Internet.|<p>[Site-naar-site-verbindingen](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[VNet-naar-VNet-verbindingen](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Punt-naar-site-verbindingen](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[Virtual WAN](#virtualwan)|Optimaliseert en automatiseert de verbinding tussen filialen en Azure. Azure-regio's fungeren als hubs die u kunt gebruiken om uw filialen te koppelen aan.|<p>[Site-naar-site-verbindingen](../virtual-wan/virtual-wan-site-to-site-portal.md), [ExpressRoute-verbindingen](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Host DNS-domeinen die naam omzetting bieden met behulp van Microsoft Azure-infra structuur.|<p>[Uw domein hosten in Azure DNS](../dns/dns-delegate-domain-azure-dns.md)</p><p>[DNS-records voor een web-app maken](../dns/dns-web-sites-custom-domain.md)</p> <p>[Een alias record maken voor Traffic Manager](../dns/tutorial-alias-tm.md)</p> <p>[Een alias record maken voor openbaar IP-adres](../dns/tutorial-alias-pip.md)</p> <p>[Een alias record maken voor de zone bron record](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|Configureer veilige en naadloze RDP/SSH-verbindingen met uw virtuele machines rechtstreeks in de Azure Portal via TLS. Wanneer u verbinding maakt via Azure Bastion, hebt u geen openbaar IP-adres nodig voor uw virtuele machines|<p>[Een Azure Bastion-host maken](../bastion/bastion-create-host-portal.md)</p><p>[Via SSH verbinding maken met een virtuele Linux-machine](../bastion/bastion-connect-vm-ssh.md)</p><p>[Verbinding maken met behulp van RDP met een Windows VM](../bastion/bastion-connect-vm-rdp.md)</p>|
|[NAT-gateway van virtueel netwerk](#nat)|Maak een NAT-gateway om uitgaande connectiviteit te bieden voor een virtuele machine.|<p>[Een NAT-gateway maken](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Azure Peering Service](#azurepeeringservice)|Samen werken met service providers voor optimale en betrouw bare route ring naar de micro soft-Cloud via het open bare netwerk.|<p>[Azure peering-service registreren](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>Virtueel netwerk

Azure Virtual Network (VNet) is de basisbouwsteen voor uw privénetwerk in Azure. U kunt een VNets gebruiken om het volgende te doen:
- **Communiceren tussen Azure-resources**: u kunt vm's en verschillende andere typen Azure-resources implementeren in een virtueel netwerk, zoals Azure app service omgevingen, de Azure Kubernetes-service (AKS) en Azure virtual machine Scale sets. Zie [Integratie van virtuele netwerkservices](../virtual-network/virtual-network-for-azure-services.md) voor een volledige lijst met Azure-resources die u in een virtueel netwerk kunt implementeren.
- **Communiceren tussen**elkaar: u kunt virtuele netwerken met elkaar verbinden, waardoor resources in beide virtuele netwerken met elkaar kunnen communiceren met behulp van peering op virtueel netwerk. De virtuele netwerken die u met elkaar verbindt, kunnen zich in dezelfde of verschillende Azure-regio's bevinden. Zie [peering van virtuele netwerken](../virtual-network/virtual-network-peering-overview.md)voor meer informatie.
- **Communiceren met Internet**: alle resources in een VNet kunnen standaard uitgaand verkeer naar Internet communiceren. U kunt binnenkomend communiceren met een resource door er een openbaar IP-adres of een openbare Load Balancer aan toe te wijzen. U kunt ook [open bare IP-adressen](../virtual-network/virtual-network-public-ip-address.md) of open bare [Load Balancer](../load-balancer/load-balancer-overview.md) gebruiken om uw uitgaande verbindingen te beheren.
- **Communiceren met on-premises netwerken**: u kunt uw on-premises computers en netwerken verbinden met een virtueel netwerk met behulp van [VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ExpressRoute](../expressroute/expressroute-introduction.md).

Zie [Wat is Azure Virtual Network?](../virtual-network/virtual-networks-overview.md)voor meer informatie.

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
Met ExpressRoute kunt u uw on-premises netwerken in de micro soft Cloud uitbreiden via een persoonlijke verbinding die wordt vereenvoudigd door een connectiviteits provider. Deze verbinding is een privéverbinding. Verkeer gaat niet via internet. Met ExpressRoute kunt u verbindingen tot stand brengen met Microsoft Cloud-services, zoals Microsoft Azure, Office 365 en Dynamics 365.  Zie [Wat is ExpressRoute?](../expressroute/expressroute-introduction.md)voor meer informatie.

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
VPN Gateway helpt u bij het maken van versleutelde cross-premises verbindingen met uw virtuele netwerk vanaf on-premises locaties of het maken van versleutelde verbindingen tussen VNets. Er zijn verschillende configuraties beschikbaar voor VPN Gateway verbindingen, zoals site-naar-site, punt-naar-site-of VNet-naar-VNet.
In het volgende diagram ziet u meerdere site-naar-site-VPN-verbindingen met hetzelfde virtuele netwerk.

![Verbindingen van site-naar-site Azure-VPN Gateway](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

Zie [VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)voor meer informatie over verschillende typen VPN-verbindingen.

### <a name="virtual-wan"></a><a name="virtualwan"></a>Virtual WAN
Azure Virtual WAN is een netwerk service die zorgt voor geoptimaliseerde en geautomatiseerde vertakkings connectiviteit met, en via Azure. Azure-regio's fungeren als hubs die u kunt gebruiken om uw filialen te koppelen aan. U kunt de Azure-backbone gebruiken om ook vertakkingen te verbinden en te profiteren van de connectiviteit van Branch-to-VNet. Met Azure Virtual WAN worden veel Azure Cloud Connectivity Services, zoals site-naar-site VPN, ExpressRoute, punt-naar-site-gebruikers-VPN, in één operationele interface gecombineerd. Connectiviteit met Azure VNets wordt tot stand gebracht met behulp van virtuele netwerk verbindingen. Zie [Wat is virtueel WAN van Azure?](../virtual-wan/virtual-wan-about.md)voor meer informatie.

![Virtual WAN-diagram](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS is een hostingservice voor DNS-domeinen die naamomzetting biedt met behulp van Microsoft Azure-infrastructuur. Door uw domeinen in Azure te hosten, kunt u uw DNS-records met dezelfde referenties, API's, hulpprogramma's en facturering beheren als voor uw andere Azure-services. Zie [Wat is Azure DNS?](../dns/dns-overview.md)voor meer informatie.

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
De Azure Bastion-service is een nieuwe, volledig door het platform beheerde PaaS-service die u in uw virtuele netwerk inricht. De service biedt beveiligde en naadloze RDP-/SSH-connectiviteit in uw virtuele machine, rechtstreeks in Azure Portal via TLS. Wanneer u verbinding maakt met Azure Bastion, hebben uw virtuele machines geen openbaar IP nodig. Zie [Wat is Azure Bastion?](../bastion/bastion-overview.md)voor meer informatie.

![Azure Bastion-architectuur](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>NAT-gateway van virtueel netwerk
Virtual Network NAT (Network Address Translation) vereenvoudigt uitsluitend uitgaande internetconnectiviteit voor virtuele netwerken. Indien geconfigureerd op een subnet, maken alle uitgaande verbindingen gebruik van uw opgegeven statische openbare IP-adressen. Uitgaande connectiviteit is mogelijk zonder load balancer of openbare IP-adressen die rechtstreeks zijn gekoppeld aan virtuele machines. Zie [Wat is virtueel netwerk NAT gateway?](../virtual-network/nat-overview.md) voor meer informatie. 

![NAT-gateway van virtueel netwerk](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Azure peering-service
Azure peering service verbetert de connectiviteit van klanten aan micro soft-Cloud Services, zoals Office 365, Dynamics 365, software as a Service (SaaS)-Services, Azure of andere micro soft-services die toegankelijk zijn via het open bare Internet. Zie [Wat is Azure peering service?](../peering-service/about.md)voor meer informatie.

## <a name="application-protection-services"></a><a name="protect"></a>Services voor toepassings beveiliging

In deze sectie worden netwerk services in azure beschreven waarmee u uw netwerk bronnen kunt beveiligen. Beveilig uw toepassingen met een of meer combi natie van deze netwerk services in azure-private link, DDoS Protection, firewall, netwerk beveiligings groepen, Web Application firewall en Virtual Network-eind punten.

|Service|Waarom gebruiken?|Scenario|
|---|---|---|
|[DDoS-beveiliging](#ddosprotection) |Hoge Beschik baarheid voor uw toepassingen met beveiliging tegen overtollige IP-verkeer|[Azure DDoS Protection beheren](../virtual-network/manage-ddos-protection.md)|
|[Web Application Firewall](#waf)|<p>[Azure WAF met Application Gateway](../web-application-firewall/ag/ag-overview.md) biedt regionale beveiliging van entiteiten in open bare en privé-adres ruimte</p><p>[Azure WAF met de voor deur](../web-application-firewall/afds/afds-overview.md) biedt beveiliging aan de rand van het netwerk tot open bare eind punten.</p>|<p>[Bot-beveiligings regels configureren](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Aangepast antwoordcode configureren](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[IP-beperkings regels configureren](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Regel voor frequentie limiet configureren](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Azure Firewall](#firewall)|Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt. Het is een volledige stateful firewall als een service met ingebouwde hoge beschikbaarheid en onbeperkte cloudschaalbaarheid.|<p>[Een Azure Firewall implementeren in een Vnet](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[-Een Azure Firewall implementeren in een hybride netwerk](../firewall/tutorial-hybrid-ps.md)</p> <p>[Inkomend verkeer filteren met Azure Firewall DNAT](../firewall/tutorial-firewall-dnat.md)</p>|
|[Netwerkbeveiligingsgroepen](#nsg)|Volledig granulair gedistribueerd besturings element voor eind knooppunten op VM/subnet voor alle netwerk verkeer stromen|[Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/tutorial-filter-network-traffic.md)|
|[Service-eindpunten voor virtueel netwerk](#serviceendpoints)|Hiermee kunt u de netwerk toegang tot bepaalde Azure-service bronnen beperken tot een subnet van een virtueel netwerk|[Netwerktoegang tot PaaS-resources beperken](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[Private Link](#privatelink)|Hiermee hebt u toegang tot Azure PaaS-Services (bijvoorbeeld Azure Storage en SQL Database) en Azure gehoste klanten services van de klant via een persoonlijk eind punt in uw virtuele netwerk.|<p>[Een privé-eindpunt maken](../private-link/create-private-endpoint-portal.md)</p><p>[Een Private Link-service maken](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS Protection 
[Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) biedt tegen maatregelen tegen de meest geavanceerde DDoS bedreigingen. De service biedt verbeterde mogelijkheden voor DDoS-beperking voor uw toepassing en resources die zijn geïmplementeerd in uw virtuele netwerken. Klanten die Azure DDoS Protection gebruiken, hebben bovendien toegang tot DDoS snelle reactie ondersteuning om DDoS-experts te benaderen tijdens een actieve aanval.

![DDoS Protection](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Web Application Firewall

[Azure Web Application firewall](../web-application-firewall/overview.md) (WAF) biedt beveiliging voor uw webtoepassingen van veelvoorkomende webtoepassingen en zwakke plekken, zoals SQL-injectie en scripting op meerdere sites. Azure WAF voorziet in out-of-Box-beveiliging van OWASP Top 10 van beveiligings problemen via beheerde regels. Daarnaast kunnen klanten aangepaste regels configureren, die door de klant beheerde regels zijn om extra beveiliging te bieden op basis van het bron-IP-bereik, en om kenmerken zoals kopteksten, cookies, formulier gegevens velden of query reeks parameters op te geven.

Klanten kunnen ervoor kiezen om [Azure WAF te implementeren met Application Gateway](../application-gateway/waf-overview.md) die regionale beveiliging biedt aan entiteiten in open bare en privé-adres ruimte. Klanten kunnen er ook voor kiezen om [Azure WAF te implementeren met de voor deur](../frontdoor/waf-overview.md) die beveiliging biedt aan de rand van het netwerk tot open bare eind punten.

![Web Application Firewall](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt. Met behulp van Azure Firewall kunt u toepassingen en beleids regels voor de toepassing en het netwerk op verschillende locaties en in virtuele netwerken centraal maken, afdwingen en registreren. Azure Firewall maakt gebruik van een statisch openbaar IP-adres voor uw virtuele-netwerkresources zodat externe firewalls verkeer dat afkomstig is van uw virtuele netwerk kunnen identificeren. 

Raadpleeg de [Azure firewall documentatie](../firewall/overview.md)voor meer informatie over Azure firewall.

![Firewalloverzicht](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>Netwerkbeveiligingsgroepen
U kunt netwerkverkeer naar en van Azure-resources in een virtueel Azure-netwerk filteren met een netwerkbeveiligingsgroep. Zie [Security Overview](../virtual-network/security-overview.md)(Engelstalig) voor meer informatie.

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Service-eindpunten
Met service-eindpunten van Virtual Network (VNet) kunt u de privé-adresruimte van uw virtuele netwerk en de identiteit van uw VNet uitbreiden naar Azure-services, via een directe verbinding. Met eindpunten kunt u uw kritieke Azure-serviceresources alleen beveiligen naar uw virtuele netwerken. Verkeer van uw VNet naar de Azure-service blijft altijd in het Microsoft Azure-backbonenetwerk. Zie [service-eind punten voor virtuele netwerken](../virtual-network/virtual-network-service-endpoints-overview.md)voor meer informatie.

![Service-eindpunten voor virtueel netwerk](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
Met [persoonlijke Azure-koppeling](../private-link/private-link-overview.md) kunt u toegang krijgen tot Azure PaaS-Services (bijvoorbeeld Azure Storage en SQL database) en Azure gehoste klanten-partner services via een persoonlijk eind punt in uw virtuele netwerk.
Verkeer tussen uw virtuele netwerk en de service wordt via het Microsoft-backbonenetwerk verplaatst. U hoeft uw service niet langer bloot te stellen aan het openbare internet. U kunt een eigen Private Link-service maken in uw virtuele netwerk en deze aanbieden bij klanten.

![Overzicht Privé-eindpunt](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>Services voor het leveren van toepassingen

In deze sectie worden de netwerk services in azure beschreven waarmee u toepassingen Content Delivery Network, Azure front-deur service, Traffic Manager, Load Balancer en Application Gateway kunt leveren.

|Service|Waarom gebruiken?|Scenario|
|---|---|---|
|[Content Delivery Network](#cdn)|Levert inhoud met hoge band breedte aan gebruikers. Cdn's slaat inhoud in de cache op rand servers op in POP-locaties (Point-of-Presence) die zich dicht bij eind gebruikers bevinden, om latentie te minimaliseren|<p>[CDN toevoegen aan een web-app](../cdn/cdn-add-to-web-app.md)</p> <p>[-Toegang tot opslag-blobs met behulp van een Azure CDN aangepast domein via HTTPS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Een aangepast domein toevoegen aan uw Azure CDN-eindpunt](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[HTTPS op een aangepast domein van Azure CDN configureren](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door Service](#frontdoor)|Hiermee kunt u de wereld wijde route ring voor uw webverkeer definiëren, beheren en bewaken door te optimaliseren voor de beste prestaties en directe globale failover voor hoge Beschik baarheid.|<p>[Een aangepast domein toevoegen aan de Azure Front Door Service](../frontdoor/front-door-custom-domain.md)</p> <p>[HTTPS configureren in een aangepast Front Door-domein](../frontdoor/front-door-custom-domain-https.md)</p><p>[Beleid voor geofiltering Web Application firewall instellen](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Traffic Manager](#trafficmanager)|Distribueert verkeer op basis van DNS naar Services in wereld wijde Azure-regio's, en biedt hoge Beschik baarheid en reactie snelheid|<p> [Verkeer routeren voor lage latentie](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Verkeer routeren naar een prioriteitseindpunt](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Verkeer beheren met gewogen eindpunten](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Verkeer routeren op basis van de geografische locatie van het eind punt](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Verkeer routeren op basis van het subnet van de gebruiker](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Load Balancer](#loadbalancer)|Voorziet in regionale taak verdeling door verkeer te routeren tussen beschikbaarheids zones en in uw VNets. Voorziet in interne taak verdeling door verkeer tussen en tussen uw resources te routeren om uw regionale toepassing te bouwen.|<p> [Internetverkeer gelijkmatig verdelen over VM's](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Taak verdeling van verkeer tussen virtuele machines in een virtueel netwerk](../load-balancer/tutorial-load-balancer-standard-internal-portal.md)<p>[Poort verkeer door sturen naar een specifieke poort op specifieke Vm's](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Taak verdeling en uitgaande regels configureren](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Application Gateway](#applicationgateway)|Azure Application Gateway is een load balancer voor webverkeer waarmee u het verkeer naar uw webapps kunt beheren.|<p>[Webverkeer omleiden met Azure Application Gateway](../application-gateway/quick-create-portal.md)</p><p>[Zelfstudie: Een toepassingsgateway configureren met TLS-beëindiging met de Azure-portal](../application-gateway/create-ssl-portal.md)</p><p>[Een toepassingsgateway maken met een omleiding op basis van een URL-pad](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>CDN (Content Delivery Network)
Azure Content Delivery Network (CDN) biedt ontwikkelaars een globale oplossing voor het snel leveren van inhoud met hoge bandbreedte door de inhoud op strategische, fysieke knooppunten in de hele wereld in de cache op te slaan. Zie voor meer informatie over Azure CDN [Azure Content Delivery Network](../cdn/cdn-overview.md)

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure front-deur service
Met de Azure Front Door Service kunt u de internationale routering van uw webverkeer definiëren, beheren en bewaken door te optimaliseren voor de beste prestaties en directe wereldwijde failover voor hoge beschikbaarheid. Met Front Door kunt u uw internationale (multiregionale) klant- en bedrijfstoepassingen transformeren in robuuste, hoogwaardige, gepersonaliseerde moderne toepassingen, API’s en inhoud die een wereldwijd bereik hebben met Azure. Zie [Azure front-deur](../frontdoor/front-door-overview.md)voor meer informatie.


### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Azure Traffic Manager is een op DNS gebaseerde load balancer waarmee u verkeer optimaal over services kunt verdelen in Azure-regio's wereldwijd, terwijl u over hoge beschikbaarheid en een hoge reactiesnelheid beschikt. Traffic Manager biedt een reeks verkeers routerings methoden voor het distribueren van verkeer zoals prioriteit, gewicht, prestaties, geografisch, meerdere waarden of subnet. Zie [Traffic Manager routerings methoden](../traffic-manager/traffic-manager-routing-methods.md)voor meer informatie over routerings methoden voor verkeer.

In het volgende diagram ziet u route ring op basis van eind punten met Traffic Manager:

![Azure Traffic Manager ' Priority ' Traffic-routerings methode](./media/networking-overview/priority.png)

Zie [Wat is Azure Traffic Manager?](../traffic-manager/traffic-manager-overview.md) voor meer informatie over Traffic Manager.

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
De Azure Load Balancer biedt hoge prestaties en lage latentie taak verdeling laag 4 voor alle UDP-en TCP-protocollen. Het beheert binnenkomende en uitgaande verbindingen. U kunt open bare en interne eind punten met taak verdeling configureren. U kunt regels definiëren voor het toewijzen van binnenkomende verbindingen aan bestemmingen van back-endservers door gebruik te maken van TCP-en HTTP-beschikbaarheids opties voor het beheren van de beschik baarheid van de service. Lees het artikel [overzicht van Load Balancer](../load-balancer/load-balancer-overview.md) voor meer informatie over Load Balancer.

In de volgende afbeelding ziet u een toepassing met meerdere lagen op Internet die gebruikmaakt van zowel externe als interne load balancers:

![Azure Load Balancer-voor beeld](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Azure Application Gateway is een load balancer voor webverkeer waarmee u het verkeer naar uw webapps kunt beheren. Het is een ADC (Application Delivery controller) als een service met verschillende mogelijkheden voor taak verdeling voor laag 7 voor uw toepassingen. Zie [Wat is Azure-toepassing gateway?](../application-gateway/overview.md)voor meer informatie.

Het volgende diagram toont op URL-pad gebaseerde route ring met Application Gateway.

![Application Gateway-voor beeld](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>Netwerk bewakings Services
In deze sectie worden de netwerk services in azure beschreven die u helpen bij het bewaken van uw netwerk bronnen-Network Watcher, ExpressRoute monitor, Azure Monitor en Virtual Network tikken.

|Service|Waarom gebruiken?|Scenario|
|---|---|---|
|[Network Watcher](#networkwatcher)|Helpt bij het bewaken en oplossen van verbindings problemen, helpt bij het vaststellen van problemen met VPN-, NSG-en route ring, het vastleggen van pakketten op uw virtuele machine, het automatiseren van diagnostische hulpprogram ma's voor activering met Azure Functions en Logic Apps|<p>[Probleem met VM-verkeersfilter vaststellen](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[VM-routeringsprobleem vaststellen](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[De communicatie tussen Vm's controleren](../network-watcher/connection-monitor.md)</p><p>[Communicatieproblemen tussen netwerken vaststellen](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Netwerkverkeer van en naar een VM in een logboek vastleggen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[Monitor voor ExpressRoute](#expressroutemonitor)|Biedt real-time bewaking van netwerk prestaties, Beschik baarheid en gebruik, helpt bij het automatisch detecteren van netwerk topologie, biedt snellere fout isolatie, detecteert tijdelijke netwerk problemen, helpt historische netwerk prestatie kenmerken te analyseren, ondersteunt meerdere abonnementen|<p>[Netwerkprestatiemeter configureren voor ExpressRoute](../expressroute/how-to-npm.md)</p><p>[Bewaking, metrische gegevens en waarschuwingen voor ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Helpt u inzicht te krijgen in de manier waarop uw toepassingen worden uitgevoerd en proactief problemen identificeren die van invloed zijn op deze en de resources waarvan ze afhankelijk zijn.|<p>[Metrische gegevens en waarschuwingen Traffic Manager](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Diagnostische gegevens van Azure monitor voor Standard Load Balancer](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Azure Firewall-logboeken en metrische gegevens bewaken](../firewall/tutorial-diagnostics.md)</p><p>[Controle en logboekregistratie van Azure Web Application Firewall](../frontdoor/waf-front-door-monitor.md)</p>|
|[Virtual Network Tik](#vnettap)|Biedt continue streaming van netwerk verkeer van de virtuele machine naar pakket verzamelaar, biedt oplossingen voor netwerk-en toepassings prestaties en hulpprogram ma's voor beveiligings analyse|[Een VNet-bron maken TAP](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Azure Network Watcher biedt hulpprogramma's voor het controleren, diagnosticeren en weergeven van metrische gegevens en het in- of uitschakelen van logboekregistratie voor resources in een virtueel Azure-netwerk. Zie [Wat is Network Watcher?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)voor meer informatie.
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>Monitor voor ExpressRoute
Zie [ExpressRoute controle, metrische gegevens en waarschuwingen](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)voor meer informatie over het weer geven van metrische gegevens voor ExpressRoute-circuits, bron logboeken en-waarschuwingen.
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Met Azure Monitor worden de beschikbaarheid en prestaties van uw toepassing gemaximaliseerd, door een uitgebreide oplossing te bieden voor het verzamelen en analyseren van, en werken met telemetriegegevens vanuit uw cloudomgeving en on-premises omgeving. Het helpt u begrijpen hoe uw toepassingen presteren en stelt proactief problemen vast die betrekking hebben op de toepassingen en de resources waarvan ze afhankelijk zijn. Zie [Azure monitor Overview](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)voor meer informatie.
### <a name="virtual-network-tap"></a><a name="vnettap"></a>Virtual Network Tik
Met het virtuele netwerk van Azure (Terminal Access Point) kunt u het netwerk verkeer van de virtuele machine continu streamen naar een netwerk pakket verzamelaar of een analyse programma. Het hulp programma Collector of Analytics wordt verschaft door een [virtuele netwerk apparaat](https://azure.microsoft.com/solutions/network-appliances/) -partner. 

In de volgende afbeelding ziet u hoe virtuele netwerk tikken werkt. 

![Hoe Virtual Network TIKT werkt](./media/networking-overview/virtual-network-tap-architecture.png)

Zie [Wat is Virtual Network Tik](../virtual-network/virtual-network-tap-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Maak uw eerste VNet en Verbind een aantal virtuele machines met de virtuele machine door de stappen in het artikel [Create Your First Virtual Network](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) uit te voeren.
- Verbind uw computer met een VNet door de stappen in het [artikel een punt-naar-site-verbinding configureren](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)te volt ooien.
- Taak verdeling van Internet verkeer naar open bare servers door de stappen in het artikel [een Internet gerichte Load Balancer maken](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) uit te voeren.
 
 
   
