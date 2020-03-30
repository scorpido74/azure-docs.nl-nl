---
title: Veelgestelde vragen - Azure ExpressRoute | Microsoft Documenten
description: De veelgestelde vragen Over ExpressRoute bevat informatie over ondersteunde Azure-services, kosten, gegevens en verbindingen, SLA, providers en locaties, bandbreedte en aanvullende technische details.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: jaredro
ms.openlocfilehash: 845c53ec970777901ae8d1c0abf5032ac705d3e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264919"
---
# <a name="expressroute-faq"></a>Veelgestelde vragen ExpressRoute

## <a name="what-is-expressroute"></a>Wat is ExpressRoute?

ExpressRoute is een Azure-service waarmee u privéverbindingen maken tussen Microsoft-datacenters en infrastructuur die zich op uw locatie of in een colocatiefaciliteit bevinden. ExpressRoute-verbindingen gaan niet over het openbare internet en bieden hogere beveiliging, betrouwbaarheid en snelheden met lagere latencies dan typische verbindingen via internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Wat zijn de voordelen van het gebruik van ExpressRoute en particuliere netwerkverbindingen?

ExpressRoute-verbindingen gaan niet via het openbare internet. Ze bieden een hogere beveiliging, betrouwbaarheid en snelheden, met lagere en consistente latencies dan typische verbindingen via het internet. In sommige gevallen kan het gebruik van ExpressRoute-verbindingen voor het overbrengen van gegevens tussen on-premises apparaten en Azure aanzienlijke kostenvoordelen opleveren.

### <a name="where-is-the-service-available"></a>Waar is de service beschikbaar?

Zie deze pagina voor servicelocatie en beschikbaarheid: [ExpressRoute partners en locaties.](expressroute-locations.md)

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Hoe kan ik ExpressRoute gebruiken om verbinding te maken met Microsoft als ik geen partnerschappen heb met een van de ExpressRoute-carrier partners?

U een regionale provider selecteren en Ethernet-verbindingen landen met een van de ondersteunde locatie's van de exchange provider. U vervolgens peeren met Microsoft op de locatie van de provider. Bekijk het laatste gedeelte van [ExpressRoute-partners en -locaties](expressroute-locations.md) om te zien of uw serviceprovider aanwezig is op een van de uitwisselingslocaties. U vervolgens een ExpressRoute-circuit bestellen via de serviceprovider om verbinding te maken met Azure.

### <a name="how-much-does-expressroute-cost"></a>Hoeveel kost ExpressRoute?

Controleer [prijsgegevens](https://azure.microsoft.com/pricing/details/expressroute/) voor prijsinformatie.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Als ik betaal voor een ExpressRoute-circuit van een bepaalde bandbreedte, moet de VPN-verbinding die ik bij mijn netwerkserviceprovider koop, dan dezelfde snelheid hebben?

Nee. U een VPN-verbinding van elke snelheid bij uw serviceprovider aanschaffen. Uw verbinding met Azure is echter beperkt tot de bandbreedte van het ExpressRoute-circuit die u aanschaft.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Als ik betaal voor een ExpressRoute circuit van een bepaalde bandbreedte, heb ik de mogelijkheid om te barsten tot hogere snelheden indien nodig?

Ja. ExpressRoute-circuits zijn zo geconfigureerd dat u tot twee keer de bandbreedtelimiet laten barsten die u hebt aangeschaft zonder extra kosten. Neem contact op met uw serviceprovider om te zien of ze deze mogelijkheid ondersteunen. Dit is niet voor een langdurige periode en is niet gegarandeerd.  Als het verkeer door een ExpressRoute Gateway stroomt, is de bandbreedte voor de sku vast en niet burstable.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Kan ik dezelfde privénetwerkverbinding met virtueel netwerk en andere Azure-services tegelijkertijd gebruiken?

Ja. Met een ExpressRoute-circuit, die eenmaal is ingesteld, u tegelijkertijd toegang krijgen tot services binnen een virtueel netwerk en andere Azure-services. U maakt verbinding met virtuele netwerken via het privé-peeringpad en met andere services via het Microsoft-peeringpad.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>Hoe worden VNets geadverteerd op ExpressRoute Private Peering?

De ExpressRoute-gateway adverteert met de *adresruimte(en)* van het Azure VNet, u niet opnemen/uitsluiten op subnetniveau. Het is altijd de VNet-adresruimte die wordt geadverteerd. Ook als VNet Peering wordt gebruikt en de peered VNet heeft "Gebruik Remote Gateway" ingeschakeld, de adresruimte van de peered VNet zal ook worden geadverteerd.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Hoeveel voorvoegsels kunnen worden geadverteerd van een VNet naar on-premises op ExpressRoute Private Peering?

Er wordt maximaal 200 voorvoegsels geadverteerd op één ExpressRoute-verbinding of via VNet-peering via gatewaytransit. Als u bijvoorbeeld 199 adresruimten op één VNet hebt aangesloten op een ExpressRoute-circuit, worden alle 199 van deze voorvoegsels geadverteerd aan on-premises. Als u ook een VNet hebt ingeschakeld om gatewaytransit toe te staan met 1 adresruimte en 150 spaakvnets ingeschakeld met de optie 'Externe gateway toestaan', adverteert de VNet die met de gateway is geïmplementeerd 151 voorvoegsels naar on-premises.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>Wat gebeurt er als ik de voorvoegsellimiet voor een ExpressRoute-verbinding verschrijd?

De verbinding tussen het ExpressRoute-circuit en de gateway (en peered VNets met behulp van gateway transit, indien van toepassing) zal naar beneden gaan. Het zal opnieuw vaststellen wanneer de voorvoegsellimiet niet meer wordt overschreden.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>Kan ik routes filteren die afkomstig zijn van mijn on-premises netwerk?

De enige manier om routes te filteren/op te nemen is op de on-premises edge router. Door de gebruiker gedefinieerde routes kunnen in het VNet worden toegevoegd om specifieke routering te beïnvloeden, maar dit is statisch en maakt geen deel uit van de BGP-advertentie.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Biedt ExpressRoute een Service Level Agreement (SLA)?

Zie voor informatie de [PAGINA ExpressRoute SLA.](https://azure.microsoft.com/support/legal/sla/)

## <a name="supported-services"></a>Ondersteunde services

ExpressRoute ondersteunt [drie routeringsdomeinen](expressroute-circuit-peerings.md) voor verschillende soorten services: private peering, Microsoft peering en public peering (afgeschaft).

### <a name="private-peering"></a>Persoonlijke peering

**Ondersteund:**

* Virtuele netwerken, inclusief alle virtuele machines en cloudservices

### <a name="microsoft-peering"></a>Microsoft-peering

Als uw ExpressRoute-circuit is ingeschakeld voor Azure Microsoft-peering, hebt u toegang tot de [openbare IP-adresbereiken](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) die in Azure via het circuit worden gebruikt. Azure Microsoft-peering biedt toegang tot services die momenteel op Azure worden gehost (met geobeperkingen, afhankelijk van de SKU van uw circuit). Als u de beschikbaarheid voor een specifieke service wilt valideren, u de documentatie voor die service controleren om te zien of er een gereserveerd bereik is gepubliceerd voor die service. Zoek vervolgens de IP-bereiken van de doelservice op en vergelijk met de bereiken die worden vermeld in het [Azure IP-bereiken en servicetags - XML-bestand public cloud](https://www.microsoft.com/download/details.aspx?id=56519). U ook een ondersteuningsticket voor de betreffende service openen ter verduidelijking.

**Ondersteund:**

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Power BI - Beschikbaar via een Azure Regional Community, zie [hier](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) hoe u de regio van uw Power BI-tenant achterhalen.
* Azure Active Directory
* [Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/)
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (Azure Global Services-community)
* Azure Public IP-adressen voor IaaS (virtuele machines, virtuele netwerkgateways, load balancers, enz.)  
* De meeste andere Azure-services worden ook ondersteund. Neem rechtstreeks contact op met de service die u wilt gebruiken om de ondersteuning te verifiëren.

**Niet ondersteund:**

* CDN
* Azure Front Door
* Verificatieserver met meerdere factoren (verouderd)
* Traffic Manager

### <a name="public-peering"></a>Openbare peering

Openbare peering is uitgeschakeld op nieuwe ExpressRoute circuits. Azure-services zijn nu beschikbaar op Microsoft-peering. Als u een circuit hebt gemaakt dat is gemaakt voordat openbare peering wordt afgeschaft, u ervoor kiezen om Microsoft-peering of openbare peering te gebruiken, afhankelijk van de services die u wilt.

Zie [ExpressRoute public peering](about-public-peering.md)voor meer informatie en configuratiestappen voor openbaar peering.

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Waarom zie ik de status 'Geadverteerde openbare voorvoegsels' als 'Validatie nodig', terwijl ik Microsoft-peering configureer?

Microsoft controleert of de opgegeven 'Geadverteerde openbare voorvoegsels' en 'Peer ASN' (of 'Klant ASN') aan u zijn toegewezen in het internetrouteringsregister. Als u de openbare voorvoegsels van een andere entiteit ontvangt en als de toewijzing niet wordt geregistreerd met het routeringsregister, wordt de automatische validatie niet voltooid en vereist de handmatige validatie. Als de automatische validatie mislukt, ziet u het bericht 'Validatie nodig'.

Als u het bericht 'Validatie nodig' ziet, verzamelt u het document(en) waarop de openbare voorvoegsels worden weergegeven, worden toegewezen aan uw organisatie door de entiteit die wordt vermeld als de eigenaar van de voorvoegsels in het routeringsregister en dient u deze documenten in voor handmatige validatie door het openen van een support ticket zoals hieronder weergegeven.

![](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Wordt Dynamics 365 ondersteund op ExpressRoute?

Dynamics 365- en Common Data Service-omgevingen (CDS) worden gehost op Azure en daarom profiteren klanten van de onderliggende ExpressRoute-ondersteuning voor Azure-resources. U verbinding maken met de serviceeindpunten als uw routerfilter de Azure-regio's bevat waarin uw Dynamics 365/CDS-omgevingen worden gehost.

> [!NOTE]
> [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) is **niet** vereist voor Dynamics 365-connectiviteit via Azure ExpressRoute.

## <a name="data-and-connections"></a>Gegevens en verbindingen

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Zijn er beperkingen op de hoeveelheid gegevens die ik via ExpressRoute kan overdragen?

We stellen geen limiet vast aan de hoeveelheid gegevensoverdracht. Raadpleeg [prijsdetails](https://azure.microsoft.com/pricing/details/expressroute/) voor informatie over bandbreedtetarieven.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Welke verbindingssnelheden worden ondersteund door ExpressRoute?

Ondersteunde bandbreedteaanbiedingen:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Welke dienstverleners zijn beschikbaar?

Zie [ExpressRoute-partners en locaties](expressroute-locations.md) voor de lijst met dienstverleners en locaties.

## <a name="technical-details"></a>Technische details

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Wat zijn de technische vereisten voor het verbinden van mijn on-premises locatie met Azure?

Zie [de pagina Vereisten expressroute](expressroute-prerequisites.md) voor vereisten.

### <a name="are-connections-to-expressroute-redundant"></a>Zijn verbindingen met ExpressRoute overbodig?

Ja. Elk ExpressRoute-circuit heeft een redundant paar kruisverbindingen geconfigureerd om een hoge beschikbaarheid te bieden.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Verlies ik de verbinding als een van mijn ExpressRoute-links mislukt?

U verliest de verbinding niet als een van de kruisverbindingen uitvalt. Er is een redundante verbinding beschikbaar om de belasting van uw netwerk te ondersteunen en een hoge beschikbaarheid van uw ExpressRoute-circuit te bieden. U bovendien een circuit maken op een andere peeringlocatie om veerkracht op circuitniveau te bereiken.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Hoe implementeer ik redundantie op private peering?

Meerdere ExpressRoute-circuits van verschillende peeringlocaties kunnen worden aangesloten op hetzelfde virtuele netwerk om een hoge beschikbaarheid te bieden in het geval dat één circuit niet beschikbaar is. U vervolgens [hogere gewichten toewijzen](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) aan de lokale verbinding om de voorkeur te geven aan een specifiek circuit. Het wordt ten zeerste aanbevolen dat klanten ten minste twee ExpressRoute-circuits instellen om enkele uitvalpunten te voorkomen. 

Zie [hier](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) voor het ontwerpen voor hoge beschikbaarheid en [hier](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) voor het ontwerpen voor disaster recovery.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Hoe implementeer ik redundantie op Microsoft-peering?

Het wordt ten zeerste aanbevolen wanneer klanten Microsoft-peering gebruiken om toegang te krijgen tot openbare Azure-services zoals Azure Storage of Azure SQL, evenals klanten die Microsoft-peering voor Office 365 gebruiken, dat ze meerdere circuits implementeren in verschillende peering locaties om enkele uitvalpunten te voorkomen. Klanten kunnen hetzelfde voorvoegsel op beide circuits adverteren en ALS PATH gebruiken [voorgebruik of](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) verschillende voorvoegsels adverteren om het pad van on-premises te bepalen.

Zie [hier](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) voor het ontwerpen voor hoge beschikbaarheid.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Hoe zorg ik voor een hoge beschikbaarheid op een virtueel netwerk dat is aangesloten op ExpressRoute?

U hoge beschikbaarheid bereiken door ExpressRoute-circuits op verschillende peeringlocaties (bijvoorbeeld Singapore, Singapore2) aan te sluiten op uw virtuele netwerk. Als een ExpressRoute-circuit uitvalt, mislukt de verbinding naar een ander ExpressRoute-circuit. Verkeer dat uw virtuele netwerk verlaat, wordt standaard doorgestuurd op basis van ECMP (Equal Cost Multi-path Routing). U Verbindingsgewicht gebruiken om het ene circuit boven het andere te verkiezen. Zie [ExpressRoute routering optimaliseren](expressroute-optimize-routing.md)voor meer informatie.

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Hoe zorg ik ervoor dat mijn verkeer dat is bestemd voor Azure Public-services zoals Azure Storage en Azure SQL op Microsoft-peering of openbare peering de voorkeur heeft op het ExpressRoute-pad?

U moet het kenmerk *Lokale voorkeur* op uw router(s) implementeren om ervoor te zorgen dat het pad van on-premises naar Azure altijd de voorkeur heeft op uw ExpressRoute-circuit(s).

Zie [hier](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-on-microsoft-and-public-peerings) meer informatie over BGP-padselectie en algemene routerconfiguraties. 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>Als ik meniet bij een cloudexchange bevind en mijn serviceprovider point-to-point-verbinding biedt, moet ik dan twee fysieke verbindingen bestellen tussen mijn on-premises netwerk en Microsoft?

Als uw serviceprovider via de fysieke verbinding twee virtuele Ethernet-circuits kan maken, hebt u slechts één fysieke verbinding nodig. De fysieke verbinding (bijvoorbeeld een optische vezel) wordt beëindigd op een laag 1 (L1) apparaat (zie de afbeelding). De twee Ethernet virtuele circuits zijn gelabeld met verschillende VLAN ID's, een voor het primaire circuit, en een voor de secundaire. Die VLAN Id's zijn in de buitenste 802.1Q Ethernet header. De binnenste 802.1Q Ethernet-header (niet weergegeven) wordt toegewezen aan een specifiek [Routeringsdomein van ExpressRoute.](expressroute-circuit-peerings.md)

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Kan ik een van mijn VLAN's uitbreiden naar Azure via ExpressRoute?

Nee. We ondersteunen geen layer 2-connectiviteitsextensies in Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Kan ik meer dan één ExpressRoute-circuit in mijn abonnement hebben?

Ja. U meer dan één ExpressRoute-circuit in uw abonnement hebben. De standaardlimiet is ingesteld op 10. U contact opnemen met Microsoft Support om de limiet te verhogen, indien nodig.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Kan ik ExpressRoute-circuits van verschillende serviceproviders hebben?

Ja. U ExpressRoute-circuits hebben bij veel serviceproviders. Elk ExpressRoute-circuit is slechts aan één serviceprovider gekoppeld. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Ik zie twee ExpressRoute peering locaties in dezelfde metro, bijvoorbeeld Singapore en Singapore2. Welke peeringlocatie moet ik kiezen om mijn ExpressRoute-circuit te maken?
Als uw serviceprovider ExpressRoute op beide locaties aanbiedt, u met uw provider samenwerken en een van beide sites kiezen om ExpressRoute in te stellen. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Kan ik meerdere ExpressRoute-circuits in dezelfde metro hebben? Kan ik ze koppelen aan hetzelfde virtuele netwerk?

Ja. U meerdere ExpressRoute-circuits hebben bij dezelfde of verschillende serviceproviders. Als de metro meerdere ExpressRoute-peeringlocaties heeft en de circuits op verschillende peeringlocaties worden gemaakt, u deze koppelen aan hetzelfde virtuele netwerk. Als de circuits op dezelfde peeringlocatie worden gemaakt, u maximaal 4 circuits koppelen aan hetzelfde virtuele netwerk.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Hoe verbind ik mijn virtuele netwerken met een ExpressRoute-circuit

De basisstappen zijn:

* Stel een ExpressRoute-circuit op en laat de serviceprovider dit inschakelen.
* U, of de provider, moet de BGP-peering(s) configureren.
* Koppel het virtuele netwerk aan het ExpressRoute-circuit.

Zie [ExpressRoute-werkstromen voor circuitinrichting en circuitstatussen voor](expressroute-workflows.md)meer informatie.

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Zijn er verbindingsgrenzen voor mijn ExpressRoute-circuit?

Ja. Het [expressroute-artikel over partners en locaties](expressroute-locations.md) geeft een overzicht van de verbindingsgrenzen voor een ExpressRoute-circuit. Connectiviteit voor een ExpressRoute-circuit is beperkt tot één geopolitieke regio. Connectiviteit kan worden uitgebreid tot andere geopolitieke regio's door de ExpressRoute premium-functie in te schakelen.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Kan ik naar meer dan één virtueel netwerk linken naar een ExpressRoute-circuit?

Ja. U maximaal 10 virtuele netwerkverbindingen hebben op een standaard ExpressRoute-circuit en maximaal 100 op een [premium ExpressRoute-circuit.](#expressroute-premium) 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Ik heb meerdere Azure-abonnementen die virtuele netwerken bevatten. Kan ik virtuele netwerken die zich in afzonderlijke abonnementen bevinden aansluiten op één ExpressRoute-circuit?

Ja. U tot 10 virtuele netwerken koppelen in hetzelfde abonnement als het circuit of verschillende abonnementen via één ExpressRoute-circuit. Deze limiet kan worden verhoogd door de ExpressRoute premium-functie in te schakelen.

Zie [Een ExpressRoute-circuit delen voor meerdere abonnementen](expressroute-howto-linkvnet-arm.md)voor meer informatie.

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Ik heb meerdere Azure-abonnementen die zijn gekoppeld aan verschillende Azure Active Directory-tenants of enterprise-overeenkomstinschrijvingen. Kan ik virtuele netwerken die zich in afzonderlijke tenants en inschrijvingen bevinden aansluiten op één ExpressRoute-circuit dat niet in dezelfde tenant of inschrijving is?

Ja. ExpressRoute-autorisaties kunnen de grenzen van abonnementen, tenant en inschrijving strekken zonder extra configuratie. 

Zie [Een ExpressRoute-circuit delen voor meerdere abonnementen](expressroute-howto-linkvnet-arm.md)voor meer informatie.

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Zijn virtuele netwerken verbonden met hetzelfde circuit geïsoleerd van elkaar?

Nee. Vanuit een routeringsperspectief maken alle virtuele netwerken die aan hetzelfde ExpressRoute-circuit zijn gekoppeld, deel uit van hetzelfde routeringsdomein en zijn ze niet van elkaar geïsoleerd. Als u routeisolatie nodig hebt, moet u een apart ExpressRoute-circuit maken.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Kan ik één virtueel netwerk hebben dat is aangesloten op meer dan één ExpressRoute-circuit?

Ja. U één virtueel netwerk koppelen aan maximaal vier ExpressRoute-circuits op dezelfde of verschillende peeringlocaties. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Heb ik toegang tot internet via mijn virtuele netwerken die zijn verbonden met ExpressRoute-circuits?

Ja. Als u via de BGP-sessie geen standaardroutes (0.0.0.0/0) of internetroutevoorvoegsels hebt geadverteerd, u verbinding maken met internet via een virtueel netwerk dat is gekoppeld aan een ExpressRoute-circuit.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Kan ik de internetverbinding met virtuele netwerken blokkeren die zijn aangesloten op ExpressRoute-circuits?

Ja. U standaardroutes adverteren (0.0.0.0/0) om alle internetverbinding met virtuele machines die binnen een virtueel netwerk zijn geïmplementeerd te blokkeren en al het verkeer via het ExpressRoute-circuit te routeren.

Als u standaardroutes adverteert, dwingen we verkeer naar services die worden aangeboden via Microsoft-peering (zoals Azure-opslag en SQL DB) terug naar uw locatie. U moet uw routers configureren om verkeer via het Microsoft-peeringpad of via internet terug te sturen naar Azure. Als u een serviceeindpunt voor de service hebt ingeschakeld, wordt het verkeer naar de service niet naar uw locatie gedwongen. Het verkeer blijft binnen het Azure-backbone-netwerk. Zie [Eindpunten voor virtuele netwerkservice voor](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json) meer informatie over serviceeindpunten

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Kunnen virtuele netwerken die aan hetzelfde ExpressRoute-circuit zijn gekoppeld met elkaar praten?

Ja. Virtuele machines die worden geïmplementeerd in virtuele netwerken die zijn aangesloten op hetzelfde ExpressRoute-circuit, kunnen met elkaar communiceren.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Kan ik site-to-site connectiviteit gebruiken voor virtuele netwerken in combinatie met ExpressRoute?

Ja. ExpressRoute kan naast de site-to-site VPN's bestaan. Zie [ExpressRoute configureren en naast elkaar bestaande verbindingen van site-to-site](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Waarom is er een openbaar IP-adres gekoppeld aan de ExpressRoute-gateway op een virtueel netwerk?

Het openbare IP-adres wordt alleen gebruikt voor intern beheer en vormt geen beveiligingsblootstelling van uw virtuele netwerk.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Zijn er beperkingen op het aantal routes dat ik kan adverteren?

Ja. We accepteren maximaal 4000 routevoorvoegsels voor privé-peering en 200 voor Microsoft-peering. U dit verhogen tot 10.000 routes voor privé-peering als u de ExpressRoute premium-functie inschakelt.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Zijn er beperkingen op IP-bereiken die ik kan adverteren tijdens de BGP-sessie?

We accepteren geen privévoorvoegsels (RFC1918) voor de BGP-sessie met Microsoft-peering. We accepteren elke voorvoegselgrootte (tot /32) op zowel microsoft als het privé-peering.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Wat gebeurt er als ik de BGP-limieten verschrijd?

BGP-sessies worden geschrapt. Ze worden gereset zodra het aantal voorvoegsel onder de limiet komt.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Wat is de ExpressRoute BGP hold time? Kan het worden aangepast?

De wachttijd is 180. De keep-alive berichten worden elke 60 seconden verzonden. Dit zijn vaste instellingen aan de Microsoft-kant die niet kunnen worden gewijzigd. Het is mogelijk voor u om verschillende timers te configureren, en de BGP sessie parameters zullen dienovereenkomstig worden onderhandeld.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Kan ik de bandbreedte van een ExpressRoute-circuit wijzigen?

Ja, u proberen de bandbreedte van uw ExpressRoute-circuit in de Azure-portal te vergroten of door PowerShell te gebruiken. Als er capaciteit beschikbaar is op de fysieke poort waarop uw circuit is gemaakt, slaagt uw wijziging. 

Als uw wijziging mislukt, betekent dit dat er niet genoeg capaciteit over is op de huidige poort en u een nieuw ExpressRoute-circuit met de hogere bandbreedte moet maken, of dat er geen extra capaciteit op die locatie is, in welk geval u de Bandbreedte. 

U zult ook moeten follow-up met uw connectiviteit provider om ervoor te zorgen dat ze de gashendels in hun netwerken bij te werken om de bandbreedte te verhogen ondersteunen. U de bandbreedte van uw ExpressRoute-circuit echter niet verminderen. Je moet een nieuw ExpressRoute circuit maken met een lagere bandbreedte en het oude circuit verwijderen.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Hoe wijzig ik de bandbreedte van een ExpressRoute-circuit?

U de bandbreedte van het ExpressRoute-circuit bijwerken met behulp van de REST API of PowerShell-cmdlet.

## <a name="expressroute-premium"></a>ExpressRoute premie

### <a name="what-is-expressroute-premium"></a>Wat is ExpressRoute premium?

ExpressRoute premium is een verzameling van de volgende functies:

* Verhoogde routeringstabellimiet van 4000 routes naar 10.000 routes voor privé-peering.
* Meer vnets en ExpressRoute Global Reach-verbindingen die kunnen worden ingeschakeld op een ExpressRoute-circuit (standaard is 10). Zie de tabel [ExpressRoute Limieten](#limits) voor meer informatie.
* Connectiviteit met Office 365
* Wereldwijde connectiviteit via het Kernnetwerk van Microsoft. U nu een VNet in een geopolitieke regio koppelen aan een ExpressRoute-circuit in een andere regio.<br>
    **Voorbeelden:**

    *  U een VNet gemaakt in Europa West koppelen aan een ExpressRoute circuit gemaakt in Silicon Valley. 
    *  Op de Microsoft-peering worden voorvoegsels uit andere geopolitieke regio's zodanig geadverteerd dat u verbinding maken met bijvoorbeeld SQL Azure in Europe West vanuit een circuit in Silicon Valley.


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>Hoeveel VNets en ExpressRoute Global Reach-verbindingen kan ik inschakelen op een ExpressRoute-circuit als ik ExpressRoute premium heb ingeschakeld?

In de volgende tabellen worden de ExpressRoute-limieten en het aantal VNets- en ExpressRoute Global Reach-verbindingen per ExpressRoute-circuit weergegeven:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Hoe schakel ik ExpressRoute premium in?

ExpressRoute premium functies kunnen worden ingeschakeld wanneer de functie is ingeschakeld, en kan worden afgesloten door het bijwerken van de circuitstatus. U ExpressRoute premium inschakelen tijdens het maken van circuits, of de REST API / PowerShell-cmdlet aanroepen.

### <a name="how-do-i-disable-expressroute-premium"></a>Hoe schakel ik ExpressRoute premium uit?

U ExpressRoute premium uitschakelen door de REST API of PowerShell-cmdlet aan te roepen. U moet ervoor zorgen dat u uw connectiviteitsbehoeften hebt geschaald om aan de standaardlimieten te voldoen voordat u ExpressRoute-premium uitschakelt. Als uw gebruik hoger is dan de standaardlimieten, mislukt het verzoek om ExpressRoute-premium uit te schakelen.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Kan ik de gewenste functies kiezen uit de premium-functieset?

Nee. Je de functies niet kiezen. We schakelen alle functies in wanneer u ExpressRoute premium inschakelt.

### <a name="how-much-does-expressroute-premium-cost"></a>Hoeveel kost ExpressRoute premium?

Raadpleeg [prijsdetails](https://azure.microsoft.com/pricing/details/expressroute/) voor kosten.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Betaal ik voor ExpressRoute premium in aanvulling op de standaard ExpressRoute kosten?

Ja. ExpressRoute premium kosten van toepassing op de top van ExpressRoute circuit kosten en kosten vereist door de connectiviteit provider.

## <a name="expressroute-local"></a>ExpressRoute Lokaal
### <a name="what-is-expressroute-local"></a>Wat is ExpressRoute Local?
ExpressRoute Local is een SKU van ExpressRoute circuit, naast de Standaard SKU en de Premium SKU. Een belangrijk kenmerk van Lokaal is dat een lokaal circuit op een ExpressRoute-peeringlocatie u alleen toegang geeft tot een of twee Azure-regio's in of nabij dezelfde metro. Een standaardcircuit geeft u daarentegen toegang tot alle Azure-regio's in een geopolitiek gebied en een Premium-circuit voor alle Azure-regio's wereldwijd. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Wat zijn de voordelen van ExpressRoute Local?
Hoewel u de gegevensoverdracht van uw standaard- of Premium ExpressRoute-circuit moet betalen, betaalt u niet afzonderlijk de gegevensoverdracht voor uw ExpressRoute Local-circuit. Met andere woorden, de prijs van ExpressRoute Local is inclusief kosten voor gegevensoverdracht. ExpressRoute Local is een voordeliger oplossing als u enorme hoeveelheden gegevens moet overdragen en u uw gegevens via een privéverbinding meenemen naar een ExpressRoute-peeringlocatie in de buurt van de gewenste Azure-regio's. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Welke functies zijn beschikbaar en wat niet op ExpressRoute Local?
In vergelijking met een Standard ExpressRoute-circuit heeft een lokaal circuit dezelfde functies, behalve:
* Bereik van toegang tot Azure-regio's zoals hierboven beschreven
* ExpressRoute Global Reach is niet beschikbaar op Lokaal

ExpressRoute Local heeft ook dezelfde limieten voor resources (bijvoorbeeld het aantal VNets per circuit) als Standard. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Waar is ExpressRoute Local beschikbaar en aan welke Azure-regio's is elke peeringlocatie toegewezen?
ExpressRoute Local is beschikbaar op de peeringlocaties waar een of twee Azure-regio's in de buurt zijn. Het is niet beschikbaar op een peering-locatie waar geen Azure-regio in die staat of provincie of land is. Zie de exacte toewijzingen op [de pagina Locaties.](expressroute-locations-providers.md)  

## <a name="expressroute-for-office-365"></a>ExpressRoute voor Office 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Hoe maak ik een ExpressRoute-circuit om verbinding te maken met Office 365-services?

1. Bekijk de [pagina Met voorwaarden voor ExpressRoute](expressroute-prerequisites.md) om er zeker van te zijn dat u aan de vereisten voldoet.
2. Bekijk de lijst met serviceproviders en locaties in het artikel [ExpressRoute-partners en locaties](expressroute-locations.md) om ervoor te zorgen dat aan uw connectiviteitsbehoeften wordt voldaan.
3. Plan uw capaciteitsvereisten door [netwerkplanning en prestatieafstemming voor Office 365 te](https://aka.ms/tune/)bekijken.
4. Volg de stappen in de werkstromen voor het instellen van [verbindingsexpressroute-werkstromen voor het inrichten van circuits en circuitstaten.](expressroute-workflows.md)

> [!IMPORTANT]
> Zorg ervoor dat u ExpressRoute premium-add-on hebt ingeschakeld bij het configureren van de connectiviteit met Office 365-services.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services"></a>Kunnen mijn bestaande ExpressRoute-circuits de connectiviteit met Office 365-services ondersteunen?

Ja. Uw bestaande ExpressRoute-circuit kan worden geconfigureerd om connectiviteit met Office 365-services te ondersteunen. Zorg ervoor dat u voldoende capaciteit hebt om verbinding te maken met Office 365-services en dat u premium-invoegtoepassing hebt ingeschakeld. [Met netwerkplanning en prestatieafstemming voor Office 365](https://aka.ms/tune/) u uw connectiviteitsbehoeften plannen. Zie ook [Een ExpressRoute-circuit maken en wijzigen.](expressroute-howto-circuit-classic.md)

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Welke Office 365-services zijn toegankelijk via een ExpressRoute-verbinding?

Raadpleeg de pagina [URL's en IP-adresbereiken van Office 365](https://aka.ms/o365endpoints) voor een actuele lijst met services die via ExpressRoute worden ondersteund.

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Hoeveel kost ExpressRoute voor Office 365-services?

Voor Office 365-services is premium-invoegtoepassing vereist. Zie de [prijsdetails pagina](https://azure.microsoft.com/pricing/details/expressroute/) voor kosten.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>In welke regio's wordt ExpressRoute voor Office 365 ondersteund?

Zie [ExpressRoute partners en locaties](expressroute-locations.md) voor informatie.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Kan ik via internet toegang krijgen tot Office 365, zelfs als ExpressRoute is geconfigureerd voor mijn organisatie?

Ja. Office 365-serviceeindpunten zijn bereikbaar via internet, ook al is ExpressRoute geconfigureerd voor uw netwerk. Neem contact op met het netwerkteam van uw organisatie of het netwerk op uw locatie is geconfigureerd om verbinding te maken met Office 365-services via ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Hoe kan ik een hoge beschikbaarheid plannen voor Office 365-netwerkverkeer op Azure ExpressRoute?
Zie de aanbeveling voor [hoge beschikbaarheid en failover met Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Kan ik toegang krijgen tot GCC-services (Office 365 Government Community) (GCC) via een Azure Us Government ExpressRoute-circuit?

Ja. Office 365 GCC-serviceeindpunten zijn bereikbaar via de Azure Us Government ExpressRoute. U moet echter eerst een ondersteuningsticket openen op de Azure-portal om de voorvoegsels te verstrekken die u van plan bent te adverteren bij Microsoft. Uw connectiviteit met Office 365 GCC-services wordt vastgesteld nadat het ondersteuningsticket is opgelost. 

## <a name="route-filters-for-microsoft-peering"></a>Routefilters voor Microsoft-peering

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Ik ben het inschakelen van Microsoft peering voor de eerste keer, welke routes zal ik zien?

U ziet geen routes. Je moet een routefilter aan je circuit koppelen om advertenties te starten. Zie [Routefilters configureren voor Microsoft-peering voor](how-to-routefilter-powershell.md)instructies.

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Ik heb Microsoft-peering ingeschakeld en nu probeer ik Exchange Online te selecteren, maar het geeft me een foutmelding dat ik niet bevoegd ben om het te doen.

Wanneer u routefilters gebruikt, kan elke klant Microsoft-peering inschakelen. Voor het gebruik van Office 365-services moet u echter nog steeds geautoriseerd worden door Office 365.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Ik heb Microsoft-peering ingeschakeld vóór 1 augustus 2017, hoe kan ik profiteren van routefilters?

Uw bestaande circuit blijft reclame maken voor de voorvoegsels voor Office 365. Als u openbare voorvoegsels van Azure wilt toevoegen via dezelfde Microsoft-peering, u een routefilter maken, de services selecteren die u moet adverteren (inclusief de Office 365-service(s) die u nodig hebt) en het filter aan uw Microsoft-peering koppelen. Zie [Routefilters configureren voor Microsoft-peering voor](how-to-routefilter-powershell.md)instructies.

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Ik heb Microsoft peering op een locatie, nu ben ik proberen om het in te schakelen op een andere locatie en ik zie geen voorvoegsels.

* Microsoft-peering van ExpressRoute-circuits die vóór 1 augustus 2017 zijn geconfigureerd, hebben alle servicevoorvoegsels die worden geadverteerd via Microsoft-peering, zelfs als routefilters niet zijn gedefinieerd.

* Microsoft-peering van ExpressRoute-circuits die op of na 1 augustus 2017 zijn geconfigureerd, worden pas geadverteerd als er een routefilter aan het circuit is gekoppeld. U ziet standaard geen voorvoegsels.

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Globaal bereik

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
