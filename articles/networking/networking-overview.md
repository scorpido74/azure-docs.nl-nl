---
title: Overzicht van Azure-netwerkservices
description: Meer informatie over netwerkservices in Azure en hun mogelijkheden - connectiviteitsservices, toepassingsbeveiligingsservices, services voor het leveren van toepassingen en netwerkbewaking.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: 90eddea839d2f6ae5235ac6a391b40dd667ab401
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257832"
---
# <a name="azure-networking-services-overview"></a>Overzicht van Azure-netwerkservices

De netwerkservices in Azure bieden een verscheidenheid aan netwerkmogelijkheden die samen of afzonderlijk kunnen worden gebruikt. Klik op een van de volgende belangrijke mogelijkheden om meer over deze mogelijkheden te weten te komen:
- [**Connectiviteitsservices:**](#connect)Verbind Azure-bronnen en on-premises resources met behulp van een of een combinatie van deze netwerkservices in Azure - Virtual Network (VNet), Virtual WAN, ExpressRoute, VPN Gateway, Virtual Network NAT Gateway, Azure DNS, Peering-service en Azure Bastion.
- [**Diensten ter bescherming van toepassingen**](#protect) Bescherm uw toepassingen met behulp van een of een combinatie van deze netwerkservices in Azure - Private Link, DDoS-beveiliging, Firewall, Netwerkbeveiligingsgroepen, WebApplication Firewall en Virtual Network Endpoints.
- [**Levering van toepassingen**](#deliver) Lever toepassingen in het Azure-netwerk met behulp van een of een combinatie van deze netwerkservices in Azure - Content Delivery Network (CDN), Azure Front Door Service, Traffic Manager, Application Gateway, Internet Analyzer en Load Balancer.
- [**Netwerkbewaking**](#monitor) – Controleer uw netwerkbronnen met behulp van een of een combinatie van deze netwerkservices in Azure - Network Watcher, ExpressRoute Monitor, Azure Monitor of VNet Terminal Access Point (TAP).

## <a name="connectivity-services"></a><a name="connect"></a>Connectiviteitsservices
 
In deze sectie worden services beschreven die connectiviteit bieden tussen Azure-resources, connectiviteit van een on-premises netwerk tot Azure-resources en branch-connectiviteit in Azure - Virtual Network (VNet), Virtual WAN, ExpressRoute, VPN Gateway, NAT-gateway voor virtueel netwerk, Azure DNS, Azure Peering-service en Azure Bastion.

|Service|Waarom gebruiken?|Scenario's|
|---|---|---|
|[Virtueel netwerk](#vnet)|Hiermee kunnen Azure-bronnen veilig met elkaar, het internet en on-premises netwerken communiceren.| <p>[Netwerkverkeer filteren](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Netwerkverkeer routeren](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Netwerktoegang tot resources beperken](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Virtuele netwerken verbinden](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute ExpressRoute](#expressroute)|Breidt uw on-premises netwerken uit naar de Microsoft-cloud via een privéverbinding die wordt gefaciliteerd door een connectiviteitsprovider.|<p>[Een ExpressRoute-circuit maken en wijzigen](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Peering voor een ExpressRoute-circuit maken en wijzigen](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Een VNet koppelen aan een ExpressRoute-circuit](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Routefilters voor ExpressRoute-circuits configureren en beheren](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN Gateway](#vpngateway)|Hiermee verzendt u versleuteld verkeer tussen een virtueel Azure-netwerk en een on-premises locatie via het openbare internet.|<p>[Site-to-site-verbindingen](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[VNet-naar-VNet-verbindingen](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Point-to-site verbindingen](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[Virtuele WAN](#virtualwan)|Optimaliseert en automatiseert branch connectiviteit naar en door Azure. Azure-regio's dienen als hubs waarmee u kiezen om uw branches aan te sluiten.|<p>[Site-to-site verbindingen](../virtual-wan/virtual-wan-site-to-site-portal.md), [ExpressRoute-verbindingen](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Host DNS-domeinen die naamomzetting bieden met behulp van Microsoft Azure-infrastructuur.|<p>[Uw domein hosten in Azure DNS](../dns/dns-delegate-domain-azure-dns.md)</p><p>[DNS-records maken voor een web-app](../dns/dns-web-sites-custom-domain.md)</p> <p>[Een aliasrecord maken voor Traffic Manager](../dns/tutorial-alias-tm.md)</p> <p>[Een aliasrecord maken voor openbaar IP-adres](../dns/tutorial-alias-pip.md)</p> <p>[Een aliasrecord maken voor zoneresourcerecord](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|Configureer beveiligde en naadloze RDP-/SSH-connectiviteit in je virtuele machine, rechtstreeks in de Azure-portal via SSL. Wanneer u verbinding maakt via Azure Bastion, hebben uw virtuele machines geen openbaar IP-adres nodig|<p>[Een Azure Bastion-host maken](../bastion/bastion-create-host-portal.md)</p><p>[Verbinding maken met SSH met een Linux-VM](../bastion/bastion-connect-vm-ssh.md)</p><p>[Verbinding maken met RDP met een Windows-vm](../bastion/bastion-connect-vm-rdp.md)</p>|
|[NAT-gateway voor virtueel netwerk](#nat)|Maak een NAT-gateway om uitgaande connectiviteit te bieden voor een virtuele machine.|<p>[Een NAT-gateway maken](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Azure-peeringservice (voorbeeld)](#azurepeeringservice)|Werk samen met serviceproviders voor een optimale en betrouwbare routering naar de Microsoft-cloud via het openbare netwerk.|<p>[Azure-peeringservice registreren](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>Virtueel netwerk

Azure Virtual Network (VNet) is de fundamentele bouwsteen voor uw privénetwerk in Azure. U een VNets gebruiken om:
- **Communiceren tussen Azure-resources:** u VM's en verschillende andere typen Azure-resources implementeren in een virtueel netwerk, zoals Azure App Service-omgevingen, de Azure Kubernetes Service (AKS) en Azure Virtual Machine Scale Sets. Zie [Integratie van virtuele netwerkservices](../virtual-network/virtual-network-for-azure-services.md) voor een volledige lijst met Azure-resources die u in een virtueel netwerk kunt implementeren.
- **Communiceer met elkaar:** u virtuele netwerken met elkaar verbinden, waardoor resources in een virtueel netwerk met elkaar kunnen communiceren met behulp van virtuele netwerkpeering. De virtuele netwerken die u met elkaar verbindt, kunnen zich in dezelfde of verschillende Azure-regio's bevinden. Zie [Virtueel netwerkpeeren](../virtual-network/virtual-network-peering-overview.md)voor meer informatie.
- **Communiceren met het internet**: Alle bronnen in een VNet kunnen standaard uitgaand communiceren naar het internet. U kunt binnenkomend communiceren met een resource door er een openbaar IP-adres of een openbare Load Balancer aan toe te wijzen. U ook [openbare IP-adressen](../virtual-network/virtual-network-public-ip-address.md) of openbare [load balancer](../load-balancer/load-balancer-overview.md) gebruiken om uw uitgaande verbindingen te beheren.
- **Communiceer met on-premises netwerken:** u uw on-premises computers en netwerken verbinden met een virtueel netwerk via [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ExpressRoute.](../expressroute/expressroute-introduction.md)

Zie Wat is Azure Virtual Network voor meer [informatie?](../virtual-network/virtual-networks-overview.md)

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute ExpressRoute
Met ExpressRoute u uw on-premises netwerken uitbreiden naar de Microsoft-cloud via een privéverbinding die wordt gefaciliteerd door een connectiviteitsprovider. Deze verbinding is een privéverbinding. Verkeer gaat niet via internet. Met ExpressRoute kunt u verbindingen tot stand brengen met Microsoft Cloud-services, zoals Microsoft Azure, Office 365 en Dynamics 365.  Zie [Wat is ExpressRoute voor](../expressroute/expressroute-introduction.md)meer informatie?

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
VPN-gateway helpt u versleutelde cross-premises verbindingen met uw virtuele netwerk te maken vanaf on-premises locaties of versleutelde verbindingen tussen VNets te maken. Er zijn verschillende configuraties beschikbaar voor VPN Gateway-verbindingen, zoals site-to-site, point-to-site of VNet naar VNet.
Het volgende diagram illustreert meerdere site-to-site VPN-verbindingen met hetzelfde virtuele netwerk.

![Site-to-Site Azure VPN-gatewayverbindingen](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

Zie [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)voor meer informatie over verschillende soorten VPN-verbindingen.

### <a name="virtual-wan"></a><a name="virtualwan"></a>Virtuele WAN
Azure Virtual WAN is een netwerkservice die geoptimaliseerde en geautomatiseerde branchconnectiviteit biedt met en door Azure. Azure-regio's dienen als hubs waarmee u kiezen om uw branches aan te sluiten. U de Azure-backbone gebruiken om ook branches met elkaar te verbinden en te genieten van branch-to-VNet-connectiviteit. Azure Virtual WAN brengt veel Azure-cloudconnectiviteitsservices samen, zoals site-to-site VPN, ExpressRoute, point-to-site-gebruikersVPN in één operationele interface. Connectiviteit met Azure VNets wordt tot stand gebracht door gebruik te maken van virtuele netwerkverbindingen. Zie [Wat is het virtuele WAN van Azure?](../virtual-wan/virtual-wan-about.md)Voor meer informatie.

![Virtual WAN-diagram](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS is een service voor het hosten van DNS-domeinen die naamomzetting verzorgt met behulp van de Microsoft Azure-infrastructuur. Door uw domeinen in Azure te hosten, kunt u uw DNS-records met dezelfde referenties, API's, hulpprogramma's en facturering beheren als voor uw andere Azure-services. Zie [Wat is Azure DNS?](../dns/dns-overview.md)

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
De Azure Bastion-service is een nieuwe, volledig platformbeheerde PaaS-service die u inuw virtuele netwerk indient. Het biedt veilige en naadloze RDP/SSH-connectiviteit met uw virtuele machines rechtstreeks in de Azure-portal via SSL. Wanneer u verbinding maakt met Azure Bastion, hebben uw virtuele machines geen openbaar IP nodig. Zie Wat is Azure Bastion voor meer [informatie?](../bastion/bastion-overview.md)

![Azure Bastion-architectuur](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>NAT-gateway voor virtueel netwerk
Virtual Network NAT (network address translation) vereenvoudigt de internetverbinding met alleen-het-netwerk voor virtuele netwerken. Wanneer geconfigureerd op een subnet, alle uitgaande connectiviteit maakt gebruik van uw opgegeven statische openbare IP-adressen. Uitgaande connectiviteit is mogelijk zonder load balancer of openbare IP-adressen die direct aan virtuele machines zijn gekoppeld. Zie [Wat is de NAT-gateway voor virtuele netwerken voor](../virtual-network/nat-overview.md) meer informatie? 

![Nat-gateway voor virtueel netwerk](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a>Azure-peeringservice
Azure Peering-service verbetert de klantconnectiviteit met Microsoft-cloudservices zoals Office 365, Dynamics 365, Software as a Service (SaaS)-services, Azure of Microsoft-services die toegankelijk zijn via het openbare internet. Zie [Wat is Azure Peering Service voor](../peering-service/about.md)meer informatie? .

## <a name="application-protection-services"></a><a name="protect"></a>Diensten ter bescherming van toepassingen

In deze sectie worden netwerkservices in Azure beschreven die uw netwerkbronnen helpen beschermen - Uw toepassingen beveiligen met een of een combinatie van deze netwerkservices in Azure - Private Link, DDoS-beveiliging, Firewall, Netwerkbeveiligingsgroepen, web Toepassingsfirewall en eindpunten voor virtuele netwerken.

|Service|Waarom gebruiken?|Scenario|
|---|---|---|
|[DDoS-bescherming](#ddosprotection) |Hoge beschikbaarheid voor uw toepassingen met bescherming tegen overtollige IP-verkeerskosten|[Azure DDoS-beveiliging beheren](../virtual-network/manage-ddos-protection.md)|
|[Web Application Firewall](#waf)|<p>[Azure WAF met Application Gateway](../web-application-firewall/ag/ag-overview.md) biedt regionale bescherming aan entiteiten in openbare en private adresruimte</p><p>[Azure WAF met Front Door](../web-application-firewall/afds/afds-overview.md) biedt bescherming aan de netwerkrand voor openbare eindpunten.</p>|<p>[Regels voor botbeveiliging configureren](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Aangepast antwoordcode configureren](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[IP-beperkingsregels configureren](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Regel tarieflimiet configureren](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Azure Firewall](#firewall)|Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt. Het is een volledig stateful firewall als een service met ingebouwde hoge beschikbaarheid en onbeperkte cloudschaalbaarheid.|<p>[Een Azure Firewall implementeren in een Vnet](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[- Een Azure Firewall implementeren in een hybride netwerk](../firewall/tutorial-hybrid-ps.md)</p> <p>[Binnenkomend verkeer filteren met Azure Firewall DNAT](../firewall/tutorial-firewall-dnat.md)</p>|
|[Netwerkbeveiligingsgroepen](#nsg)|Volledige gedetailleerde gedistribueerde end node-besturingselement op VM/subnet voor alle netwerkverkeersstromen|[Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/tutorial-filter-network-traffic.md)|
|[Service-eindpunten voor virtueel netwerk](#serviceendpoints)|Hiermee u de netwerktoegang tot bepaalde Azure-servicebronnen beperken tot een virtueel netwerksubnet|[Netwerktoegang tot PaaS-resources beperken](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[Private Link](#privatelink)|Hiermee u Azure PaaS-services (bijvoorbeeld Azure Storage en SQL Database) en Azure-services die eigendom zijn van de klant/partner services hebben via een privéeindpunt in uw virtuele netwerk.|<p>[Een privé-eindpunt maken](../private-link/create-private-endpoint-portal.md)</p><p>[Een Private Link-service maken](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS-bescherming 
[Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) biedt tegenmaatregelen tegen de meest geavanceerde DDoS-bedreigingen. De service biedt verbeterde DDoS-mitigatiemogelijkheden voor uw toepassing en resources die zijn geïmplementeerd in uw virtuele netwerken. Bovendien hebben klanten die Azure DDoS Protection gebruiken toegang tot DDoS Rapid Response-ondersteuning om DDoS-experts bij een actieve aanval in te schakelen.

![DDoS Protection](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Web Application Firewall

[Azure Web Application Firewall](../web-application-firewall/overview.md) (WAF) biedt bescherming aan uw webtoepassingen tegen veelvoorkomende webexploits en kwetsbaarheden zoals SQL-injectie en cross site scripting. Azure WAF biedt out-of-box bescherming tegen OWASP top 10 kwetsbaarheden via beheerde regels. Daarnaast kunnen klanten ook aangepaste regels configureren, die door de klant beheerde regels zijn om extra bescherming te bieden op basis van het IP-bereik van de bron, en kenmerken aanvragen, zoals kopteksten, cookies, formuliergegevensvelden of querytekenreeksen.

Klanten kunnen ervoor kiezen azure WAF te implementeren [met Application Gateway,](../application-gateway/waf-overview.md) die regionale bescherming biedt aan entiteiten in openbare en privéadresruimte. Klanten kunnen er ook voor kiezen om Azure WAF te implementeren [met Front Door,](../frontdoor/waf-overview.md) dat bescherming biedt aan de netwerkrand voor openbare eindpunten.

![Web Application Firewall](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt. Met Azure Firewall u toepassingen en netwerkconnectiviteitsbeleid voor abonnementen en virtuele netwerken centraal maken, afdwingen en registreren. Azure Firewall maakt gebruik van een statisch openbaar IP-adres voor uw virtuele-netwerkresources zodat externe firewalls verkeer dat afkomstig is van uw virtuele netwerk kunnen identificeren. 

Zie de [Azure Firewall-documentatie](../firewall/overview.md)voor meer informatie over Azure Firewall.

![Firewalloverzicht](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>Netwerkbeveiligingsgroepen
U kunt netwerkverkeer naar en van Azure-resources in een virtueel Azure-netwerk filteren met een netwerkbeveiligingsgroep. Zie [Beveiligingsoverzicht voor](../virtual-network/security-overview.md)meer informatie.

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Service-eindpunten
Met service-eindpunten van Virtual Network (VNet) kunt u de privé-adresruimte van uw virtuele netwerk en de identiteit van uw VNet uitbreiden naar Azure-services, via een directe verbinding. Met eindpunten kunt u uw kritieke Azure-serviceresources alleen beveiligen naar uw virtuele netwerken. Verkeer van uw VNet naar de Azure-service blijft altijd in het Microsoft Azure-backbonenetwerk. Zie [Eindpunten voor virtuele netwerkservice voor](../virtual-network/virtual-network-service-endpoints-overview.md)meer informatie.

![Service-eindpunten voor virtueel netwerk](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
[Met Azure Private Link](../private-link/private-link-overview.md) heeft u toegang tot Azure PaaS-services (bijvoorbeeld Azure Storage en SQL Database) en Azure gehoste client-owned/partnerservices via een privéeindpunt in uw virtuele netwerk.
Verkeer tussen uw virtuele netwerk en de service reist het Microsoft-backbone-netwerk. Het blootstellen van uw dienst aan het openbare internet is niet langer nodig. U uw eigen privélinkservice in uw virtuele netwerk maken en deze aan uw klanten leveren.

![Overzicht van privéeindpunten](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>Levering van toepassingen

In deze sectie worden netwerkservices in Azure beschreven die helpen bij het leveren van toepassingen - Network Watcher, ExpressRoute Monitor, Azure Monitor of VNet Terminal Access Point (TAP).

|Service|Waarom gebruiken?|Scenario|
|---|---|---|
|[Content Delivery Network](#cdn)|Levert inhoud met een hoge bandbreedte aan gebruikers. CDN's slaan inhoud in de cache op edge-servers op poplocaties (point-of-presence) die dicht bij eindgebruikers staan, om de latentie te minimaliseren|<p>[CDN toevoegen aan een web-app](../cdn/cdn-add-to-web-app.md)</p> <p>[- Toegang tot opslagblobs met een aangepast Azure CDN-domein via HTTPS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Een aangepast domein toevoegen aan uw Azure CDN-eindpunt](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[HTTPS configureren op een aangepast Azure CDN-domein](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door Service](#frontdoor)|Hiermee u de globale routering voor uw webverkeer definiëren, beheren en bewaken door te optimaliseren voor de beste prestaties en directe wereldwijde failover voor hoge beschikbaarheid.|<p>[Een aangepast domein toevoegen aan uw Azure Front Door-service](../frontdoor/front-door-custom-domain.md)</p> <p>[HTTPS configureren in een aangepast Front Door-domein](../frontdoor/front-door-custom-domain-https.md)</p><p>[Firewallbeleid voor geofilters instellen](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Verkeersbeheerder](#trafficmanager)|Distribueert verkeer op basis van DNS naar services over wereldwijde Azure-regio's, terwijl het tegelijkertijd een hoge beschikbaarheid en responsiviteit biedt|<p> [Verkeer routeren voor lage latentie](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Verkeer routeren naar een prioriteitseindpunt](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Verkeer beheren met gewogen eindpunten](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Routeverkeer op basis van geografische locatie van het eindpunt](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Verkeer routeren op basis van het subnet van de gebruiker](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Load balancer](#loadbalancer)|Biedt regionale load-balancing door verkeer over beschikbaarheidszones en naar uw VNets te routeren. Biedt interne taakverdeling door het verkeer over en tussen uw resources te routeren om uw regionale toepassing te bouwen.|<p> [Internetverkeer gelijkmatig verdelen over VM's](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Load-balance verkeer over VM's binnen een virtueel netwerk](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[Havenleidingverkeer naar een specifieke poort op specifieke VM's](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Load balancing en uitgaande regels configureren](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Application Gateway](#applicationgateway)|Azure Application Gateway is een load balancer voor webverkeer waarmee u het verkeer naar uw webapps kunt beheren.|<p>[Webverkeer omleiden met Azure Application Gateway](../application-gateway/quick-create-portal.md)</p><p>[Een toepassingsgateway configureren met SSL-beëindiging](../application-gateway/create-ssl-portal.md)</p><p>[Een toepassingsgateway maken met een omleiding op basis van een URL-pad](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>Content Delivery Network
Azure Content Delivery Network (CDN) biedt ontwikkelaars een globale oplossing voor het snel leveren van inhoud met hoge bandbreedte door de inhoud op strategische, fysieke knooppunten in de hele wereld in de cache op te slaan. Zie [Azure Content Delivery Network](../cdn/cdn-overview.md) voor meer informatie over Azure CDN

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure Front Door-service
Met de Azure Front Door Service kunt u de internationale routering van uw webverkeer definiëren, beheren en bewaken door te optimaliseren voor de beste prestaties en directe wereldwijde failover voor hoge beschikbaarheid. Met Front Door kunt u uw internationale (multiregionale) klant- en bedrijfstoepassingen transformeren in robuuste, hoogwaardige, gepersonaliseerde moderne toepassingen, API’s en inhoud die een wereldwijd bereik hebben met Azure. Zie [Azure Front Door](../frontdoor/front-door-overview.md)voor meer informatie.


### <a name="traffic-manager"></a><a name="trafficmanager"></a>Verkeersbeheerder

Azure Traffic Manager is een op DNS gebaseerde load balancer waarmee u verkeer optimaal over services kunt verdelen in Azure-regio's wereldwijd, terwijl u over hoge beschikbaarheid en een hoge reactiesnelheid beschikt. Traffic Manager biedt een reeks verkeersrouteringsmethoden om verkeer te distribueren, zoals prioriteit, gewogen, prestaties, geografische, multi-value of subnet. Zie [Routeringsmethoden voor verkeersbeheer](../traffic-manager/traffic-manager-routing-methods.md)voor meer informatie over routeringsmethoden voor verkeer .

In het volgende diagram ziet u op eindpuntprioriteit gebaseerde routering met Traffic Manager:

![Methode voor het routeren van het azure Traffic Manager 'Priority'](./media/networking-overview/priority.png)

Zie Wat is Azure [Traffic Manager voor](../traffic-manager/traffic-manager-overview.md) meer informatie over Traffic Manager?

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load balancer
De Azure Load Balancer biedt krachtige, lage latentie Layer 4 load-balancing voor alle UDP- en TCP-protocollen. Het beheert inkomende en uitgaande verbindingen. U openbare en interne eindpunten voor belastingsgebalanceerde punten configureren. U regels definiëren om binnenkomende verbindingen met back-endpoolbestemmingen in kaart te brengen met behulp van TCP- en HTTP-opties voor het onderzoeken van de status om de beschikbaarheid van services te beheren. Lees het [overzichtsartikel Load Balancer](../load-balancer/load-balancer-overview.md) voor meer informatie over Load Balancer.

De volgende afbeelding toont een op internet gerichte multi-tier toepassing die zowel externe als interne load balancers gebruikt:

![Voorbeeld van Azure Load Balancer](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Azure Application Gateway is een load balancer voor webverkeer waarmee u het verkeer naar uw webapps kunt beheren. Het is een Application Delivery Controller (ADC) als een dienst, die verschillende layer 7 load-balancing mogelijkheden biedt voor uw toepassingen. Zie Wat is Azure Application Gateway voor meer [informatie?](../application-gateway/overview.md).

In het volgende diagram ziet u url-padgebaseerde routering met Application Gateway.

![Voorbeeld van toepassingsgateway](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>Netwerkbewakingsdiensten
In deze sectie worden netwerkservices in Azure beschreven die helpen bij het bewaken van uw netwerkbronnen - Network Watcher, ExpressRoute Monitor, Azure Monitor en Tap voor virtueel netwerk.

|Service|Waarom gebruiken?|Scenario|
|---|---|---|
|[Network Watcher](#networkwatcher)|Helpt bij het monitoren en oplossen van verbindingsproblemen, helpt bij het diagnosticeren van VPN-, NSG- en routeringsproblemen, het vastleggen van pakketten op uw VM, automatiseert het activeren van diagnostische hulpprogramma's met Azure Functions en Logic Apps|<p>[Probleem met VM-verkeersfilter vaststellen](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[VM-routeringsprobleem vaststellen](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Communicatie tussen VM's bewaken](../network-watcher/connection-monitor.md)</p><p>[Communicatieproblemen tussen netwerken vaststellen](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Netwerkverkeer van en naar een VM in een logboek vastleggen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[ExpressRoute-monitor](#expressroutemonitor)|Biedt real-time monitoring van netwerkprestaties, beschikbaarheid en gebruik, helpt bij het automatisch detecteren van netwerktopologie, biedt snellere foutisolatie, detecteert tijdelijke netwerkproblemen, helpt bij het analyseren van historische netwerkprestaties kenmerken, ondersteunt multi-abonnement|<p>[Netwerkprestatiemeter configureren voor ExpressRoute](../expressroute/how-to-npm.md)</p><p>[Bewaking, metrische gegevens en waarschuwingen voor ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Helpt u te begrijpen hoe uw toepassingen presteren en identificeert proactief problemen die van invloed zijn op hen en de resources waarvan ze afhankelijk zijn.|<p>[Statistieken en waarschuwingen voor Verkeersbeheer](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Azure-monitordiagnostiek voor StandaardloadBalancer](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Azure Firewall-logboeken en metrische gegevens bewaken](../firewall/tutorial-diagnostics.md)</p><p>[Controle en logboekregistratie van Azure Web Application Firewall](../frontdoor/waf-front-door-monitor.md)</p>|
|[Virtueel netwerk TAP](#vnettap)|Biedt continue streaming van netwerkverkeer voor virtuele machines naar pakketverzamelaar, maakt oplossingen voor netwerk- en toepassingsprestatiebeheer en beveiligingsanalysetools mogelijk|[Een VNet TAP-bron maken](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Azure Network Watcher biedt hulpprogramma's voor het controleren, diagnosticeren en weergeven van metrische gegevens en het in- of uitschakelen van logboekregistratie voor resources in een virtueel Azure-netwerk. Zie Wat is Network Watcher voor meer [informatie?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute-monitor
Zie [ExpressRoute-monitoring, statistieken en waarschuwingen](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)voor meer informatie over hoe expressroute-circuitstatistieken, diagnostische logboeken en waarschuwingen worden weergegeven.
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Met Azure Monitor worden de beschikbaarheid en prestaties van uw toepassing gemaximaliseerd, door een uitgebreide oplossing te bieden voor het verzamelen en analyseren van, en werken met telemetriegegevens vanuit uw cloudomgeving en on-premises omgeving. Het helpt u begrijpen hoe uw toepassingen presteren en stelt proactief problemen vast die betrekking hebben op de toepassingen en de resources waarvan ze afhankelijk zijn. Zie [Overzicht van Azure-monitor](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)voor meer informatie .
### <a name="virtual-network-tap"></a><a name="vnettap"></a>Virtueel netwerk TAP
Azure virtual network TAP (Terminal Access Point) stelt u in staat om uw netwerkverkeer voor virtuele machines continu te streamen naar een netwerkpakketverzamelaar of analysetool. De collector- of analysetool wordt geleverd door een [netwerkpartner voor virtuele apparaten.](https://azure.microsoft.com/solutions/network-appliances/) 

In de volgende afbeelding ziet u hoe het virtuele netwerk TAP werkt. 

![Hoe het virtuele netwerk TAP werkt](./media/networking-overview/virtual-network-tap-architecture.png)

Zie [Wat is Virtual Network TAP voor](../virtual-network/virtual-network-tap-overview.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Maak uw eerste VNet en sluit er een paar VM's op aan door de stappen in het artikel [Uw eerste virtuele netwerk maken](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) in te vullen.
- Sluit uw computer aan op een VNet door de stappen in het [artikel Een punt-naar-site-verbindingsartikel configureren](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)uit te voeren.
- Laadbalans internetverkeer naar openbare servers door de stappen in het artikel [Een internetgebaseerde load balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) te maken.
 
 
   
