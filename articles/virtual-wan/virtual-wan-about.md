---
title: Overzicht van Azure Virtual WAN | Microsoft Docs
description: Meer informatie over virtuele WAN-geautomatiseerde schaalbare branch-to-branch-connectiviteit, beschikbare regio's en partners.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 9ac70252ce7c818ccbdecfd996b9970f011aa967
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241414"
---
# <a name="about-azure-virtual-wan"></a>Over Azure Virtual WAN

Azure Virtual WAN is een netwerkservice die geoptimaliseerde en geautomatiseerde branchconnectiviteit biedt met en door Azure. Azure-regio's dienen als hubs waarmee u kiezen om uw branches aan te sluiten. U de Azure-backbone gebruiken om ook branches met elkaar te verbinden en te genieten van branch-to-VNet-connectiviteit. We hebben een lijst met partners die connectiviteitsautomatisering ondersteunen met Azure Virtual WAN VPN. Zie het artikel [Virtual WAN-partners en locaties](virtual-wan-locations-partners.md) voor meer informatie.

Azure Virtual WAN brengt veel Azure-cloudconnectiviteitsservices samen, zoals site-to-site VPN, User VPN (point-to-site) en ExpressRoute in één operationele interface. Connectiviteit met Azure VNets wordt tot stand gebracht door gebruik te maken van virtuele netwerkverbindingen. Het maakt [wereldwijde transitnetwerkarchitectuur](virtual-wan-global-transit-network-architecture.md) mogelijk op basis van een klassiek hub-and-spoke connectiviteitsmodel waarbij de cloud gehoste netwerkhub transitive connectiviteit mogelijk maakt tussen eindpunten die over verschillende soorten 'spaken' kunnen worden verdeeld.

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan1.png)

In dit artikel vindt u een snelle weergave van de netwerkconnectiviteit in Azure Virtual WAN. Virtual WAN biedt de volgende voordelen:

* **Geïntegreerde connectiviteitsoplossingen in hub en spoke:** Automatiseer site-to-site configuratie en connectiviteit tussen on-premises sites en een Azure-hub.
* **Geautomatiseerde installatie en configuratie van spokes:** zorg voor een naadloze verbinding van uw virtuele netwerken en workloads met de Azure-hub.
* **Intuïtieve probleemoplossing:** U de end-to-end-stroom binnen Azure bekijken en deze informatie vervolgens gebruiken om vereiste acties uit te voeren.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Virtuele WAN's voor basis- en standaardproducten

Er zijn twee soorten virtuele WAN's: Basic en Standard. In de volgende tabel worden de beschikbare configuraties voor elk type weergegeven.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Zie [Een virtueel WAN upgraden van Basic naar Standard](upgrade-virtual-wan.md)voor stappen om een virtuele WAN te upgraden.

## <a name="architecture"></a><a name="architecture"></a>Architectuur

Zie de volgende artikelen voor informatie over de Virtuele WAN-architectuur en hoe u migreren naar Virtual WAN:

* [Virtuele WAN-architectuur](migrate-from-hub-spoke-topology.md)
* [Wereldwijde architectuur voor doorvoernetwerk](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Virtual WAN-resources

Als u een end-to-end virtuele WAN wilt configureren, maakt u de volgende resources:

* **virtualWAN:** de virtualWAN-resource staat voor een virtuele overlay van uw Azure-netwerk en is een verzameling van meerdere resources. Het bevat koppelingen naar al uw virtuele hubs die u wilt opnemen in het virtuele WAN. Virtual WAN-resources zijn van elkaar geïsoleerd en kunnen geen gemeenschappelijke hub bevatten. Virtuele hubs in Virtual WAN communiceren niet met elkaar.

* **Hub:** een virtuele hub is een virtueel netwerk dat door Microsoft wordt beheerd. De hub bevat verschillende serviceeindpunten om connectiviteit mogelijk te maken. Vanuit uw on-premises netwerk (vpn-site) u verbinding maken met een VPN-gateway in de virtuele hub, ExpressRoute-circuits verbinden met een virtuele hub of zelfs mobiele gebruikers verbinden met een Point-to-site-gateway in de virtuele hub. De hub is de kern van uw netwerk in een regio. Een Azure-regio mag maar één hub bevatten.

  Een hubgateway is niet hetzelfde als een gateway voor het virtuele netwerk die u voor ExpressRoute en VPN Gateway gebruikt. Wanneer u bijvoorbeeld Virtual WAN gebruikt, maakt u geen site-to-site-verbinding van uw on-premises site rechtstreeks naar uw VNet. In plaats daarvan maakt u een site-to-site-verbinding met de hub. Het verkeer verloopt altijd via de hubgateway. Dit houdt in dat uw VNet's hun eigen gateway voor het virtuele netwerk niet nodig hebben. In Virtual WAN kunnen uw VNet's eenvoudig schalen via de virtuele hub en de gateway van de virtuele hub.

* **Hub-verbinding met het virtuele netwerk:** de resource van de hub-verbinding met het virtuele netwerk wordt gebruikt om de hub naadloos met het virtuele netwerk te verbinden.

* **(Preview) Hub-to-Hub-verbinding** - Hubs zijn allemaal met elkaar verbonden in een virtueel WAN. Dit houdt in dat een tak, gebruiker of VNet die is verbonden met een lokale hub kan communiceren met een andere branch of VNet met behulp van de volledige mesh-architectuur van de verbonden hubs. U vnets ook aansluiten binnen een hub die via de virtuele hub wordt geleid, evenals VNets via hub, met behulp van het hub-to-hub connected framework.

* **De tabel met de hubroute:** u kunt een virtuele-hubroute maken en de route toepassen op de routetabel van de virtuele hub. U kunt meerdere routes toepassen op de routetabel van de virtuele hub.

**Aanvullende virtuele WAN-resources**

  * **Site:** Deze bron wordt alleen gebruikt voor site-to-site verbindingen. De site bron is **vpnsite**. Het vertegenwoordigt uw on-premises VPN-apparaat en de instellingen. Wanneer u met een Virtual WAN-partner werkt, beschikt u over een geïntegreerde oplossing die deze gegevens automatisch naar Azure exporteert.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Typen connectiviteit

Virtual WAN maakt de volgende soorten connectiviteit mogelijk: Site-to-Site VPN, User VPN (Point-to-Site) en ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Site-naar-site VPN-verbindingen

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan.png)

Wanneer u een virtuele WAN-site-to-site-verbinding maakt, u samenwerken met een beschikbare partner. Als u geen partner wilt gebruiken, u de verbinding handmatig configureren. Zie [Een site-to-site-verbinding maken met Virtual WAN](virtual-wan-site-to-site-portal.md)voor meer informatie.

#### <a name="virtual-wan-partner-workflow"></a><a name="s2spartner"></a>Virtuele WAN-partnerwerkstroom

Wanneer u met een Virtuele WAN-partner werkt, is de werkstroom:

1. De branch device (VPN/SDWAN)-controller is geverifieerd om sitecentrische informatie naar Azure te exporteren met behulp van een [Azure Service Principal.](../active-directory/develop/howto-create-service-principal-portal.md)
2. De controller (VPN/SD-WAN) voor filiaalapparaten ontvangt de Azure-verbindingsconfiguratie en werkt het lokale apparaat bij. Op deze manier wordt het downloaden van de configuratie, het bewerken en het bijwerken van het on-premises VPN-apparaat geautomatiseerd.
3. Wanneer het apparaat eenmaal de juiste Azure-configuratie heeft, wordt een verbinding tussen de locaties (twee actieve tunnels) en Azure WAN tot stand gebracht. Azure ondersteunt zowel IKEv1 als IKEv2. BGP is optioneel.

#### <a name="partners-for-site-to-site-virtual-wan-connections"></a><a name="partners"></a>Partners voor site-to-site virtuele WAN-verbindingen

Zie het artikel [Virtual WAN-partners en locaties](virtual-wan-locations-partners.md) voor een lijst met beschikbare partners en locaties.

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Vpn-verbindingen van gebruikers (point-to-site)

U verbinding maken met uw resources in Azure via een IPsec/IKE (IKEv2) of OpenVPN-verbinding. Voor dit type verbinding moet een VPN-client worden geconfigureerd op de clientcomputer. Zie [Een point-to-site-verbinding maken](virtual-wan-point-to-site-portal.md)voor meer informatie.

### <a name="expressroute-connections"></a><a name="er"></a>ExpressRoute-verbindingen
Met ExpressRoute u via een privéverbinding een on-premises netwerk verbinden met Azure. Zie [Een ExpressRoute-verbinding maken met Virtual WAN](virtual-wan-expressroute-portal.md)als u de verbinding wilt maken.

## <a name="locations"></a><a name="locations"></a>Locaties

Zie het artikel [Virtual WAN-partners en locaties](virtual-wan-locations-partners.md) voor locatiegegevens.

## <a name="faq"></a><a name="faq"></a>Veelgestelde vragen

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Volgende stappen

[Een site-to-site-verbinding maken met behulp van Virtual WAN](virtual-wan-site-to-site-portal.md)
