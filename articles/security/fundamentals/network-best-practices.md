---
title: Aanbevolen procedures voor netwerkbeveiliging - Microsoft Azure
description: In dit artikel vindt u een reeks aanbevolen procedures voor netwerkbeveiliging met behulp van ingebouwde Azure-mogelijkheden.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 5e155758d19b45d977fcd087bff0ceb85898f8f8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548309"
---
# <a name="azure-best-practices-for-network-security"></a>Aanbevolen procedures voor Azure voor netwerkbeveiliging
In dit artikel wordt een verzameling aanbevolen procedures voor Azure besproken om uw netwerkbeveiliging te verbeteren. Deze best practices zijn afgeleid van onze ervaring met Azure-netwerken en de ervaringen van klanten zoals uzelf.

Voor elke aanbevolen praktijk wordt in dit artikel uitgelegd:

* Wat de beste praktijk is
* Waarom u die best practice wilt inschakelen
* Wat kan het resultaat zijn als u de beste praktijken niet inschakelt
* Mogelijke alternatieven voor de beste praktijken
* Hoe u leren om de beste praktijken in te schakelen

Deze aanbevolen procedures zijn gebaseerd op een consensusadvies en Azure-platformmogelijkheden en functiesets, zoals ze bestaan op het moment dat dit artikel werd geschreven. Meningen en technologieën veranderen in de loop van de tijd en dit artikel zal regelmatig worden bijgewerkt om deze veranderingen weer te geven.

## <a name="use-strong-network-controls"></a>Sterke netwerkbesturingselementen gebruiken
U [virtuele Azure-machines (VM's)](https://azure.microsoft.com/services/virtual-machines/) en apparaten verbinden met andere netwerkapparaten door ze op [virtuele Azure-netwerken](../../virtual-network/index.yml)te plaatsen. Dat wil zeggen dat u virtuele netwerkinterfacekaarten verbinden met een virtueel netwerk om TCP/IP-gebaseerde communicatie tussen netwerkapparaten mogelijk te maken. Virtuele machines die zijn verbonden met een virtueel Azure-netwerk, kunnen verbinding maken met apparaten op hetzelfde virtuele netwerk, verschillende virtuele netwerken, het internet of uw eigen on-premises netwerken.

Als u uw netwerk en de beveiliging van uw netwerk plant, raden we u aan:

- Beheer van kernnetwerkfuncties zoals ExpressRoute, virtuele netwerk- en subnetprovisioning en IP-adressering.
- Governance van netwerkbeveiligingselementen, zoals netwerkfuncties voor virtuele apparaten zoals ExpressRoute, virtuele netwerk- en subnetprovisioning en IP-adressering.

Als u een gemeenschappelijke set beheertools gebruikt om uw netwerk en de beveiliging van uw netwerk te bewaken, krijgt u duidelijk inzicht in beide. Een eenvoudige, uniforme beveiligingsstrategie vermindert fouten omdat het menselijk begrip en de betrouwbaarheid van automatisering verhoogt.

## <a name="logically-segment-subnets"></a>Logisch segment subnetten
Azure virtuele netwerken zijn vergelijkbaar met LAN's op uw on-premises netwerk. Het idee achter een virtueel Azure-netwerk is dat u een netwerk maakt op basis van één privé-IP-adresruimte, waarop u al uw virtuele Azure-machines plaatsen. De beschikbare private IP-adresruimten bevinden zich in de reeksen klasse A (10.0.0.0/8), klasse B (172.16.0.0/12) en klasse C (192.168.0.0/16).

Aanbevolen procedures voor het logisch segmenteren van subnetten zijn:

**Aanbevolen procedures**: Wijs geen regels voor toegestane regels toe met brede marges (bijvoorbeeld 0,0,0 toe staan tot en met 255.255.255.255).  
**Detail:** Zorg ervoor dat probleemoplossingsprocedures het instellen van dit soort regels ontmoedigen of verbieden. Deze toestaan regels leiden tot een vals gevoel van veiligheid en worden vaak gevonden en uitgebuit door rode teams.

**Aanbevolen procedures**: Segmenteer de grotere adresruimte in subnetten.   
**Detail:** Gebruik [CIDR-gebaseerde](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)subnetting principes om uw subnetten te maken.

**Aanbevolen procedures**: Netwerktoegangsbesturingselementen maken tussen subnetten. Routering tussen subnetten gebeurt automatisch en u hoeft geen routeringstabellen handmatig te configureren. Standaard zijn er geen netwerktoegangsbesturingselementen tussen de subnetten die u op een virtueel Azure-netwerk maakt.   
**Detail:** Gebruik een [netwerkbeveiligingsgroep](/azure/virtual-network/virtual-networks-nsg) om te beschermen tegen ongevraagd verkeer naar Azure-subnetten. Netwerkbeveiligingsgroepen zijn eenvoudige, stateful packet inspection devices die gebruik maken van de 5-tuple-benadering (bron-IP, bronpoort, doel-IP, doelpoort en layer 4-protocol) om regels voor toestaan/weigeren voor netwerkverkeer te maken. U staat verkeer van en naar één IP-adres, van en naar meerdere IP-adressen of van en naar hele subnetten toe of weigert deze te weigeren.

Wanneer u netwerkbeveiligingsgroepen gebruikt voor netwerktoegangscontrole tussen subnetten, u resources die tot dezelfde beveiligingszone of rol behoren, in hun eigen subnetten plaatsen.

**Aanbevolen procedures**: Vermijd kleine virtuele netwerken en subnetten om eenvoud en flexibiliteit te garanderen.   
**Detail:** De meeste organisaties voegen meer resources toe dan aanvankelijk gepland, en het opnieuw toewijzen van adressen is arbeidsintensief. Het gebruik van kleine subnetten voegt een beperkte beveiligingswaarde toe en het toewijzen van een netwerkbeveiligingsgroep aan elk subnet voegt overhead toe. Definieer subnetten in grote lijnen om ervoor te zorgen dat u flexibiliteit hebt voor groei.

**Aanbevolen procedures**: Vereenvoudig het regelbeheer van de netwerkbeveiligingsgroep door [toepassingsbeveiligingsgroepen te](https://azure.microsoft.com/blog/applicationsecuritygroups/)definiëren .  
**Detail:** Definieer een toepassingsbeveiligingsgroep voor lijsten met IP-adressen waarvan u denkt dat deze in de toekomst kan veranderen of die in veel netwerkbeveiligingsgroepen kunnen worden gebruikt. Zorg ervoor dat u application security groups duidelijk noemt, zodat anderen hun inhoud en doel kunnen begrijpen.

## <a name="adopt-a-zero-trust-approach"></a>Een Zero Trust-benadering hanteren
Perimeter-gebaseerde netwerken werken in de veronderstelling dat alle systemen binnen een netwerk kunnen worden vertrouwd. Maar de huidige medewerkers hebben overal toegang tot de bronnen van hun organisatie op verschillende apparaten en apps, waardoor perimeterbeveiligingscontroles irrelevant zijn. Toegangsbeheerbeleid dat alleen gericht is op wie toegang heeft tot een resource, is niet voldoende. Om de balans tussen beveiliging en productiviteit onder de knie te krijgen, moeten beveiligingsbeheerders ook rekening houden met *de manier waarop* een resource wordt benaderd.

Netwerken moeten evolueren van traditionele verdedigingen omdat netwerken kwetsbaar kunnen zijn voor inbreuken: een aanvaller kan één eindpunt binnen de vertrouwde grens compromitteren en vervolgens snel een voet aan de grond krijgen in het hele netwerk. [Zero Trust-netwerken](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) elimineren het concept van vertrouwen op basis van netwerklocatie binnen een perimeter. In plaats daarvan gebruiken Zero Trust-architecturen apparaat- en gebruikersvertrouwensclaims om toegang te krijgen tot organisatiegegevens en -bronnen. Voor nieuwe initiatieven kiest u zero trust-benaderingen die vertrouwen valideren op het moment van toegang.

Aanbevolen procedures zijn:

**Aanbevolen procedures**: Geef voorwaardelijke toegang tot bronnen op basis van apparaat, identiteit, zekerheid, netwerklocatie en meer.  
**Detail:** Met [Azure AD Conditional Access](/azure/active-directory/conditional-access/overview) u de juiste toegangsbesturingselementen toepassen door geautomatiseerde toegangscontrolebeslissingen te implementeren op basis van de vereiste voorwaarden. Zie [Toegang tot Azure-beheer beheren met voorwaardelijke toegang](../../role-based-access-control/conditional-access-azure-management.md)voor meer informatie.

**Aanbevolen procedures**: Poorttoegang alleen inschakelen na goedkeuring van de werkstroom.  
**Detail:** U [just-in-time VM-toegang in Azure Security Center](../../security-center/security-center-just-in-time.md) gebruiken om binnenkomend verkeer naar uw Azure VM's te vergrendelen, waardoor de blootstelling aan aanvallen wordt verminderd en u gemakkelijk toegang hebt tot VM's wanneer dat nodig is.

**Aanbevolen procedures**: Geef tijdelijke machtigingen toe om bevoorrechte taken uit te voeren, waardoor kwaadwillende of onbevoegde gebruikers geen toegang meer krijgen nadat de machtigingen zijn verlopen. Toegang wordt alleen verleend wanneer gebruikers deze nodig hebben.  
**Detail:** Gebruik just-in-time toegang in Azure AD Privileged Identity Management of in een oplossing van derden om machtigingen te verlenen voor het uitvoeren van bevoorrechte taken.

Zero Trust is de volgende evolutie in netwerkbeveiliging. De staat van cyberaanvallen drijft organisaties om de "veronderstel inbreuk" mentaliteit te nemen, maar deze aanpak mag niet worden beperkt. Zero Trust-netwerken beschermen bedrijfsgegevens en -bronnen en zorgen ervoor dat organisaties een moderne werkplek kunnen bouwen door technologieën te gebruiken die werknemers in staat stellen altijd en overal en op welke manier dan ook productief te zijn.

## <a name="control-routing-behavior"></a>Routeringsgedrag beheren
Wanneer u een virtuele machine op een virtueel Azure-netwerk plaatst, kan de VM verbinding maken met elke andere virtuele virtuele machine op hetzelfde virtuele netwerk, zelfs als de andere VM's zich op verschillende subnetten bevinden. Dit is mogelijk omdat een verzameling systeemroutes die standaard is ingeschakeld, dit type communicatie mogelijk maakt. Met deze standaardroutes kunnen VM's op hetzelfde virtuele netwerk verbindingen met elkaar en met het internet (alleen voor uitgaande communicatie naar het internet) worden gestart.

Hoewel de standaardsysteemroutes handig zijn voor veel implementatiescenario's, zijn er momenten waarop u de routeringsconfiguratie voor uw implementaties wilt aanpassen. U het next-hop-adres configureren om specifieke bestemmingen te bereiken.

We raden u aan [door de gebruiker gedefinieerde routes te](../../virtual-network/virtual-networks-udr-overview.md) configureren wanneer u een beveiligingstoestel implementeert voor een virtueel netwerk. We praten hierover in een later gedeelte met de titel [uw kritieke Azure-servicebronnen beveiligen voor alleen uw virtuele netwerken.](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks)

> [!NOTE]
> Door de gebruiker gedefinieerde routes zijn niet vereist en de standaardsysteemroutes werken meestal.
>
>

## <a name="use-virtual-network-appliances"></a>Virtuele netwerkapparaten gebruiken
Netwerkbeveiligingsgroepen en door de gebruiker gedefinieerde routering kunnen een bepaalde mate van netwerkbeveiliging bieden op de netwerk- en transportlagen van het [OSI-model.](https://en.wikipedia.org/wiki/OSI_model) Maar in sommige situaties wilt of moet u beveiliging op hoge niveaus van de stack inschakelen. In dergelijke situaties raden we u aan virtuele netwerkbeveiligingsapparaten te implementeren die worden geleverd door Azure-partners.

Azure-netwerkbeveiligingsapparaten kunnen betere beveiliging bieden dan wat besturingselementen op netwerkniveau bieden. Netwerkbeveiligingsmogelijkheden van virtuele netwerkbeveiligingsapparaten zijn:


* Firewalling
* Inbraakdetectie/inbraakpreventie
* Kwetsbaarheidsbeheer
* Toepassingsbesturingselement
* Op netwerken gebaseerde anomaliedetectie
* Webfiltering
* Antivirus
* Botnetbescherming

Als u beschikbare Azure-beveiligingsapparaten voor virtuele netwerken wilt vinden, gaat u naar Azure [Marketplace](https://azure.microsoft.com/marketplace/) en zoekt u naar 'beveiliging' en 'netwerkbeveiliging'.

## <a name="deploy-perimeter-networks-for-security-zones"></a>Perimeternetwerken implementeren voor beveiligingszones
Een [perimeternetwerk](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) (ook wel DMZ genoemd) is een fysiek of logisch netwerksegment dat een extra beveiligingslaag biedt tussen uw assets en het internet. Gespecialiseerde netwerktoegangscontroleapparaten aan de rand van een perimeternetwerk staan alleen gewenst verkeer toe in uw virtuele netwerk.

Perimeternetwerken zijn handig omdat u uw beheer van netwerktoegangscontrolebeheer, -bewaking, logboekregistratie en rapportage richten op de apparaten aan de rand van uw virtuele Azure-netwerk. Een perimeternetwerk is de plaats waar u meestal DDoS-preventie (Distributed Denial of Service), inbraakdetectie/inbraakpreventiesystemen (IDS/IPS), firewallregels en -beleid, webfiltering, netwerkantimalware en meer inschakelt. De netwerkbeveiligingsapparaten zitten tussen het internet en uw virtuele Azure-netwerk en hebben een interface op beide netwerken.

Hoewel dit het basisontwerp van een perimeternetwerk is, zijn er vele verschillende ontwerpen, zoals achter-aan-rug, tri-homed, en multi-homed.

Op basis van het eerder genoemde Zero Trust-concept raden we u aan om een perimeternetwerk te gebruiken voor alle hoge beveiligingsimplementaties om het niveau van netwerkbeveiliging en toegangscontrole voor uw Azure-bronnen te verbeteren. U Azure of een oplossing van derden gebruiken om een extra beveiligingslaag tussen uw assets en het internet te bieden:

- Azure native besturingselementen. [Azure Firewall](/azure/firewall/overview) en de [firewall voor webtoepassingen in Application Gateway](../../application-gateway/features.md#web-application-firewall) bieden basisbeveiliging met een volledig stateful firewall as a service, ingebouwde hoge beschikbaarheid, onbeperkte schaalbaarheid van de cloud, FQDN-filtering, ondersteuning voor OWASP-kernregelsets en eenvoudige installatie en configuratie.
- Aanbiedingen van derden. Zoek in Azure [Marketplace](https://azuremarketplace.microsoft.com/) naar de volgende generatie firewall (NGFW) en andere aanbiedingen van derden die vertrouwde beveiligingstools en aanzienlijk verbeterde niveaus van netwerkbeveiliging bieden. Configuratie is misschien complexer, maar met een aanbod van derden u mogelijk bestaande mogelijkheden en vaardigheden gebruiken.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Vermijd blootstelling aan het internet met speciale WAN-koppelingen
Veel organisaties hebben gekozen voor de hybride IT-route. Met hybride IT bevinden sommige informatiemiddelen van het bedrijf zich in Azure en andere blijven on-premises. In veel gevallen worden sommige onderdelen van een service uitgevoerd in Azure, terwijl andere onderdelen on-premises blijven.

In een hybride IT-scenario is er meestal een soort cross-premises connectiviteit. Met cross-premises connectiviteit kan het bedrijf zijn on-premises netwerken verbinden met virtuele Azure-netwerken. Er zijn twee cross-premises connectiviteitsoplossingen beschikbaar:

* [Site-to-site VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Het is een vertrouwde, betrouwbare en gevestigde technologie, maar de verbinding vindt plaats via het internet. Bandbreedte is beperkt tot een maximum van ongeveer 1,25 Gbps. Site-to-site VPN is een wenselijke optie in sommige scenario's.
* **Azure ExpressRoute**. We raden u aan [ExpressRoute](../../expressroute/expressroute-introduction.md) te gebruiken voor uw cross-premises connectiviteit. Met ExpressRoute kunt u uw on-premises netwerken in de Microsoft Cloud uitbreiden via een persoonlijke verbinding die wordt gefaciliteerd door een connectiviteitsprovider. Met ExpressRoute u verbindingen tot stand brengen met Microsoft-cloudservices zoals Azure, Office 365 en Dynamics 365. ExpressRoute is een speciale WAN-koppeling tussen uw on-premises locatie of een Microsoft Exchange-hostingprovider. Omdat dit een telco-verbinding is, reizen uw gegevens niet via internet, dus worden ze niet blootgesteld aan de potentiële risico's van internetcommunicatie.

De locatie van uw ExpressRoute-verbinding kan van invloed zijn op de firewallcapaciteit, schaalbaarheid, betrouwbaarheid en zichtbaarheid van het netwerkverkeer. U moet bepalen waar ExpressRoute in bestaande (on-premises) netwerken moet worden beëindigd. U kunt:

- Buiten de firewall (het perimeternetwerkparadigma) beëindigen als u inzicht in het verkeer nodig hebt, als u een bestaande praktijk van het isoleren van datacenters moet voortzetten of als u alleen extranetbronnen op Azure plaatst.
- Eindigen binnen de firewall (het paradigma van de netwerkextensie). Dit is de standaardaanbeveling. In alle andere gevallen raden we aan Azure te behandelen als een nth-datacenter.

## <a name="optimize-uptime-and-performance"></a>Uptime en prestaties optimaliseren
Als een service is uitgeschakeld, kan informatie niet worden geopend. Als de prestaties zo slecht zijn dat de gegevens onbruikbaar zijn, u de gegevens als ontoegankelijk beschouwen. Vanuit een beveiligingsperspectief moet u doen wat u om ervoor te zorgen dat uw diensten optimale uptime en prestaties hebben.

Een populaire en effectieve methode voor het verbeteren van beschikbaarheid en prestaties is load balancing. Load balancing is een methode voor het distribueren van netwerkverkeer over servers die deel uitmaken van een service. Als u bijvoorbeeld front-end webservers als onderdeel van uw service hebt, u taakverdeling gebruiken om het verkeer over uw meerdere front-end webservers te distribueren.

Deze verdeling van het verkeer verhoogt de beschikbaarheid, want als een van de webservers niet meer beschikbaar is, stopt de load balancer met het verzenden van verkeer naar die server en leidt deze door naar de servers die nog online zijn. Load balancing helpt ook de prestaties, omdat de processor,netwerk en geheugen overhead voor het serveren van aanvragen is verdeeld over alle load-balanced servers.

We raden u aan om waar mogelijk en waar nodig voor uw services lastbalancing in te zetten. Hieronder volgen scenario's op zowel het virtuele netwerkniveau van Azure als op mondiaal niveau, samen met opties voor het balanceren van de lastenvoor elk.

**Scenario**: U hebt een toepassing die:

- Voor verzoeken van dezelfde gebruikers-/clientsessie moeten worden gevraagd om dezelfde back-end virtuele machine te bereiken. Voorbeelden hiervan zijn winkelwagen-apps en webmailservers.
- Accepteert alleen een beveiligde verbinding, dus onversleutelde communicatie naar de server is geen acceptabele optie.
- Hiervoor zijn meerdere HTTP-aanvragen vereist op dezelfde langlopende TCP-verbinding die moet worden doorgestuurd of in evenwicht moet worden gebracht naar verschillende back-endservers.

**Load-balancing option**: Gebruik [Azure Application Gateway](/azure/application-gateway/application-gateway-introduction), een HTTP-webverkeerslastenbalans. Application Gateway ondersteunt end-to-end TLS-versleuteling en [TLS-beëindiging](/azure/application-gateway/application-gateway-introduction) bij de gateway. Webservers kunnen vervolgens worden ontzorgd van encryptie en decryptie overhead en het verkeer stroomt onversleuteld naar de back-end servers.

**Scenario:** U moet binnenkomende verbindingen van het internet laden tussen uw servers in een virtueel Azure-netwerk. Scenario's zijn wanneer u:

- Hebben stateless toepassingen die inkomende verzoeken van het internet te accepteren.
- Vereist geen plaksessies of TLS-offload. Sticky sessies is een methode die wordt gebruikt met Application Load Balancing, om server-affiniteit te bereiken.

**Load-balancing option:** Gebruik de Azure-portal om [een externe load balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) te maken die binnenkomende aanvragen verspreidt over meerdere VM's om een hoger beschikbaarheidsniveau te bieden.

**Scenario:** U moet balansverbindingen laden van VM's die niet op internet staan. In de meeste gevallen worden de verbindingen die worden geaccepteerd voor taakverdeling geïnitieerd door apparaten in een virtueel Azure-netwerk, zoals SQL Server-exemplaren of interne webservers.   
**Load-balancing option:** Gebruik de Azure-portal om [een interne load balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) te maken die binnenkomende aanvragen verspreidt over meerdere VM's om een hoger beschikbaarheidsniveau te bieden.

**Scenario:** U hebt globale taakverdeling nodig omdat u:

- Heb een cloudoplossing die op grote schaal wordt verspreid over meerdere regio's en het hoogst mogelijke uptime (beschikbaarheid) vereist.
- Heb het hoogst mogelijke uptime nodig om ervoor te zorgen dat uw service beschikbaar is, zelfs als een volledig datacenter niet beschikbaar is.

**Load-balancing option**: Gebruik Azure Traffic Manager. Traffic Manager maakt het mogelijk om balansverbindingen met uw services te laden op basis van de locatie van de gebruiker.

Als de gebruiker bijvoorbeeld een verzoek aan uw service van de EU indient, wordt de verbinding gericht op uw services in een EU-datacenter. Dit onderdeel van Traffic Manager global load balancing helpt om de prestaties te verbeteren, omdat verbinding maken met het dichtstbijzijnde datacenter sneller is dan verbinding maken met datacenters die ver weg zijn.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>RDP/SSH-toegang tot virtuele machines uitschakelen
Het is mogelijk om virtuele Azure-machines te bereiken met behulp van [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) en het Secure [Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH)-protocol. Deze protocollen maken het beheer van VM's vanaf externe locaties mogelijk en worden standaard gebruikt in datacenters.

Het potentiële beveiligingsprobleem met het gebruik van deze protocollen via het internet is dat aanvallers [brute force-technieken](https://en.wikipedia.org/wiki/Brute-force_attack) kunnen gebruiken om toegang te krijgen tot virtuele Azure-machines. Nadat de aanvallers toegang hebben verkregen, kunnen ze uw VM gebruiken als een startpunt voor aanvallen op andere computers in uw virtuele netwerk of zelfs voor aanvallen op netwerkapparaten buiten Azure.

We raden u aan directe RDP- en SSH-toegang tot uw virtuele Azure-machines vanaf internet uit te schakelen. Nadat directe RDP- en SSH-toegang vanaf het internet is uitgeschakeld, hebt u andere opties die u gebruiken om toegang te krijgen tot deze VM's voor extern beheer.

**Scenario:** Schakel één gebruiker in om via internet verbinding te maken met een virtueel Azure-netwerk.   
**Optie:** [Point-to-site VPN](/azure/vpn-gateway/vpn-gateway-point-to-site-create) is een andere term voor een VPN-client/serververbinding met externe toegang. Nadat de point-to-site verbinding is gemaakt, kan de gebruiker RDP of SSH gebruiken om verbinding te maken met vm's die zich op het virtuele Azure-netwerk bevinden waarmee de gebruiker is verbonden via point-to-site VPN. Dit veronderstelt dat de gebruiker gemachtigd is om deze VM's te bereiken.

Point-to-site VPN is veiliger dan directe RDP- of SSH-verbindingen omdat de gebruiker twee keer moet verifiëren voordat hij verbinding maakt met een VM. Eerst moet de gebruiker zich verifiëren (en worden geautoriseerd) om de point-to-site VPN-verbinding tot stand te brengen. Ten tweede moet de gebruiker zich verifiëren (en geautoriseerd zijn) om de RDP- of SSH-sessie vast te stellen.

**Scenario:** Gebruikers in uw on-premises netwerk in staat stellen verbinding te maken met VM's in uw virtuele Azure-netwerk.   
**Optie:** Een [site-to-site VPN](/azure/vpn-gateway/vpn-gateway-site-to-site-create) verbindt een heel netwerk met een ander netwerk via het internet. U een site-to-site VPN gebruiken om uw on-premises netwerk te verbinden met een virtueel Azure-netwerk. Gebruikers op uw on-premises netwerk maken verbinding via het RDP- of SSH-protocol via de site-to-site VPN-verbinding. U hoeft geen directe RDP- of SSH-toegang via internet toe te staan.

**Scenario:** Gebruik een speciale WAN-koppeling om functionaliteit te bieden die vergelijkbaar is met de site-to-site VPN.   
**Optie**: [ExpressRoute gebruiken](https://azure.microsoft.com/documentation/services/expressroute/). Het biedt functionaliteit vergelijkbaar met de site-to-site VPN. Dit zijn de belangrijkste verschillen:

- De speciale WAN-link doorkruist het internet niet.
- Dedicated WAN-koppelingen zijn doorgaans stabieler en presteren beter.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Uw kritieke Azure-serviceresources beveiligen voor alleen uw virtuele netwerken
Gebruik eindpunten voor virtuele netwerkservices om uw privéadresruimte voor virtuele netwerken en de identiteit van uw virtuele netwerk uit te breiden naar de Azure-services via een directe verbinding. Met eindpunten kunt u uw kritieke Azure-serviceresources alleen beveiligen naar uw virtuele netwerken. Verkeer van uw virtuele netwerk naar de Azure-service blijft altijd op het Microsoft Azure-backbone-netwerk.

Service-eindpunten bieden de volgende voordelen:

- **Verbeterde beveiliging voor uw Azure-serviceresources**: met service-eindpunten kunnen Azure-serviceresources op veilige wijze worden gekoppeld aan uw virtuele netwerk. Het koppelen van serviceresources aan een virtueel netwerk biedt extra beveiliging doordat de toegang tot resources via openbaar internet volledig wordt verwijderd en alleen verkeer vanaf uw virtuele netwerk wordt toegestaan.
- **Optimale routering voor Azure-serviceverkeer vanuit uw virtuele netwerk:** alle routes in uw virtuele netwerk die internetverkeer naar uw on-premises en/of virtuele apparaten dwingen, ook geforceerd als gedwongen tunneling, dwingen Azure-serviceverkeer ook om dezelfde route te nemen als het internetverkeer. Service-eindpunten bieden een optimale routering voor Azure-verkeer.

  Eindpunten brengen serviceverkeer altijd rechtstreeks van uw virtuele netwerk naar de service op het Azure-backbonenetwerk. Door het verkeer op het Azure-backbone-netwerk te behouden, u uitgaand internetverkeer vanaf uw virtuele netwerken blijven controleren en bewaken, door middel van gedwongen tunneling, zonder dat dit gevolgen heeft voor het serviceverkeer. Meer informatie over [door de gebruiker gedefinieerde routes en gedwongen tunneling](../../virtual-network/virtual-networks-udr-overview.md).

- **Eenvoudig in te stellen met minder beheeroverhead:** u hebt geen gereserveerde, openbare IP-adressen in uw virtuele netwerken meer nodig om Azure-bronnen te beveiligen via een IP-firewall. Er zijn geen NAT- of gatewayapparaten vereist voor het instellen van de service-eindpunten. Service-eindpunten worden geconfigureerd met een simpele klik op een subnet. Er is geen extra overhead om de eindpunten te onderhouden.

Zie Eindpunten voor virtuele [netwerkservice](../../virtual-network/virtual-network-service-endpoints-overview.md)voor meer informatie over serviceeindpunten en de Azure-services en -regio's waarvoor serviceeindpunten beschikbaar zijn.

## <a name="next-steps"></a>Volgende stappen
Zie [aanbevolen procedures en patronen voor Azure-beveiliging](best-practices-and-patterns.md) voor meer aanbevolen procedures voor beveiliging die u gebruiken bij het ontwerpen, implementeren en beheren van uw cloudoplossingen met Azure.
