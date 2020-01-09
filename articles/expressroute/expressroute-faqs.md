---
title: Veelgestelde vragen - Azure ExpressRoute | Microsoft Docs
description: De veelgestelde vragen over de ExpressRoute bevat informatie over ondersteunde Azure-Services, kosten, gegevens en verbindingen, SLA, Providers en locaties, bandbreedte en aanvullende technische gegevens.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: jaredro
ms.openlocfilehash: 734bb48d1ddb50af7c28e948c8267b4cd88fcdf7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437033"
---
# <a name="expressroute-faq"></a>Veelgestelde vragen over ExpressRoute

## <a name="what-is-expressroute"></a>Wat is ExpressRoute?

ExpressRoute is een Azure-service waarmee u particuliere verbindingen maken tussen Microsoft-datacenters en infrastructuur on-premises of in een CO-locatiefaciliteit. ExpressRoute-verbindingen niet het openbare Internet en bieden een hogere beveiliging, betrouwbaarheid en snelheid met kortere wachttijden dan gebruikelijke verbindingen via Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Wat zijn de voordelen van het gebruik van ExpressRoute en VPN-verbindingen?

ExpressRoute-verbindingen gaan niet via het openbare internet. Deze verbindingen bieden een betere beveiliging, betrouwbaarheid en snelheid, met lagere en consistente wachttijden dan gebruikelijke verbindingen via Internet. In sommige gevallen, het gebruik van ExpressRoute-verbindingen voor het overbrengen van gegevens tussen on-premises apparaten en Azure aanzienlijke kostenbesparingen kan opleveren.

### <a name="where-is-the-service-available"></a>Waar de service beschikbaar is?

Deze pagina voor de locatie en de beschikbaarheid wordt weergegeven: [ExpressRoute partners en locaties](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Hoe kan ik ExpressRoute gebruiken om te verbinden met Microsoft als ik geen partnerschappen met een van de ExpressRoute-provider-partners?

U kunt een regionale luchtvaartmaatschappij selecteren en land Ethernet-verbindingen op een van de uitwisseling van de ondersteunde locaties van de provider. U kunt vervolgens koppelen met Microsoft op de providerlocatie. Controleer de laatste sectie van [ExpressRoute partners en locaties](expressroute-locations.md) om te zien of uw serviceprovider aanwezig is in een van de exchange-locaties. Vervolgens kunt u een ExpressRoute-circuit via de service-provider verbinding maken met Azure bestellen.

### <a name="how-much-does-expressroute-cost"></a>Wat kost ExpressRoute?

Controleer [prijsinformatie](https://azure.microsoft.com/pricing/details/expressroute/) voor informatie over prijzen.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Als ik voor een ExpressRoute-circuit van een bepaalde bandbreedte betalen, de VPN-verbinding die ik aankopen op mijn netwerkserviceprovider doen hoeft te zijn dezelfde snelheid?

Nee. U kunt een VPN-verbinding van elke kopen bij uw serviceprovider. Uw verbinding met Azure is echter beperkt tot de bandbreedte van het ExpressRoute-circuit die u aanschaft.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Als ik voor een ExpressRoute-circuit van een bepaalde bandbreedte betalen, heb ik de mogelijkheid om uit te breiden tot hogere snelheden, indien nodig?

Ja. ExpressRoute-circuits zijn geconfigureerd, zodat u om uit te breiden tot twee keer de Bandbreedtelimiet die u voor zonder extra kosten aangeschaft. Neem contact op met uw serviceprovider om te zien als deze mogelijkheid wordt ondersteund. Dit is niet gedurende een langere periode en is niet gegarandeerd.  Als verkeer via een ExpressRoute-gateway loopt, is de band breedte voor de SKU vast en niet bursteel.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Kan ik tegelijkertijd dezelfde particulier netwerkverbinding met virtual network en andere Azure-services gebruiken?

Ja. Een ExpressRoute-circuit, nadat deze is ingesteld, kunt u tegelijkertijd toegang krijgen tot de services binnen een virtueel netwerk en andere Azure-services. U verbinding maken met virtuele netwerken via het pad voor persoonlijke peering en andere services via het pad van de Microsoft-peering.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Biedt ExpressRoute een Service Level Agreement (SLA)?

Zie voor meer informatie, de [ExpressRoute SLA](https://azure.microsoft.com/support/legal/sla/) pagina.

## <a name="supported-services"></a>Ondersteunde services

ExpressRoute ondersteunt [drie routerings domeinen](expressroute-circuit-peerings.md) voor diverse typen services: persoonlijke peering, micro soft-peering en open bare peering (afgeschaft).

### <a name="private-peering"></a>Persoonlijke peering

**Geboden**

* Virtuele netwerken, inclusief alle virtuele machines en cloudservices

### <a name="microsoft-peering"></a>Microsoft-peering

Als uw ExpressRoute-circuit is ingeschakeld voor Azure micro soft-peering, kunt u toegang krijgen tot de [open bare IP-](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) adresbereiken die worden gebruikt in azure via het circuit. Azure micro soft-peering biedt toegang tot services die momenteel worden gehost op Azure (met geografische beperkingen, afhankelijk van de SKU van uw circuit). Als u de beschik baarheid voor een specifieke service wilt valideren, kunt u de documentatie voor die service controleren om te zien of er een gereserveerd bereik voor die service is gepubliceerd. Zoek vervolgens de IP-bereiken van de doel service op en vergelijk met de bereiken die worden vermeld in het [Azure IP-bereik en de service Tags – open bare Cloud XML-bestand](https://www.microsoft.com/download/details.aspx?id=56519). U kunt ook een ondersteunings ticket voor de betreffende service openen ter verduidelijking.

**Geboden**

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Power BI-beschikbaar via een regionale community van Azure, Zie [hier](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) voor meer informatie over de regio van uw Power bi Tenant.
* Azure Active Directory
* [Virtueel bureau blad van Windows](https://azure.microsoft.com/services/virtual-desktop/)
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (Global Services van Azure-community)
* De meeste van de Azure-services worden ondersteund. Controleer of rechtstreeks met de service die u wilt gebruiken om te controleren of de ondersteuning.

**Niet ondersteund:**

* CDN
* Azure Front Door
* Multi-factor Authentication-Server (verouderd)
* Traffic Manager

### <a name="public-peering"></a>Openbare peering

Openbare peering is uitgeschakeld op nieuwe ExpressRoute-circuits. Azure-Services zijn nu beschikbaar op micro soft-peering. Als u een circuit hebt gemaakt dat vóór open bare peering werd afgeschaft, kunt u kiezen voor het gebruik van micro soft-peering of open bare peering, afhankelijk van de gewenste services.

Zie [ExpressRoute Public peering](about-public-peering.md)(Engelstalig) voor meer informatie over en configuratie stappen voor open bare peering.

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Waarom zie ' geadverteerde open bare voor voegsels ' als ' validatie vereist ' tijdens het configureren van micro soft-peering?

Micro soft controleert of de opgegeven ' aangekondigde open bare voor voegsels ' en ' peer-ASN ' (of ' klant-ASN ') aan u zijn toegewezen in het Internet Routing REGI ster. Als u de open bare voor voegsels van een andere entiteit krijgt en de toewijzing niet is vastgelegd met het routerings register, wordt de automatische validatie niet voltooid en moet hand matig worden gevalideerd. Als de automatische validatie mislukt, wordt het bericht validatie vereist weer gegeven.

Als u het bericht validatie vereist ziet, verzamelt u de documenten die de open bare voor voegsels bevatten, worden toegewezen aan uw organisatie door de entiteit die wordt vermeld als eigenaar van de voor voegsels in het routerings register en deze documenten voor hand matige validatie indienen door een ondersteunings ticket openen zoals hieronder wordt weer gegeven.

![](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Wordt Dynamics 365 ondersteund op ExpressRoute?

De omgevingen Dynamics 365 en Common Data Service (CDS) worden gehost op Azure en daarom kunnen klanten profiteren van de onderliggende ExpressRoute-ondersteuning voor Azure-resources. U kunt verbinding maken met de service-eind punten als uw router filter de Azure-regio's bevat waarin uw Dynamics 365/CDS-omgevingen worden gehost.

> [!NOTE]
> [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) is **niet** vereist voor Dynamics 365-connectiviteit via Azure ExpressRoute.

## <a name="data-and-connections"></a>Gegevens en verbindingen

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Zijn er beperkingen met betrekking tot de hoeveelheid gegevens die ik kan overdragen met behulp van ExpressRoute?

We doen niet een limiet ingesteld voor de hoeveelheid gegevens worden overgebracht. Raadpleeg [prijsinformatie](https://azure.microsoft.com/pricing/details/expressroute/) voor meer informatie over tarieven voor bandbreedte.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Welke verbindingssnelheden worden ondersteund door ExpressRoute?

Ondersteunde bandbreedte aanbiedingen:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Welke serviceproviders zijn beschikbaar?

Zie [ExpressRoute partners en locaties](expressroute-locations.md) voor een lijst van serviceproviders en locaties.

## <a name="technical-details"></a>Technische details

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Wat zijn de technische vereisten voor het verbinden van mijn on-premises locatie naar Azure?

Zie [ExpressRoute vereisten pagina](expressroute-prerequisites.md) voor vereisten.

### <a name="are-connections-to-expressroute-redundant"></a>Verbindingen met ExpressRoute redundante zijn?

Ja. Elk ExpressRoute-circuit heeft een redundant paar cross-verbindingen die zijn geconfigureerd voor een hoge beschikbaarheid.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Wordt ik wordt verbroken als een van mijn ExpressRoute-koppelingen niet?

U verliest connectiviteit niet als een van de cross-verbindingen mislukt. Een redundante verbinding is beschikbaar voor ondersteuning van de belasting van uw netwerk en een hoge beschikbaarheid van uw ExpressRoute-circuit. Daarnaast kunt u een circuit maken in een andere locatie voor een circuit op serverniveau flexibiliteit.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Hoe kan ik de redundantie voor privé-peering implementeren?

Meerdere ExpressRoute-circuits van verschillende peering locaties kunnen worden aangesloten op hetzelfde virtuele netwerk om hoge Beschik baarheid te bieden in het geval dat één circuit niet beschikbaar is. U kunt vervolgens [hogere gewichten toewijzen](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) aan de lokale verbinding om voor keur te geven aan een specifiek circuit. Het wordt ten zeerste aanbevolen om ten minste twee ExpressRoute-circuits in te stellen om afzonderlijke storings punten te voor komen. 

Bekijk [hier](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) wat u kunt ontwerpen voor hoge Beschik baarheid en dat u [hier](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) kunt ontwerpen voor herstel na nood gevallen.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Hoe kan ik redundantie implementeren op micro soft-peering?

Het wordt ten zeerste aanbevolen wanneer klanten micro soft-peering gebruiken om toegang te krijgen tot open bare Azure-Services, zoals Azure Storage of Azure SQL, en klanten die gebruikmaken van micro soft-peering voor Office 365 dat ze meerdere circuits in verschillende peering implementeren locaties om individuele storings punten te voor komen. Klanten kunnen hetzelfde voor voegsel op beide circuits adverteren en in [afwachting](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) van het pad gebruiken, of verschillende voor voegsels adverteren om het pad van on-premises te bepalen.

Bekijk [hier](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) wat u kunt ontwerpen voor hoge Beschik baarheid.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Hoe kan ik ervoor zorgen dat hoge beschikbaarheid in een virtueel netwerk is verbonden met ExpressRoute?

U kunt hoge beschikbaarheid bereiken via een ExpressRoute-circuits in verschillende peeringlocaties (bijvoorbeeld, Singapore, Singapore2) verbinding met uw virtuele netwerk. Als één ExpressRoute-circuit uitgeschakeld wordt, wordt connectiviteit schakelt over naar een ander ExpressRoute-circuit. Standaard wordt het virtuele netwerk uitgaand verkeer doorgestuurd op basis van op gelijke kosten Multipath routering (ECMP). U kunt verbinding gewicht gebruiken één aansluiting liever naar een andere. Zie voor meer informatie, [ExpressRoute-routering optimaliseren](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Hoe kan ik ervoor te zorgen dat mijn verkeer dat is bestemd voor open bare Azure-Services, zoals Azure Storage en Azure SQL op micro soft-peering of open bare peering, de voor keur heeft op het ExpressRoute-pad?

U moet het *lokale voorkeurs* kenmerk op uw router (s) implementeren om ervoor te zorgen dat het pad van on-premises naar Azure altijd de voor keur heeft voor uw ExpressRoute-circuit (s).

Zie [hier](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-on-microsoft-and-public-peerings) voor meer informatie over het selecteren van BGP-paden en algemene router configuraties. 

### <a name="onep2plink"></a>Als ik geen CO-locatie op een cloudexchange ben en mijn serviceprovider point-to-point-verbinding biedt, heb ik nodig om twee fysieke verbindingen tussen mijn on-premises netwerk en Microsoft?

Als uw serviceprovider twee virtuele Ethernet-circuits via de fysieke verbinding maken kan, moet u slechts één fysieke verbinding. De fysieke verbinding (bijvoorbeeld in een fiber optische) wordt beëindigd op een laag 1 (L1)-apparaat (Zie de afbeelding). De twee virtuele Ethernet-circuits zijn gelabeld met verschillende VLAN-id's, één voor het primaire circuit en één voor de secundaire server. Deze VLAN-id's zijn in de buitenste 802.1Q Ethernet-header. De binnenste 802.1Q Ethernet-header (niet weergegeven) is toegewezen aan een specifieke [ExpressRoute routeringsdomein](expressroute-circuit-peerings.md).

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Kan ik een van mijn VLAN's uit te breiden met behulp van ExpressRoute?

Nee. We ondersteunen geen layer 2-connectiviteit uitbreidingen in Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Kan ik meer dan één ExpressRoute-circuit hebben in mijn abonnement?

Ja. U kunt meer dan één ExpressRoute-circuit in uw abonnement hebt. De standaardlimiet is ingesteld op 10. U kunt contact opnemen met Microsoft Support om de limiet te verhogen indien nodig.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Kan ik ExpressRoute-circuits van andere serviceproviders hebben?

Ja. U kunt ExpressRoute-circuits hebt met veel serviceproviders. Elk ExpressRoute-circuit is gekoppeld aan een serviceprovider. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Ik zie twee ExpressRoute-peeringlocaties in de dezelfde metro bijvoorbeeld Singapore en Singapore2. Welke peeringlocatie moet ik kiezen om te maken van mijn ExpressRoute-circuit?
Als uw serviceprovider ExpressRoute op beide sites biedt, kunt u werken met uw provider en kies een van beide site voor het instellen van ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Kan ik meerdere ExpressRoute-circuits hebben in de dezelfde metro? Kan ik deze koppelen aan hetzelfde virtuele netwerk?

Ja. U kunt meerdere ExpressRoute-circuits hebt met de dezelfde of verschillende serviceproviders. Als de metro meerdere ExpressRoute-peeringlocaties heeft en de circuits van worden gemaakt op verschillende locaties voor peering, kunt u ze kunt koppelen aan hetzelfde virtuele netwerk. Als de circuits op dezelfde peering-locatie worden gemaakt, kunt u Maxi maal 4 circuits koppelen aan hetzelfde virtuele netwerk.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Hoe ik mijn virtuele netwerken verbinden met een ExpressRoute-circuit

De eenvoudige stappen zijn:

* Een ExpressRoute-circuit tot stand brengen en hebben de serviceprovider inschakelen.
* U of de provider, moet het BGP peering (s) configureren.
* Het virtueel netwerk koppelen aan het ExpressRoute-circuit.

Zie voor meer informatie, [ExpressRoute-werkstromen voor circuitinrichting en circuitstatussen](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Zijn er begrenzing connectiviteit voor mijn ExpressRoute-circuit?

Ja. De [ExpressRoute partners en locaties](expressroute-locations.md) artikel biedt een overzicht van de grenzen van de verbinding voor een ExpressRoute-circuit. Connectiviteit voor een ExpressRoute-circuit is beperkt tot één geopolitieke regio. Connectiviteit kan worden uitgebreid om cross-geopolitieke regio's door in te schakelen van de ExpressRoute premium-functie.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Kan ik naar meer dan één virtueel netwerk aan een ExpressRoute-circuit koppelen?

Ja. U kunt maximaal 10 virtuele netwerken-verbindingen op een standard ExpressRoute-circuit, en tot wel 100 op hebben een [premium ExpressRoute-circuit](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Ik heb meerdere Azure-abonnementen met virtuele netwerken. Kan ik virtuele netwerken koppelen die zich in afzonderlijke abonnementen op één ExpressRoute-circuit?

Ja. U kunt Maxi maal 10 virtuele netwerken in hetzelfde abonnement als het circuit of andere abonnementen koppelen met een enkel ExpressRoute-circuit. Deze limiet kan worden verhoogd met het inschakelen van de ExpressRoute premium-functie.

Zie voor meer informatie, [delen van een ExpressRoute-circuit voor meerdere abonnementen](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Ik heb meerdere Azure-abonnementen die zijn gekoppeld aan verschillende tenants van Azure Active Directory of Enterprise Agreement-inschrijvingen. Kan ik virtuele netwerken die zich in afzonderlijke tenants en inschrijvingen voor een enkel ExpressRoute-circuit niet in dezelfde tenant of inschrijving verbinding maken?

Ja. Autorisaties voor ExpressRoute kunnen abonnement, de tenant en de inschrijving grenzen omvatten zonder aanvullende configuratie vereist. 

Zie voor meer informatie, [delen van een ExpressRoute-circuit voor meerdere abonnementen](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Worden virtuele netwerken worden gekoppeld aan hetzelfde circuit geïsoleerd van elkaar?

Nee. Vanuit een perspectief routering, worden alle virtuele netwerken die zijn gekoppeld aan hetzelfde ExpressRoute-circuit deel uitmaken van hetzelfde routeringsdomein en zijn niet van elkaar geïsoleerd. Als u route isolatie nodig hebt, moet u een afzonderlijke ExpressRoute-circuit maken.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Kan ik een virtueel netwerk verbonden met meer dan één ExpressRoute-circuit hebben?

Ja. U kunt één virtueel netwerk met Maxi maal vier ExpressRoute-circuits koppelen aan dezelfde of een andere peering-locatie. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Kan ik toegang tot het Internet van mijn virtuele netwerken die zijn verbonden met ExpressRoute-circuits?

Ja. Als u hebt niet geadverteerd standaardroutes (0.0.0.0/0) of Internet route voorvoegsels via de BGP-sessie, kunt u verbinding maken met Internet vanuit een virtueel netwerk dat is gekoppeld aan een ExpressRoute-circuit.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Kan ik verbinding met Internet aan virtuele netwerken die zijn verbonden met ExpressRoute-circuits blokkeren?

Ja. U kunt de standaardroutes bekendmaakt (0.0.0.0/0) voor het blokkeren van alle internetverbinding met virtuele machines die binnen een virtueel netwerk is geïmplementeerd en routeren van al het verkeer af via het ExpressRoute-circuit.

Als u standaardroutes bekendmaakt, dwingen we verkeer naar services die worden aangeboden via Microsoft-peering (zoals Azure storage en SQL-database) terug naar uw locatie. U moet uw routers voor het retourneren van verkeer naar Azure via het pad van de Microsoft-peering of via Internet configureren. Als u een service-eindpunt voor de service hebt ingeschakeld, wordt het verkeer naar de service wordt niet geforceerd op uw locatie. Het verkeer blijft in het Azure-backbone-netwerk. Zie voor meer informatie over service-eindpunten, [service-eindpunten voor virtuele netwerken](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Kunnen virtuele netwerken die zijn gekoppeld aan hetzelfde ExpressRoute-circuit met elkaar communiceren?

Ja. Virtuele machines die worden geïmplementeerd in virtuele netwerken die zijn verbonden met hetzelfde ExpressRoute-circuit kunnen met elkaar communiceren.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Kan ik site-naar-site-connectiviteit voor virtuele netwerken in combinatie met ExpressRoute gebruiken?

Ja. ExpressRoute kan worden gecombineerd met site-naar-site VPN-verbindingen. Zie [configureren van ExpressRoute en site-naar-site-verbindingen naast elkaar](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Waarom is er een openbaar IP-adres dat is gekoppeld aan de ExpressRoute-gateway in een virtueel netwerk?

Het openbare IP-adres wordt gebruikt voor interne beheer alleen en geen vormt een beveiligingsrisico van uw virtuele netwerk.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Zijn er beperkingen met betrekking tot het aantal routes dat ik kunt adverteren?

Ja. We accepteren maximaal 4000 voorvoegsels van de route voor persoonlijke peering en 200 voor Microsoft-peering. U kunt deze verhogen tot 10.000 routes voor persoonlijke peering als u de ExpressRoute premium-functie inschakelt.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Zijn er beperkingen voor IP-adresbereiken die ik via de BGP-sessie adverteren kunt?

We accepteren geen persoonlijke voorvoegsels (RFC1918) voor de Microsoft-peering BGP-sessie. We accepteren elke voorvoegsel grootte (Maxi maal/32) op zowel micro soft als de persoonlijke peering.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Wat gebeurt er als ik het BGP beperkt?

BGP-sessies wordt verwijderd. Ze worden opnieuw ingesteld zodra het aantal voorvoegsel lager de limiet is.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Wat is de tijd van de blokkering BGP ExpressRoute? Kan het worden aangepast?

De tijd van de blokkering is 180. De keepalive berichten worden elke 60 seconden. Deze worden instellingen opgelost aan de kant van Microsoft die niet kan worden gewijzigd. Het is mogelijk dat u verschillende timers configureren en de parameters van BGP-sessie wordt onderhandeld over dienovereenkomstig.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Kan ik de bandbreedte van een ExpressRoute-circuit wijzigen?

Ja, kunt u proberen te verhogen van de bandbreedte van uw ExpressRoute-circuit in Azure portal of met behulp van PowerShell. Als er capaciteit beschikbaar op de fysieke poort waarop uw circuit is gemaakt is, wordt de wijziging lukt. 

Als de wijziging is mislukt, betekent dit dat ofwel er is onvoldoende capaciteit die links op de huidige poort en moet u een nieuwe ExpressRoute-circuit maken met de hogere bandbreedte of dat er geen extra capaciteit op die locatie, in dat geval kunt u zich niet te verhogen de de bandbreedte. 

U moet ook contact opnemen met uw connectiviteitsprovider om ervoor te zorgen dat ze de vertragingen in hun netwerken voor de ondersteuning van de bandbreedte vergroten bijwerken. U kunt de bandbreedte van uw ExpressRoute-circuit echter niet, reduceren. U moet een nieuwe ExpressRoute-circuit maken met een lagere bandbreedte en het oude circuit verwijderen.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Hoe kan ik de bandbreedte van een ExpressRoute-circuit wijzigen?

U kunt de bandbreedte van het ExpressRoute-circuit met de REST API of PowerShell-cmdlet bijwerken.

## <a name="expressroute-premium"></a>ExpressRoute premium

### <a name="what-is-expressroute-premium"></a>Wat is ExpressRoute premium?

ExpressRoute premium is een verzameling van de volgende functies:

* Verhoogd routering tabellimiet van 4000 routes tot 10.000 routes voor persoonlijke peering.
* Een hoger aantal vnet's en globale bereiken ExpressRoute-verbindingen die kunnen worden ingeschakeld op een ExpressRoute-circuit (de standaardwaarde is 10). Zie voor meer informatie de [limieten voor ExpressRoute](#limits) tabel.
* Connectiviteit met Office 365
* Globale connectiviteit via de Microsoft core-netwerk. U kunt nu een VNet in een geopolitieke regio met een ExpressRoute-circuit in een andere regio koppelen.<br>
    **Voorbeelden:**

    *  U kunt een VNet dat is gemaakt in West-Europa aan een ExpressRoute-circuit gemaakt in Silicon Valley koppelen. 
    *  Op de Microsoft-peering, worden de voorvoegsels van andere geopolitieke regio's worden geadverteerd, zodat u verbinding, bijvoorbeeld SQL Azure in Europa (West) vanuit een circuit in Silicon Valley maken kunt.


### <a name="limits"></a>Het aantal verbindingen voor vnet's en ExpressRoute globaal bereik kan ik inschakelen op een ExpressRoute-circuit als ik ExpressRoute premium ingeschakeld?

De volgende tabellen tonen de limieten voor ExpressRoute en het aantal vnet's en globale bereiken ExpressRoute-verbindingen per ExpressRoute-circuit:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Hoe kan ik ExpressRoute premium inschakelen?

ExpressRoute premium-functies kunnen worden ingeschakeld wanneer de functie is ingeschakeld en kunnen worden afgesloten door het bijwerken van de status van het circuit. U kunt ExpressRoute premium inschakelen tijdens de aanmaak van circuit of de REST-API kunt aanroepen / PowerShell-cmdlet.

### <a name="how-do-i-disable-expressroute-premium"></a>Hoe kan ik ExpressRoute premium uitschakelen?

U kunt de ExpressRoute premium uitschakelen door het aanroepen van de REST API of PowerShell-cmdlet. U moet ervoor zorgen dat u de behoeften van uw verbinding om te voldoen aan de standaardlimieten voordat u ExpressRoute premium uitschakelen hebt aangepast. Als uw gebruik kan worden geschaald dan de standaardlimieten, mislukt de aanvraag voor het uitschakelen van ExpressRoute premium.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Kan ik kiezen en kiezen in de functies die ik wil dat uit de premium-functieset?

Nee. De functies kan niet worden opgenomen. We inschakelen alle functies wanneer u ExpressRoute premium inschakelen.

### <a name="how-much-does-expressroute-premium-cost"></a>Wat kost ExpressRoute premium?

Raadpleeg [prijsinformatie](https://azure.microsoft.com/pricing/details/expressroute/) voor kosten.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Moet ik betalen voor de ExpressRoute premium naast standard ExpressRoute-kosten in rekening gebracht?

Ja. ExpressRoute premium kosten gelden boven op de kosten voor ExpressRoute-circuit en de kosten die zijn vereist door de connectiviteitsprovider.

## <a name="expressroute-local"></a>Lokale ExpressRoute
### <a name="what-is-expressroute-local"></a>Wat is ExpressRoute lokaal?
ExpressRoute local is een SKU van ExpressRoute-circuit, naast de standaard-SKU en de Premium-SKU. Een belang rijke functie van Local is dat een lokaal circuit op een ExpressRoute-peering-locatie u alleen toegang biedt tot een of twee Azure-regio's in of in de buurt van dezelfde metro. Met een standaard circuit hebt u daarentegen toegang tot alle Azure-regio's in een geopolitieke regio en een Premium-circuit naar alle Azure-regio's. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Wat zijn de voor delen van ExpressRoute lokale?
Wanneer u een uitgangs gegevens overdracht moet betalen voor uw Standard-of Premium ExpressRoute-circuit, betaalt u geen uitgaande gegevens overdracht afzonderlijk voor uw lokale circuit van ExpressRoute. Met andere woorden, de prijs van de lokale ExpressRoute omvat ook kosten voor gegevens overdracht. ExpressRoute local is een rendabelere oplossing als u grote hoeveel heden gegevens wilt overdragen en u uw gegevens via een particuliere verbinding met een ExpressRoute-peering-locatie in de buurt van uw gewenste Azure-regio's kunt brengen. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Welke functies zijn er beschikbaar en wat bevinden zich niet op ExpressRoute lokale locatie?
Vergeleken met een standaard ExpressRoute-circuit heeft een lokaal circuit dezelfde set functies, behalve:
* Bereik van toegang tot Azure-regio's zoals hierboven wordt beschreven
* ExpressRoute Global Reach is niet beschikbaar op de lokale locatie

ExpressRoute Local heeft ook dezelfde limieten voor bronnen (bijvoorbeeld het aantal VNets per circuit) als standaard. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Waar is ExpressRoute lokaal beschikbaar en welke Azure-regio's wordt elke peering-locatie toegewezen aan?
ExpressRoute local is beschikbaar op de peering locaties waar een of twee Azure-regio's sluitend zijn. Het is niet beschikbaar op een peering-locatie waar zich geen Azure-regio in die staat of provincie bevindt. Zie de exacte toewijzingen op [de pagina locaties](expressroute-locations-providers.md).  

## <a name="expressroute-for-office-365"></a>ExpressRoute voor Office 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Hoe maak ik een ExpressRoute-circuit verbinding maken met Office 365-services?

1. Controleer de [ExpressRoute vereisten pagina](expressroute-prerequisites.md) om ervoor te zorgen dat u voldoet aan de vereisten.
2. Om ervoor te zorgen dat aan de behoeften van uw connectiviteit wordt voldaan, bekijk de lijst van serviceproviders en locaties in de [ExpressRoute partners en locaties](expressroute-locations.md) artikel.
3. Aan de hand van plan bent uw capaciteitsvereisten [netwerkplanning en prestatieafstemming voor Office 365](https://aka.ms/tune/).
4. Volg de stappen die worden vermeld in de werkstromen voor het instellen van connectiviteit [ExpressRoute-werkstromen voor circuitinrichting en circuitstatussen](expressroute-workflows.md).

> [!IMPORTANT]
> Zorg ervoor dat u premium-invoegtoepassing voor ExpressRoute hebt ingeschakeld bij het configureren van de verbinding met Office 365-services.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services"></a>Kunnen mijn huidige ExpressRoute-circuits connectiviteit ondersteunen met Office 365-Services?

Ja. Uw bestaande ExpressRoute-circuit kan worden geconfigureerd ter ondersteuning van de verbinding met Office 365-services. Zorg ervoor dat u hebt onvoldoende capaciteit om te verbinden met Office 365-services en dat u premium-invoegtoepassing hebt ingeschakeld. [Netwerkplanning en prestatieafstemming voor Office 365](https://aka.ms/tune/) helpt u van plan bent uw verbinding nodig heeft. Zie ook [maken en aanpassen van een ExpressRoute-circuit](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Welke Office 365 services zijn toegankelijk via een ExpressRoute-verbinding?

Raadpleeg [Office 365-URL's en IP-adresbereiken](https://aka.ms/o365endpoints) pagina voor een bijgewerkte lijst met services die via ExpressRoute worden ondersteund.

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Wat kost ExpressRoute voor Office 365-services?

Office 365-services vereist premium-invoegtoepassing wordt ingeschakeld. Zie de [pagina met prijsinformatie](https://azure.microsoft.com/pricing/details/expressroute/) voor kosten.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Welke regio's wordt ExpressRoute voor Office 365 ondersteund?

Zie [ExpressRoute partners en locaties](expressroute-locations.md) voor meer informatie.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Krijg ik toegang tot Office 365 via Internet, zelfs als ExpressRoute is geconfigureerd voor mijn organisatie?

Ja. Office 365-service-eindpunten zijn bereikbaar via het Internet, hoewel ExpressRoute voor uw netwerk is geconfigureerd. Neem contact op met uw organisatie networking-team als het netwerk op de locatie is geconfigureerd voor het verbinding maken met Office 365-services via ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Hoe kan ik plannen voor hoge beschikbaarheid voor Office 365-netwerkverkeer op Azure ExpressRoute?
Zie de aanbeveling voor [hoge beschikbaarheid en failover met Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Kan ik toegang tot Office 365 US Government Community (GCC) services via een Azure US Government ExpressRoute-circuit?

Ja. Office 365 GCC service-eindpunten zijn bereikbaar via het Azure US Government ExpressRoute. Echter, moet u eerst een ondersteuningsticket in Azure portal voor de voorvoegsels die u van plan bent om te adverteren naar Microsoft. De verbinding met Office 365 GCC-services wordt tot stand worden gebracht nadat de ondersteuningsticket is opgelost. 

## <a name="route-filters-for-microsoft-peering"></a>Routefilters voor Microsoft-peering

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Ik ben inschakelen van het Microsoft-peering voor het eerst welke routes ziet ik?

Niet ziet u alle routes. U moet een routefilter koppelen aan uw circuit voorvoegsel advertenties te starten. Zie voor instructies [configureren routefilters voor Microsoft-peering](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Kan ik Microsoft-peering ingeschakeld en ik wil nu selecteert u Exchange Online, maar het biedt me een foutbericht dat ik niet ben gemachtigd om dit te doen.

Wanneer u routefilters, kan een klant inschakelen Microsoft-peering. Voor het gebruik van Office 365-services, moet u echter wel tot ophalen geautoriseerd door Office 365.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Kan ik Microsoft-peering vóór 1 augustus 2017, hoe kan ik profiteren van routefilters ingeschakeld?

Uw bestaande circuit gaat verder met het adverteren van de voor voegsels voor Office 365. Als u advertenties voor open bare Azure-voor voegsels wilt toevoegen aan dezelfde micro soft-peering, kunt u een route filter maken, de services selecteren die u nodig hebt (met inbegrip van de Office 365-service (s) die u nodig hebt) en het filter koppelen aan uw micro soft-peering. Zie voor instructies [configureren routefilters voor Microsoft-peering](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Ik heb Microsoft-peering op één locatie, nu ik probeer uit te schakelen op een andere locatie en ik zie niet alle voorvoegsels.

* Microsoft-peering van ExpressRoute-circuits die zijn geconfigureerd vóór 1 augustus 2017 hebben alle service-voorvoegsels geadverteerd via Microsoft-peering, zelfs als routefilters zijn niet gedefinieerd.

* Microsoft-peering van ExpressRoute-circuits die zijn geconfigureerd op of na 1 augustus 2017 hebben geen voorvoegsels geadverteerd totdat een routefilter is gekoppeld aan het circuit. Standaard ziet u geen voorvoegsels.

## <a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
