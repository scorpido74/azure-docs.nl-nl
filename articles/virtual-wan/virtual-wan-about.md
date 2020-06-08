---
title: Overzicht van Azure Virtual WAN | Microsoft Docs
description: Lees meer over geautomatiseerde schaalbare verbindingen tussen filialen, beschikbare regio's en partners via Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 05/14/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 8bdba64445212c564a3d4762bc8497be15f7d9a0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657007"
---
# <a name="about-azure-virtual-wan"></a>Over Azure Virtual WAN

Azure Virtual WAN is een netwerkservice die een groot aantal netwerk-, beveiligings- en routeringsfuncties samenbrengt om één operationele interface te bieden. Deze functies omvatten vertakkingsconnectiviteit (via connectiviteitsautomatisering van Virtual WAN Partner-apparaten zoals SD-WAN of VPN CPE), site-naar-site-VPN-connectiviteit, connectiviteit via VPN voor externe gebruikers (punt-naar-site), persoonlijke connectiviteit (ExpressRoute), intra-cloud-connectiviteit (transitieve connectiviteit voor virtuele netwerken), VPN-ExpressRoute-interconnectiviteit, routering, Azure Firewall en versleuteling voor persoonlijke connectiviteit. U hoeft niet al deze use cases te hebben om Virtual WAN te gaan gebruiken. U kunt gewoon aan de slag met slechts één use case en vervolgens uw netwerk aanpassen tijdens de ontwikkeling ervan.

De Virtual WAN-architectuur is een hub- en spoke-architectuur met schaal en prestaties ingebouwd voor vertakkingen (VPN/SD-WAN-apparaten), gebruikers (Azure VPN/OpenVPN/IKEv2-clients), ExpressRoute-circuits en virtuele netwerken. Het maakt een [netwerkarchitectuur voor globale transit](virtual-wan-global-transit-network-architecture.md) mogelijk, waarbij de in de cloud gehoste 'hub' transitieve connectiviteit mogelijk maakt tussen eindpunten die kunnen worden gedistribueerd in verschillende typen spokes.

Azure-regio's fungeren als hubs waarmee u verbinding kunt maken. Alle hubs zijn verbonden in full mesh in een standaard virtuele WAN, waardoor de gebruiker de Microsoft-backbone eenvoudig kan gebruiken voor any-to-any-connectiviteit (elke willekeurige spoke). Gebruikers kunnen spoke-connectiviteit met SD-WAN-/VPN-apparaten handmatig instellen in Azure Virtual WAN of gebruikmaken van de partneroplossing voor Azure Virtual WAN CPE (SD-WAN/VPN) om connectiviteit met Azure in te stellen. Er is een lijst met partners die connectiviteitsautomatisering ondersteunen (mogelijkheid om de apparaatgegevens te exporteren naar Azure, de Azure-configuratie te downloaden en connectiviteit op te zetten) met Azure Virtual WAN. Zie het artikel [Virtual WAN-partners en -locaties](virtual-wan-locations-partners.md) voor meer informatie.

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan1.png)

Met dit artikel krijgt u snel inzicht in de netwerkverbindingen in Azure Virtual WAN. Virtual WAN biedt de volgende voordelen:

* **Geïntegreerde verbindingsoplossingen in de hub en spoke:** automatiseer de interlokale configuratie en verbindingen tussen on-premises locaties en een Azure-hub.
* **Geautomatiseerde installatie en configuratie van spokes:** zorg voor een naadloze verbinding van uw virtuele netwerken en workloads met de Azure-hub.
* **Intuïtieve probleemoplossing:** u beschikt over een overzicht van de end-to-end stroom in Azure en kunt aan de hand van deze informatie de vereiste acties ondernemen.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Virtuele WAN's Basic en Standard

Er zijn twee soorten virtuele WAN's: Basic en Standard. In de volgende tabel staan de beschikbare configuraties voor elk type.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Zie [Een virtuele WAN upgraden van Basic naar Standard](upgrade-virtual-wan.md) voor stappen om een virtuele WAN te upgraden.

## <a name="architecture"></a><a name="architecture"></a>Architectuur

Raadpleeg de volgende artikelen voor meer informatie over de Virtual WAN-architectuur en hoe u migreert naar Virtual WAN:

* [Virtuele WAN-architectuur](migrate-from-hub-spoke-topology.md)
* [Wereldwijde architectuur voor doorvoernetwerk](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Virtual WAN-resources

Als u een end-to-end virtuele WAN wilt configureren, maakt u de volgende resources:

* **virtualWAN:** De virtualWAN-resource vertegenwoordigt een virtuele overlay van uw Azure-netwerk en is een verzameling van meerdere resources. Het bevat koppelingen naar al uw virtuele hubs die u wilt opnemen in het virtuele WAN. Virtual WAN-resources zijn van elkaar geïsoleerd en kunnen geen gemeenschappelijke hub bevatten. Virtuele hubs in Virtual WAN communiceren niet met elkaar.

* **Hub:** een virtuele hub is een virtueel netwerk dat door Microsoft wordt beheerd. De hub bevat verschillende service-eindpunten die verbindingen mogelijk maken. Vanuit uw on-premises netwerk (vpnsite) kunt u verbinding maken met een VPN Gateway binnen de virtuele hub, ExpressRoute-circuits verbinden met een virtuele hub, of zelfs mobiele gebruikers verbinden met een punt-naar-site-gateway in de virtuele hub. De hub is de kern van uw netwerk in een regio. Een Azure-regio mag maar één hub bevatten.

  Een hubgateway is niet hetzelfde als een gateway voor het virtuele netwerk die u voor ExpressRoute en VPN Gateway gebruikt. Wanneer u Virtual WAN gebruikt, maakt u bijvoorbeeld geen site-naar-site-verbinding vanaf uw on-premises locatie rechtstreeks naar uw VNet. In plaats daarvan maakt u een site-naar-site-verbinding naar de hub. Het verkeer verloopt altijd via de hubgateway. Dit houdt in dat uw VNet's hun eigen gateway voor het virtuele netwerk niet nodig hebben. In Virtual WAN kunnen uw VNet's eenvoudig schalen via de virtuele hub en de gateway van de virtuele hub.

* **Hub-verbinding met het virtuele netwerk:** de resource van de hubverbinding met het virtuele netwerk wordt gebruikt om de hub naadloos met het virtuele netwerk te verbinden.

* **(Preview) Hub-naar-hub-verbinding**: hubs zijn allemaal met elkaar verbonden in een virtuele WAN. Dit betekent dat een vertakking, gebruiker of VNet die is verbonden met een lokale hub kan communiceren met een andere vertakking of VNet met behulp van de full mesh-architectuur van de verbonden hubs. U kunt VNets ook verbinden binnen een hub die verkeer via de virtuele hub leidt, evenals VNets op de hub, met behulp van het hub-naar-hub-framework.

* **Tabel met de hubroute:**  u kunt een virtuele hubroute maken en de route toepassen op de routetabel van de virtuele hub. U kunt meerdere routes toepassen op de routetabel van de virtuele hub.

**Aanvullende Virtual WAN-resources**

  * **Site:** Deze resource wordt alleen gebruikt voor site-naar-site-verbindingen. De siteresource is **vpnsite**. Deze staat voor uw on-premises VPN-apparaat en de bijbehorende instellingen. Wanneer u met een Virtual WAN-partner werkt, beschikt u over een geïntegreerde oplossing die deze gegevens automatisch naar Azure exporteert.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Typen connectiviteit

Met Virtual WAN zijn de volgende typen connectiviteit mogelijk: Site-naar-site-VPN, gebruikers-VPN (punt-naar-site) en ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Site-naar-site-VPN-verbindingen

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan.png)

Wanneer u een site-naar-site-verbinding voor een virtuele WAN maakt, kunt u werken met een beschikbare partner. Als u geen partner wilt gebruiken, kunt u de verbinding handmatig configureren. Raadpleeg [Een site-naar-site-verbinding maken met Virtual WAN](virtual-wan-site-to-site-portal.md) voor meer informatie.

#### <a name="virtual-wan-partner-workflow"></a><a name="s2spartner"></a>Partnerworkflow van Virtual WAN-partner

Wanneer u met een Virtual WAN-partner werkt, is dit de workflow:

1. De controller (VPN/SD-WAN) voor filiaalapparaten wordt geverifieerd voor het exporteren van locatiegegevens naar Azure met behulp van een [Azure-service-principal](../active-directory/develop/howto-create-service-principal-portal.md).
2. De controller (VPN/SD-WAN) voor filiaalapparaten ontvangt de Azure-verbindingsconfiguratie en werkt het lokale apparaat bij. Op deze manier wordt het downloaden van de configuratie, het bewerken en het bijwerken van het on-premises VPN-apparaat geautomatiseerd.
3. Wanneer het apparaat eenmaal de juiste Azure-configuratie heeft, wordt een verbinding tussen de locaties (twee actieve tunnels) en Azure WAN tot stand gebracht. Azure ondersteunt zowel IKEv1 als IKEv2. BGP is optioneel.

#### <a name="partners-for-site-to-site-virtual-wan-connections"></a><a name="partners"></a>Partners voor virtuele WAN-verbindingen tussen locaties

Raadpleeg het artikel [Virtuele WAN-partners en locaties](virtual-wan-locations-partners.md) voor een lijst met de beschikbare partners en locaties.

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Gebruikers-VPN-verbindingen (punt-naar-site)

U kunt verbinding maken met uw resources in Azure via een IPsec/IKE (IKEv2)- of OpenVPN-verbinding. Voor dit type verbinding moet een client op de clientcomputer zijn geconfigureerd. Raadpleeg [Een site-naar-site-verbinding maken](virtual-wan-point-to-site-portal.md) voor meer informatie.

### <a name="expressroute-connections"></a><a name="er"></a>ExpressRoute-verbindingen
Met ExpressRoute kunt u uw on-premises netwerk uitbreiden naar Azure via een privéverbinding. Raadpleeg [Een ExpressRoute-verbinding maken met Virtual WAN](virtual-wan-expressroute-portal.md) als u de verbinding tot stand wilt brengen.

## <a name="locations"></a><a name="locations"></a>Locaties

Zie het artikel [Virtual WAN-partners en -locaties](virtual-wan-locations-partners.md) voor informatie over de locatie.

## <a name="faq"></a><a name="faq"></a>Veelgestelde vragen

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Volgende stappen

[Een site-naar-site-verbinding maken met Virtual WAN](virtual-wan-site-to-site-portal.md)
