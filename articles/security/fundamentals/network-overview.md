---
title: Netwerkbeveiligingsconcepten en -vereisten in Azure | Microsoft Documenten
description: In dit artikel vindt u basisuitleg over de belangrijkste netwerkbeveiligingsconcepten en -vereisten en informatie over wat Azure in elk van deze gebieden biedt.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 496ee1bc97f6b72e09a62ae3491af7ccc7328583
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811107"
---
# <a name="azure-network-security-overview"></a>Overzicht van Azure-netwerkbeveiliging

Netwerkbeveiliging kan worden gedefinieerd als het proces om resources te beschermen tegen ongeautoriseerde toegang of aanvallen door besturingselementen toe te passen op netwerkverkeer. Het doel is ervoor te zorgen dat alleen legitiem verkeer is toegestaan. Azure bevat een robuuste netwerkinfrastructuur ter ondersteuning van de vereisten voor toepassingen en serviceconnectiviteit. Netwerkconnectiviteit is mogelijk tussen resources in Azure, tussen on-premises en azure gehoste resources en van en naar internet en Azure.

In dit artikel worden enkele opties die Azure biedt op het gebied van netwerkbeveiliging. U meer informatie krijgen over:

* Azure-netwerken
* Netwerktoegangsbeheer
* Azure Firewall
* Beveiligde toegang op afstand en cross-premises connectiviteit
* Beschikbaarheid
* Naamomzetting
* Perimeter netwerk (DMZ) architectuur
* Azure DDoS Protection
* Azure Front Door
* Traffic Manager
* Monitoring en detectie van bedreigingen

## <a name="azure-networking"></a>Azure-netwerken

Azure vereist dat virtuele machines worden aangesloten op een Azure Virtual Network. Een virtueel netwerk is een logische constructie die is gebouwd bovenop de fysieke Azure-netwerkstructuur. Elk virtueel netwerk is geïsoleerd van alle andere virtuele netwerken. Dit helpt ervoor te zorgen dat netwerkverkeer in uw implementaties niet toegankelijk is voor andere Azure-klanten.

Meer informatie:

* [Overzicht van virtueel netwerk](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Netwerktoegangsbeheer

Netwerktoegangscontrole is de handeling van het beperken van de connectiviteit van en naar specifieke apparaten of subnetten binnen een virtueel netwerk. Het doel van netwerktoegangscontrole is om de toegang tot uw virtuele machines en services te beperken tot goedgekeurde gebruikers en apparaten. Toegangsbesturingselementen zijn gebaseerd op beslissingen om verbindingen van en naar uw virtuele machine of service toe te staan of te weigeren.

Azure ondersteunt verschillende typen netwerktoegangsbeheer, zoals:

* Netwerklaagbesturingselement
* Routecontrole en gedwongen tunneling
* Virtuele netwerkbeveiligingsapparaten

### <a name="network-layer-control"></a>Netwerklaagbesturingselement

Elke veilige implementatie vereist enige mate van netwerktoegangscontrole. Het doel van netwerktoegangscontrole is om de communicatie met virtuele machines te beperken tot de benodigde systemen. Andere communicatiepogingen worden geblokkeerd.

> [!NOTE]
> Storage Firewalls worden behandeld in het [overzichtsartikel over azure-opslagbeveiliging](storage-overview.md)

#### <a name="network-security-rules-nsgs"></a>Netwerkbeveiligingsregels (NSGs)

Als u toegangsbeheer op basis van netwerkniveau nodig hebt (op basis van IP-adres en de TCP- of UDP-protocollen), u Network Security Groups (NSG's) gebruiken. Een NSG is een eenvoudige, stateful, packet filtering firewall, en het stelt u in staat om toegang te beheren op basis van een [5-tuple](https://www.techopedia.com/definition/28190/5-tuple). NSGs bevatten functionaliteit om het beheer te vereenvoudigen en de kans op configuratiefouten te verkleinen:

* **Uitgebreide beveiligingsregels** vereenvoudigen de NSG-regeldefinitie en stellen u in staat om complexe regels te maken in plaats van meerdere eenvoudige regels te maken om hetzelfde resultaat te bereiken.
* **Servicetags** zijn door Microsoft gemaakte labels die een groep IP-adressen vertegenwoordigen. Ze worden dynamisch bijgewerkt om IP-bereiken op te nemen die voldoen aan de voorwaarden die opname in het label definiëren. Als u bijvoorbeeld een regel wilt maken die van toepassing is op alle Azure-opslag in de oostelijke regio, u Storage.EastUS gebruiken
* **Met toepassingsbeveiligingsgroepen** u resources implementeren in toepassingsgroepen en de toegang tot deze bronnen beheren door regels te maken die deze toepassingsgroepen gebruiken. Als u bijvoorbeeld webservers hebt geïmplementeerd in de toepassingsgroep 'Webservers' u een regel maken die een NSG toepast waarmee 443 verkeer van het internet wordt toegestaan op alle systemen in de toepassingsgroep 'Webservers'.

NSGs bieden geen inspectie van de toepassingslaag of geverifieerde toegangscontroles.

Meer informatie:

* [Netwerkbeveiligingsgroepen](../../virtual-network/security-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>ASC just-in-time VM-toegang

[Azure security center](../../security-center/security-center-intro.md) kan de NSG's beheren op VM's en de toegang tot de VM vergrendelen totdat een gebruiker met de juiste functiegebaseerde toegangscontrole [RBAC-machtigingen](/azure/role-based-access-control/overview) toegang vraagt. Wanneer de gebruiker is geautoriseerd, voert ASC wijzigingen door aan de NSG's om gedurende de opgegeven tijd toegang te krijgen tot geselecteerde poorten. Wanneer de tijd verstrijkt worden de NSG's hersteld naar hun vorige beveiligde status.

Meer informatie:

* [Azure Security Center Just in Time Access](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Service-eindpunten

Serviceeindpunten zijn een andere manier om controle over uw verkeer toe te passen. U de communicatie met ondersteunde services beperken tot alleen uw VNets via een directe verbinding. Verkeer van uw VNet naar de opgegeven Azure-service blijft op het Microsoft Azure-backbonenetwerk.  

Meer informatie:

* [Service-eindpunten](../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Routecontrole en gedwongen tunneling

De mogelijkheid om routeringsgedrag op uw virtuele netwerken te beheren is van cruciaal belang. Als routering onjuist is geconfigureerd, kunnen toepassingen en services die op uw virtuele machine worden gehost, verbinding maken met ongeautoriseerde apparaten, waaronder systemen die eigendom zijn van en worden beheerd door potentiële aanvallers.

Azure-netwerken ondersteunt de mogelijkheid om het routeringsgedrag voor netwerkverkeer op uw virtuele netwerken aan te passen. Hiermee u de standaardrouteringstabelvermeldingen in uw virtuele netwerk wijzigen. Controle over routegedrag helpt u ervoor te zorgen dat al het verkeer van een bepaald apparaat of een bepaalde groep apparaten uw virtuele netwerk binnenkomt of verlaat via een specifieke locatie.

U hebt bijvoorbeeld een virtueel netwerkbeveiligingstoestel in uw virtuele netwerk. U wilt ervoor zorgen dat al het verkeer van en naar uw virtuele netwerk via dat virtuele beveiligingsapparaat gaat. U dit doen door [Gebruikersgedefinieerde routes](../../virtual-network/virtual-networks-udr-overview.md) (UDR's) in Azure te configureren.

[Gedwongen tunneling](https://www.petri.com/azure-forced-tunneling) is een mechanisme dat u gebruiken om ervoor te zorgen dat uw diensten geen verbinding met apparaten op het internet mogen starten. Houd er rekening mee dat dit anders is dan het accepteren van binnenkomende verbindingen en er vervolgens op reageren. Front-end webservers moeten reageren op verzoeken van internethosts, en dus is internetverkeer toegestaan om deze webservers binnen te komen en de webservers mogen reageren.

Wat u niet wilt toestaan is een front-end webserver om een uitgaande aanvraag te starten. Dergelijke verzoeken kunnen een beveiligingsrisico vormen omdat deze verbindingen kunnen worden gebruikt om malware te downloaden. Zelfs als u wilt dat deze front-end servers uitgaande verzoeken naar het internet initiëren, wilt u misschien hen dwingen om door uw on-premises webproxy's te gaan. Hierdoor u profiteren van URL-filtering en -registratie.

In plaats daarvan zou u gedwongen tunneling willen gebruiken om dit te voorkomen. Wanneer u gedwongen tunneling inschakelt, worden alle verbindingen met het internet via uw on-premises gateway geforceerd. U gedwongen tunneling configureren door gebruik te maken van UDR's.

Meer informatie:

* [Wat zijn door de gebruiker gedefinieerde routes en IP-forwarding](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Virtuele netwerkbeveiligingsapparaten

Terwijl NSGs, UDR's en gedwongen tunneling u een beveiligingsniveau bieden op het netwerk en de transportlagen van het [OSI-model,](https://en.wikipedia.org/wiki/OSI_model)wilt u mogelijk ook beveiliging inschakelen op een hoger niveau dan het netwerk.

Uw beveiligingsvereisten kunnen bijvoorbeeld bestaan uit:

* Verificatie en autorisatie voordat u toegang geeft tot uw toepassing
* Inbraakdetectie en inbraakrespons
* Inspectie van toepassingslagen voor protocollen op hoog niveau
* URL-filtering
* Antivirus op netwerkniveau en antimalware
* Anti-bot bescherming
* Toegangsbeheer voor toepassingen
* Aanvullende DDoS-beveiliging (boven de DDoS-beveiliging die wordt geboden door de Azure-fabric zelf)

U hebt toegang tot deze verbeterde netwerkbeveiligingsfuncties met behulp van een Azure-partneroplossing. U de meest recente Azure-oplossingen voor netwerkbeveiliging van Azure vinden door naar de [Azure Marketplace](https://azure.microsoft.com/marketplace/)te gaan en te zoeken naar 'beveiliging' en 'netwerkbeveiliging'.

## <a name="azure-firewall"></a>Azure Firewall

Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt. Het is een volledige stateful firewall als een service met ingebouwde hoge beschikbaarheid en onbeperkte cloudschaalbaarheid. Enkele functies zijn:

* Hoge beschikbaarheid
* Schaalbaarheid in de cloud
* Regels voor het filteren van de FQDN van toepassingen
* Regels voor het filteren van netwerkverkeer

Meer informatie:

* [Overzicht van Azure Firewall](/azure/firewall/overview)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Beveiligde toegang op afstand en cross-premises connectiviteit

Het instellen, configureren en beheren van uw Azure-resources moet op afstand worden uitgevoerd. Bovendien u hybride [IT-oplossingen](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) implementeren die onderdelen on-premises en in de Azure public cloud hebben. Deze scenario's vereisen veilige toegang op afstand.

Azure-netwerken ondersteunen de volgende veilige scenario's voor externe toegang:

* Afzonderlijke werkstations verbinden met een virtueel netwerk
* Verbind uw on-premises netwerk met een virtueel netwerk met een VPN
* Verbind uw on-premises netwerk met een virtueel netwerk met een speciale WAN-koppeling
* Virtuele netwerken met elkaar verbinden

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Afzonderlijke werkstations verbinden met een virtueel netwerk

U mogelijk individuele ontwikkelaars of bedrijfspersoneel in staat stellen virtuele machines en services in Azure te beheren. Stel dat u toegang nodig hebt tot een virtuele machine in een virtueel netwerk. Maar uw beveiligingsbeleid staat geen RDP- of SSH-externe toegang toe tot afzonderlijke virtuele machines. In dit geval u een [point-to-site VPN-verbinding](../../vpn-gateway/point-to-site-about.md) gebruiken.

Met de point-to-site VPN-verbinding u een privé- en beveiligde verbinding tussen de gebruiker en het virtuele netwerk instellen. Wanneer de VPN-verbinding tot stand is gebracht, kan de gebruiker RDP of SSH via de VPN-koppeling naar een virtuele machine op het virtuele netwerk. (Dit veronderstelt dat de gebruiker kan verifiëren en is geautoriseerd.) Point-to-site VPN-ondersteuning:

* Secure Socket Tunneling Protocol (SSTP), een eigen SSL-gebaseerd VPN-protocol. Een SSL VPN-oplossing kan firewalls binnendringen, omdat de meeste firewalls TCP-poort 443 openen, die TLS/SSL gebruikt. SSTP wordt alleen ondersteund op Windows-apparaten. Azure ondersteunt alle versies van Windows met SSTP (Windows 7 en hoger).

* IKEv2 VPN, een op standaarden gebaseerde IPsec VPN-oplossing. IKEv2 VPN kan worden gebruikt om verbinding te maken vanaf Mac-apparaten (OSX-versie 10.11 en hoger).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Meer informatie:

* [Een point-to-site-verbinding configureren met PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Verbind uw on-premises netwerk met een virtueel netwerk met een VPN

Misschien wilt u uw hele bedrijfsnetwerk of delen ervan verbinden met een virtueel netwerk. Dit is gebruikelijk in hybride IT-scenario's, waarbij organisaties [hun on-premises datacenter uitbreiden naar Azure.](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84) In veel gevallen hosten organisaties delen van een service in Azure en onderdelen on-premises. Ze kunnen dit bijvoorbeeld doen wanneer een oplossing front-end webservers in Azure en on-premises back-enddatabases bevat. Deze typen 'cross-premises' verbindingen maken het beheer van azure-resources ook veiliger en maken scenario's mogelijk, zoals het uitbreiden van Active Directory-domeincontrollers naar Azure.

Een manier om dit te bereiken is het gebruik van een [site-to-site VPN.](https://www.techopedia.com/definition/30747/site-to-site-vpn) Het verschil tussen een site-to-site VPN en een point-to-site VPN is dat deze een enkel apparaat verbindt met een virtueel netwerk. Een site-to-site VPN verbindt een heel netwerk (zoals uw on-premises netwerk) met een virtueel netwerk. Site-to-site VPN's voor een virtueel netwerk maken gebruik van het uiterst veilige VPN-protocol in de IPsec-tunnelmodus.

Meer informatie:

* [Een VNet resourcebeheer maken met een vpn-verbinding van site-naar-site met behulp van de Azure-portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Over VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Verbind uw on-premises netwerk met een virtueel netwerk met een speciale WAN-koppeling

Point-to-site en site-to-site VPN-verbindingen zijn effectief voor het inschakelen van cross-premises connectiviteit. Sommige organisaties beschouwen ze echter als de volgende nadelen:

* VPN-verbindingen verplaatsen gegevens over het internet. Dit stelt deze verbindingen bloot aan potentiële beveiligingsproblemen die betrokken zijn bij het verplaatsen van gegevens via een openbaar netwerk. Bovendien kunnen betrouwbaarheid en beschikbaarheid voor internetverbindingen niet worden gegarandeerd.
* VPN-verbindingen met virtuele netwerken hebben mogelijk niet de bandbreedte voor sommige toepassingen en doeleinden, omdat ze maximaal ongeveer 200 Mbps bedragen.

Organisaties die het hoogste beveiligings- en beschikbaarheidsniveau nodig hebben voor hun cross-premises verbindingen, gebruiken doorgaans speciale WAN-koppelingen om verbinding te maken met externe sites. Azure biedt u de mogelijkheid om een speciale WAN-koppeling te gebruiken die u gebruiken om uw on-premises netwerk aan een virtueel netwerk te verbinden. Azure ExpressRoute, Express route direct en Express route global reach maken dit mogelijk.

Meer informatie:

* [Technisch overzicht van ExpressRoute](../../expressroute/expressroute-introduction.md)
* [ExpressRoute direct](../../expressroute/expressroute-erdirect-about.md)
* [Express route wereldwijd bereik](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Virtuele netwerken met elkaar verbinden

Het is mogelijk om veel virtuele netwerken te gebruiken voor uw implementaties. Er zijn verschillende redenen waarom u dit zou kunnen doen. U het beheer vereenvoudigen of wilt u meer beveiliging. Ongeacht de motivatie om resources op verschillende virtuele netwerken te plaatsen, kunnen er momenten zijn waarop u wilt dat resources op elk van de netwerken met elkaar in contact komen.

Een optie is voor diensten op een virtueel netwerk om verbinding te maken met diensten op een ander virtueel netwerk, door "looping terug" via het internet. De verbinding begint op een virtueel netwerk, gaat via het internet, en komt dan terug naar de bestemming virtuele netwerk. Deze optie legt de verbinding bloot aan de beveiligingsproblemen die inherent zijn aan een internetcommunicatie.

Een betere optie zou kunnen zijn om een site-to-site VPN te maken die verbinding maakt tussen twee virtuele netwerken. Deze methode maakt gebruik van dezelfde [IPSec tunnel mode](https://technet.microsoft.com/library/cc786385.aspx) protocol als de cross-premises site-to-site VPN-verbinding hierboven vermeld.

Het voordeel van deze aanpak is dat de VPN-verbinding wordt gemaakt via de Azure-netwerkstructuur, in plaats van verbinding te maken via het internet. Dit biedt u een extra beveiligingslaag in vergelijking met site-to-site VPN's die verbinding maken via internet.

Meer informatie:

* [Een VNet-naar-VNet-verbinding configureren met Azure Resource Manager en PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Een andere manier om uw virtuele netwerken te verbinden is [VNET peering.](../../virtual-network/virtual-network-peering-overview.md) Met deze functie u twee Azure-netwerken verbinden, zodat de communicatie tussen deze netwerken plaatsvindt via de Microsoft-backbone-infrastructuur zonder dat deze ooit via internet wordt gebruikt. VNET-peering kan twee VNET's binnen dezelfde regio of twee VNET's in Azure-regio's verbinden. NSG's kunnen worden gebruikt om de connectiviteit tussen verschillende subnetten of systemen te beperken.

## <a name="availability"></a>Beschikbaarheid

Beschikbaarheid is een belangrijk onderdeel van elk beveiligingsprogramma. Als uw gebruikers en systemen geen toegang hebben tot wat ze nodig hebben om toegang te krijgen via het netwerk, kan de service als gecompromitteerd worden beschouwd. Azure heeft netwerktechnologieën die de volgende mechanismen met hoge beschikbaarheid ondersteunen:

* OP HTTP gebaseerde taakverdeling
* Load balancing op netwerkniveau
* Wereldwijde taakverdeling

Load balancing is een mechanisme dat is ontworpen om verbindingen gelijkmatig over meerdere apparaten te verdelen. De doelstellingen van load balancing zijn:

* Om de beschikbaarheid te vergroten. Wanneer u balansverbindingen laadt op meerdere apparaten, kunnen een of meer apparaten niet meer beschikbaar zijn zonder dat de service in gevaar komt. De services die op de resterende online apparaten worden uitgevoerd, kunnen de inhoud van de service blijven aanbieden.
* Om de prestaties te verhogen. Wanneer u balansverbindingen laadt op meerdere apparaten, hoeft één apparaat niet alle verwerkingen te verwerken. In plaats daarvan wordt de verwerkings- en geheugenvereisten voor het serveren van de inhoud verspreid over meerdere apparaten.

### <a name="http-based-load-balancing"></a>OP HTTP gebaseerde taakverdeling

Organisaties die webservices uitvoeren, willen vaak een OP HTTP gebaseerde load balancer voor die webservices hebben. Dit helpt zorgen voor voldoende prestatieniveaus en hoge beschikbaarheid. Traditionele, netwerkgebaseerde load balancers vertrouwen op netwerk- en transportlaagprotocollen. HTTP-gebaseerde load balancers, aan de andere kant, beslissingen nemen op basis van kenmerken van het HTTP-protocol.

Azure Application Gateway biedt OP HTTP gebaseerde taakverdeling voor uw webservices. Application Gateway ondersteunt:

* Cookie-gebaseerde sessie affiniteit. Deze mogelijkheid zorgt ervoor dat verbindingen met een van de servers achter die load balancer intact blijven tussen de client en de server. Dit zorgt voor stabiliteit van transacties.
* TLS offload. Wanneer een client verbinding maakt met de load balancer, wordt die sessie versleuteld met behulp van het HTTPS-protocol (TLS). Om de prestaties te verhogen, u echter het HTTP-protocol (onversleuteld) gebruiken om verbinding te maken tussen de load balancer en de webserver achter de load balancer. Dit wordt aangeduid als "TLS offload," omdat de webservers achter de load balancer niet de processor overhead die betrokken zijn bij encryptie ervaring. De webservers kunnen daarom sneller serviceaanvragen indienen.
* INHOUDsroutering op basis van URL's. Deze functie maakt het mogelijk voor de load balancer om beslissingen te nemen over waar verbindingen door te sturen op basis van de doel-URL. Dit biedt veel meer flexibiliteit dan oplossingen die beslissingen nemen over het balanceren op basis van IP-adressen.

Meer informatie:

* [Overzicht van Application Gateway](/azure/application-gateway/application-gateway-introduction)

### <a name="network-level-load-balancing"></a>Load balancing op netwerkniveau

In tegenstelling tot http-gebaseerde load balancing, maakt de load balancing op netwerkniveau beslissingen op basis van IP-adres- en poortnummers (TCP of UDP).
U de voordelen van het balanceren van de taakverdeling op netwerkniveau in Azure behalen met Azure Load Balancer. Enkele belangrijke kenmerken van Load Balancer zijn:

* Load balancing op netwerkniveau op basis van IP-adres en poortnummers.
* Ondersteuning voor elk protocol voor toepassingslagen.
* Laadsaldi naar azure virtuele machines en cloud services rol exemplaren.
* Kan worden gebruikt voor zowel internet-facing (externe load balancing) en niet-internet facing (internal load balancing) applicaties en virtuele machines.
* Endpoint monitoring, die wordt gebruikt om te bepalen of een van de services achter de load balancer niet meer beschikbaar is.

Meer informatie:

* [Internetgerichte load balancer tussen meerdere virtuele machines of services](/azure/load-balancer/load-balancer-internet-overview)
* [Overzicht van interne load balancer](/azure/load-balancer/load-balancer-internal-overview)

### <a name="global-load-balancing"></a>Wereldwijde taakverdeling

Sommige organisaties willen het hoogst mogelijke niveau van beschikbaarheid. Een manier om dit doel te bereiken is het hosten van applicaties in wereldwijd gedistribueerde datacenters. Wanneer een toepassing wordt gehost in datacenters over de hele wereld, is het mogelijk dat een hele geopolitieke regio niet beschikbaar wordt en de toepassing nog steeds operationeel is.

Deze load-balancing strategie kan ook prestatievoordelen opleveren. U aanvragen voor de service doorsturen naar het datacenter dat het dichtst bij het apparaat ligt dat de aanvraag indient.

In Azure u de voordelen van wereldwijde taakverdeling behalen met Azure Traffic Manager.

Meer informatie:

* [Wat is Traffic Manager?](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Naamomzetting

Naamoplossing is een kritieke functie voor alle services die u host in Azure. Vanuit beveiligingsperspectief kan het compromitteren van de functie voor naamomzetting ertoe leiden dat een aanvaller verzoeken van uw sites omleidt naar de site van een aanvaller. Veilige naamomzetting is een vereiste voor al uw cloudgehoste services.

Er zijn twee soorten naamomzetting die u moet aanpakken:

* Interne naamresolutie. Dit wordt gebruikt door services op uw virtuele netwerken, uw on-premises netwerken of beide. Namen die worden gebruikt voor interne naamomzetting zijn niet toegankelijk via internet. Voor optimale beveiliging is het belangrijk dat uw interne naamomzettingsschema niet toegankelijk is voor externe gebruikers.
* Externe naamresolutie. Dit wordt gebruikt door mensen en apparaten buiten uw on-premises netwerken en virtuele netwerken. Dit zijn de namen die zichtbaar zijn voor het internet en worden gebruikt om directe verbinding te maken met uw cloudservices.

Voor interne naamresolutie hebt u twee opties:

* Een virtuele DNS-server voor het netwerk. Wanneer u een nieuw virtueel netwerk maakt, wordt er een DNS-server voor u gemaakt. Deze DNS-server kan de namen van de machines in dat virtuele netwerk oplossen. Deze DNS-server is niet configureerbaar, wordt beheerd door de Azure-fabricmanager en kan u daarom helpen uw oplossing voor naamomzetting te beveiligen.
* Neem je eigen DNS-server mee. U hebt de mogelijkheid om een DNS-server naar keuze op uw virtuele netwerk te plaatsen. Deze DNS-server kan een geïntegreerde DNS-server in Active Directory zijn of een speciale DNS-serveroplossing die wordt geleverd door een Azure-partner, die u verkrijgen van de Azure Marketplace.

Meer informatie:

* [Overzicht van virtueel netwerk](../../virtual-network/virtual-networks-overview.md)
* [DNS-servers beheren die door een virtueel netwerk worden gebruikt](../../virtual-network/manage-virtual-network.md#change-dns-servers)

Voor externe naamresolutie hebt u twee opties:

* Host uw eigen externe DNS-server on-premises.
* Host uw eigen externe DNS-server bij een serviceprovider.

Veel grote organisaties hosten hun eigen DNS-servers on-premises. Ze kunnen dit doen omdat ze de netwerkexpertise en wereldwijde aanwezigheid hebben om dit te doen.

In de meeste gevallen is het beter om uw DNS-naamomzettingsservices te hosten bij een serviceprovider. Deze dienstverleners hebben de netwerkexpertise en wereldwijde aanwezigheid om een zeer hoge beschikbaarheid te garanderen voor uw naamresolutieservices. Beschikbaarheid is essentieel voor DNS-services, want als uw naamomzettingsservices mislukken, kan niemand uw internetservices bereiken.

Azure biedt u een zeer beschikbare en goed presterende externe DNS-oplossing in de vorm van Azure DNS. Deze oplossing voor externe naamomzetting maakt gebruik van de wereldwijde Azure DNS-infrastructuur. Hiermee u uw domein hosten in Azure, met dezelfde referenties, API's, hulpprogramma's en facturering als uw andere Azure-services. Als onderdeel van Azure erft het ook de sterke beveiligingsbesturingselementen die in het platform zijn ingebouwd.

Meer informatie:

* [Overzicht van Azure DNS](../../dns/dns-overview.md)
* [Met Azure DNS-privézones](../../dns/private-dns-overview.md) u privé-DNS-namen configureren voor Azure-resources in plaats van de automatisch toegewezen namen zonder dat u een aangepaste DNS-oplossing hoeft toe te voegen.

## <a name="perimeter-network-architecture"></a>Perimeternetwerkarchitectuur

Veel grote organisaties gebruiken perimeternetwerken om hun netwerken te segmenteren en een bufferzone te creëren tussen het internet en hun services. Het perimetergedeelte van het netwerk wordt beschouwd als een zone met lage beveiliging en er worden geen waardevolle assets in dat netwerksegment geplaatst. U ziet meestal netwerkbeveiligingsapparaten met een netwerkinterface in het perimeternetwerksegment. Een andere netwerkinterface is verbonden met een netwerk met virtuele machines en services die binnenkomende verbindingen van het internet accepteren.

U perimeternetwerken op verschillende manieren ontwerpen. De beslissing om een perimeternetwerk te implementeren en vervolgens welk type perimeternetwerk u wilt gebruiken als u besluit er een te gebruiken, is afhankelijk van uw netwerkbeveiligingsvereisten.

Meer informatie:

* [Microsoft Cloud Services en netwerkbeveiliging](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

DDoS-aanvallen (Distributed Denial of Service-aanvallen) vormen een van de grootste beschikbaarheids- en beveiligingsproblemen voor klanten die hun toepassingen verplaatsen naar de cloud. Een DDoS-aanval probeert de bronnen van een toepassing uit te putten, waardoor de toepassing niet beschikbaar is voor legitieme gebruikers. DDoS-aanvallen kunnen worden gericht op elk eindpunt dat openbaar bereikbaar is via internet.
Microsoft biedt DDoS-bescherming die bekend staat als **Basic** als onderdeel van het Azure-platform. Dit is gratis en omvat altijd op monitoring en real-time mitigatie van gemeenschappelijke netwerkniveau aanvallen. Naast de beveiligingen die bij DDoS-bescherming **Basic** zijn meegeleverd, u de **standaardoptie** inschakelen. DDoS Protection Standard functies omvatten:

* **Native platform integratie:** Native geïntegreerd in Azure. Inclusief configuratie via de Azure-portal. DDoS Protection Standard begrijpt uw resources en resourceconfiguratie.
* **Beveiliging van de turnkey:** Vereenvoudigde configuratie beschermt onmiddellijk alle bronnen op een virtueel netwerk zodra DDoS Protection Standard is ingeschakeld. Er is geen interventie of gebruikersdefinitie vereist. DDoS Protection Standard vermindert de aanval direct en automatisch, zodra deze wordt gedetecteerd.
* **Always-on verkeersbewaking:** Uw applicatie verkeerspatronen worden 24 uur per dag, 7 dagen per week gemonitord, op zoek naar indicatoren van DDoS-aanvallen. Mitigatie wordt uitgevoerd wanneer het beveiligingsbeleid wordt overschreden.
* **Rapporten over aanvalsbeperking** Attack Mitigation Reports gebruiken geaggregeerde netwerkstroomgegevens om gedetailleerde informatie te geven over aanvallen die op uw resources zijn gericht.
* **Aanvalsstroomlogboeken** Met Attack Mitigation Flow Logs u het gedropte verkeer, doorgestuurd verkeer en andere aanvalsgegevens in bijna realtime bekijken tijdens een actieve DDoS-aanval.
* **Adaptieve tuning:** Intelligente verkeersprofilering leert het verkeer van uw toepassing in de loop van de tijd en selecteert en werkt het profiel bij dat het meest geschikt is voor uw service. Het profiel wordt aangepast naarmate het verkeer in de loop van de tijd verandert. Laag 3 tot laag 7 bescherming: biedt full stack DDoS-bescherming, wanneer gebruikt met een firewall voor webtoepassingen.
* **Uitgebreide mitigatieschaal:** Meer dan 60 verschillende aanvalstypen kunnen worden beperkt, met wereldwijde capaciteit, om te beschermen tegen de grootste bekende DDoS-aanvallen.
* **Aanvalsstatistieken:** Samengevatte statistieken van elke aanval zijn toegankelijk via Azure Monitor.
* **Waarschuwing voor aanvallen:** Waarschuwingen kunnen worden geconfigureerd bij het begin en einde van een aanval, en over de duur van de aanval, met behulp van ingebouwde aanvalsstatistieken. Waarschuwingen worden geïntegreerd in uw operationele software zoals Microsoft Azure Monitor-logboeken, Splunk, Azure Storage, E-mail en de Azure-portal.
* **Kostengarantie:**  Data-overdracht en applicatie scale-out service credits voor gedocumenteerde DDoS-aanvallen.
* **DDoS Snel responsief** DDoS Protection Standard-klanten hebben nu toegang tot het Rapid Response-team tijdens een actieve aanval. DRR kan helpen met aanvalsonderzoek, aangepaste oplossingen tijdens een aanval en analyse na de aanval.


Meer informatie:

* [Overzicht van DDOS-beveiliging](../../virtual-network/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure Front Door

Azure Front Door Service stelt u in staat om de globale routering van uw webverkeer te definiëren, te beheren en te bewaken. Het optimaliseert de routering van uw verkeer voor de beste prestaties en hoge beschikbaarheid. Met Azure Front Door kunt u regels voor toegangsbeheer maken voor aangepaste webtoepassingsfirewalls (WAF) om uw HTTP/HTTPS-workload te beschermen tegen exploitatie op basis van klant-IP-adressen, landcode en http-parameters. Daarnaast u met Front Door ook tariefbeperkende regels maken om kwaadaardig botverkeer te bestrijden, het omvat TLS-offloading en per-HTTP/HTTPS-verzoek, verwerking van toepassingslagen.

Het Front Door-platform wordt zelf beschermd door Azure DDoS Protection Basic. Voor verdere bescherming kan Azure DDoS Protection Standard worden ingeschakeld op uw VNETs en bronnen beschermen tegen netwerklaagaanvallen (TCP/UDP) via automatische afstemming en risicobeperking. Front Door is een laag 7 omgekeerde proxy, het staat alleen webverkeer door te geven aan back-end servers en blokkeren andere soorten verkeer standaard.

Meer informatie:

* Voor meer informatie over de hele set Azure Front door-mogelijkheden u het [Azure Front Door-overzicht bekijken](../../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Azure-beheer

Azure Traffic Manager is een op DNS gebaseerde load balancer waarmee u verkeer optimaal over services kunt verdelen in Azure-regio's wereldwijd, terwijl u over hoge beschikbaarheid en een hoge reactiesnelheid beschikt. Traffic Manager maakt gebruik van DNS om aanvragen van clients door te sturen naar de meest geschikte eindpunten op basis van een methode om verkeer te routeren en van de status van de eindpunten. Een eindpunt is een internetgerichte service die binnen of buiten Azure wordt gehost. Verkeersbeheerder bewaakt de eindpunten en leidt geen verkeer naar eindpunten die niet beschikbaar zijn.

Meer informatie:

* [Overzicht azure traffic manager](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Monitoring en detectie van bedreigingen

Azure biedt mogelijkheden om u op dit belangrijke gebied te helpen met vroegtijdige detectie, bewaking en verzamelen en controleren van netwerkverkeer.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Azure Network Watcher kan u helpen problemen op te lossen en biedt een hele nieuwe set hulpprogramma's om te helpen bij het identificeren van beveiligingsproblemen.

[Security Group View](../../network-watcher/network-watcher-security-group-view-overview.md) helpt bij het controleren en beveiligingscompliance van virtuele machines. Gebruik deze functie om programmatische audits uit te voeren en het basisbeleid dat door uw organisatie is gedefinieerd te vergelijken met effectieve regels voor elk van uw VM's. Dit kan u helpen bij het identificeren van elke configuratiedrift.

[Pakketopname](../../network-watcher/network-watcher-packet-capture-overview.md) stelt u in staat om netwerkverkeer van en naar de virtuele machine vast te leggen. U netwerkstatistieken verzamelen en toepassingsproblemen oplossen, die van onschatbare waarde kunnen zijn bij het onderzoek naar netwerkinbraken. U deze functie ook samen met Azure-functies gebruiken om netwerkopnamen te starten als reactie op specifieke Azure-waarschuwingen.

Zie het overzicht van Azure [network watcher monitoring](../../network-watcher/network-watcher-monitoring-overview.md)voor meer informatie over Network Watcher en het testen van een deel van de functionaliteit in uw labs.

> [!NOTE]
> Ga naar de [pagina Azure-updates](https://azure.microsoft.com/updates/?product=network-watcher)voor de meest actuele meldingen over beschikbaarheid en status van deze service.

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center helpt u bedreigingen te voorkomen, te detecteren en erop te reageren en biedt u meer inzicht in en controle over de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders onopgemerkt zouden kunnen blijven en werkt met een grote reeks beveiligingsoplossingen.

Security Center helpt u bij het optimaliseren en bewaken van de netwerkbeveiliging door:

* Het verstrekken van netwerkbeveiligingsaanbevelingen.
* De status van uw netwerkbeveiligingsconfiguratie bewaken.
* U waarschuwen voor netwerkbedreigingen, zowel op eindpunt- als netwerkniveau.

Meer informatie:

* [Inleiding tot Azure Security Center](../../security-center/security-center-intro.md)

### <a name="virtual-network-tap"></a>Virtueel netwerk TAP

Azure virtual network TAP (Terminal Access Point) stelt u in staat om uw netwerkverkeer voor virtuele machines continu te streamen naar een netwerkpakketverzamelaar of analysetool. De collector- of analysetool wordt geleverd door een netwerkpartner voor virtuele apparaten. U dezelfde tap-bron voor virtueel netwerk gebruiken om verkeer van meerdere netwerkinterfaces in dezelfde of verschillende abonnementen te verzamelen.

Meer informatie:

* [Virtual Network TAP](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Logboekregistratie

Logboekregistratie op netwerkniveau is een belangrijke functie voor elk scenario voor netwerkbeveiliging. In Azure u informatie die is verkregen voor NSGs registreren om registratiegegevens op netwerkniveau te verkrijgen. Met NSG-logging krijgt u informatie van:

* [Activiteitslogboeken](../../azure-monitor/platform/platform-logs-overview.md). Gebruik deze logboeken om alle bewerkingen weer te geven die zijn ingediend bij uw Azure-abonnementen. Deze logboeken zijn standaard ingeschakeld en kunnen worden gebruikt binnen de Azure-portal. Ze stonden voorheen bekend als audit of operationele logs.
* Gebeurtenislogboeken. Deze logboeken geven informatie over welke NSG-regels werden toegepast.
* Teller logs. Deze logboeken laten u weten hoe vaak elke NSG-regel is toegepast om verkeer te weigeren of toe te staan.

U [microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), een krachtig hulpmiddel voor gegevensvisualisatie, ook gebruiken om deze logboeken te bekijken en te analyseren.
Meer informatie:

* [Azure Monitor-logboeken voor netwerkbeveiligingsgroepen (NSGs)](../../virtual-network/virtual-network-nsg-manage-log.md)
