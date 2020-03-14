---
title: Overzicht van Azure Virtual WAN | Microsoft Docs
description: Meer informatie over de geautomatiseerde schaal bare vertakking-to-Branch connectiviteit van Virtual WAN, beschik bare regio's en partners.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 9ac70252ce7c818ccbdecfd996b9970f011aa967
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241414"
---
# <a name="about-azure-virtual-wan"></a>Over Azure Virtual WAN

Azure Virtual WAN is een netwerk service die zorgt voor geoptimaliseerde en geautomatiseerde vertakkings connectiviteit met, en via Azure. Azure-regio's fungeren als hubs die u kunt gebruiken om uw filialen te koppelen aan. U kunt de Azure-backbone gebruiken om ook vertakkingen te verbinden en te profiteren van de connectiviteit van Branch-to-VNet. Er is een lijst met partners die connectiviteits automatisering ondersteunen met Azure Virtual WAN VPN. Zie het artikel [virtuele WAN-partners en locaties](virtual-wan-locations-partners.md) voor meer informatie.

Met Azure Virtual WAN worden veel Azure-Cloud verbindings Services, zoals site-naar-site-VPN, gebruikers-VPN (punt-naar-site), en ExpressRoute naar één operationele interface gecombineerd. Connectiviteit met Azure VNets wordt tot stand gebracht met behulp van virtuele netwerk verbindingen. Het schakelt [wereld wijde doorvoer netwerk architectuur](virtual-wan-global-transit-network-architecture.md) in op basis van een klassiek hub-en-spoke-verbindings model, waarbij de hub van het gehoste netwerk van de Cloud transitieve connectiviteit mogelijk maakt tussen eind punten die kunnen worden gedistribueerd in verschillende typen spokes.

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan1.png)

In dit artikel vindt u een kort overzicht van de netwerk verbinding in azure Virtual WAN. Virtual WAN biedt de volgende voordelen:

* **Geïntegreerde connectiviteits oplossingen in hub en spoke:** Automatiseer site-naar-site-configuratie en connectiviteit tussen on-premises sites en een Azure-hub.
* **Geautomatiseerde installatie en configuratie van spokes:** zorg voor een naadloze verbinding van uw virtuele netwerken en workloads met de Azure-hub.
* **Intuïtieve problemen oplossen:** U kunt de end-to-end-stroom in azure bekijken en vervolgens deze informatie gebruiken om de vereiste acties uit te voeren.

## <a name="basicstandard"></a>Basis en standaard virtuele Wan's

Er zijn twee typen virtuele Wan's: Basic en Standard. De volgende tabel bevat de beschik bare configuraties voor elk type.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Zie [een virtuele WAN upgraden van Basic naar Standard](upgrade-virtual-wan.md)voor stappen om een virtueel WAN bij te werken.

## <a name="architecture"></a>Opstelling

Raadpleeg de volgende artikelen voor meer informatie over de Virtual WAN-architectuur en het migreren naar Virtual WAN:

* [Virtuele WAN-architectuur](migrate-from-hub-spoke-topology.md)
* [Wereld wijde doorvoer netwerk architectuur](virtual-wan-global-transit-network-architecture.md)

## <a name="resources"></a>Virtual WAN-resources

Als u een end-to-end virtuele WAN wilt configureren, maakt u de volgende resources:

* **virtualWAN:** de virtualWAN-resource staat voor een virtuele overlay van uw Azure-netwerk en is een verzameling van meerdere resources. Het bevat koppelingen naar al uw virtuele hubs die u wilt opnemen in het virtuele WAN. Virtual WAN-resources zijn van elkaar geïsoleerd en kunnen geen gemeenschappelijke hub bevatten. Virtuele hubs in een virtueel WAN communiceren niet met elkaar.

* **Hub:** een virtuele hub is een virtueel netwerk dat door Microsoft wordt beheerd. De hub bevat verschillende service-eind punten om connectiviteit mogelijk te maken. Vanuit uw on-premises netwerk (vpnsite) kunt u verbinding maken met een VPN Gateway binnen de virtuele hub, ExpressRoute-circuits verbinden met een virtuele hub, of zelfs mobiele gebruikers verbinden met een punt-naar-site-gateway in de virtuele hub. De hub is de kern van uw netwerk in een regio. Een Azure-regio mag maar één hub bevatten.

  Een hubgateway is niet hetzelfde als een gateway voor het virtuele netwerk die u voor ExpressRoute en VPN Gateway gebruikt. Wanneer u bijvoorbeeld virtueel WAN gebruikt, maakt u geen site-naar-site-verbinding vanaf uw on-premises site rechtstreeks naar uw VNet. In plaats daarvan maakt u een site-naar-site-verbinding met de hub. Het verkeer verloopt altijd via de hubgateway. Dit houdt in dat uw VNet's hun eigen gateway voor het virtuele netwerk niet nodig hebben. In Virtual WAN kunnen uw VNet's eenvoudig schalen via de virtuele hub en de gateway van de virtuele hub.

* **Hub-verbinding met het virtuele netwerk:** de resource van de hub-verbinding met het virtuele netwerk wordt gebruikt om de hub naadloos met het virtuele netwerk te verbinden.

* **(Preview) hub-naar-hub-verbinding** : hubs zijn allemaal verbonden met elkaar in een virtueel WAN. Dit betekent dat een vertakking, gebruiker of VNet dat is verbonden met een lokale hub kan communiceren met een andere vertakking of VNet met behulp van de volledige mesh-architectuur van de verbonden hubs. U kunt VNets ook verbinden binnen een hub die via de virtuele hub wordt door lopen, evenals VNets op de hele hub, met behulp van het hub-to-hub Connected Framework.

* **De tabel met de hubroute:** u kunt een virtuele-hubroute maken en de route toepassen op de routetabel van de virtuele hub. U kunt meerdere routes toepassen op de routetabel van de virtuele hub.

**Aanvullende virtuele WAN-resources**

  * **Site:** Deze bron wordt alleen gebruikt voor site-naar-site-verbindingen. De site resource is **vpnsite**. Het staat voor uw on-premises VPN-apparaat en de bijbehorende instellingen. Wanneer u met een Virtual WAN-partner werkt, beschikt u over een geïntegreerde oplossing die deze gegevens automatisch naar Azure exporteert.

## <a name="connectivity"></a>Typen connectiviteit

Met Virtual WAN kunt u de volgende typen connectiviteit: site-naar-site-VPN, gebruikers-VPN (punt-naar-site) en ExpressRoute.

### <a name="s2s"></a>Site-naar-site-VPN-verbindingen

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan.png)

Wanneer u een virtuele WAN-site-naar-site-verbinding maakt, kunt u werken met een beschik bare partner. Als u geen partner wilt gebruiken, kunt u de verbinding hand matig configureren. Zie [een site-naar-site-verbinding maken met behulp van Virtual WAN](virtual-wan-site-to-site-portal.md)voor meer informatie.

#### <a name="s2spartner"></a>Virtuele WAN-partner werk stroom

Wanneer u met een virtuele WAN-partner werkt, is de werk stroom:

1. De controller (VPN/SD-WAN) voor filiaalapparaten wordt geverifieerd voor het exporteren van locatiegegevens naar Azure met behulp van een [Azure-service-principal](../active-directory/develop/howto-create-service-principal-portal.md).
2. De controller (VPN/SD-WAN) voor filiaalapparaten ontvangt de Azure-verbindingsconfiguratie en werkt het lokale apparaat bij. Op deze manier wordt het downloaden van de configuratie, het bewerken en het bijwerken van het on-premises VPN-apparaat geautomatiseerd.
3. Wanneer het apparaat eenmaal de juiste Azure-configuratie heeft, wordt een verbinding tussen de locaties (twee actieve tunnels) en Azure WAN tot stand gebracht. Azure ondersteunt zowel IKEv1 als IKEv2. BGP is optioneel.

#### <a name="partners"></a>Partners voor site-naar-site virtuele WAN-verbindingen

Zie het artikel [virtuele WAN-partners en locaties](virtual-wan-locations-partners.md) voor een lijst met de beschik bare partners en locaties.

### <a name="uservpn"></a>VPN-verbindingen (punt-naar-site)

U kunt verbinding maken met uw resources in azure via een IPsec/IKE-of OpenVPN-verbinding. Voor dit type verbinding moet een VPN-client worden geconfigureerd op de client computer. Zie [een punt-naar-site-verbinding maken](virtual-wan-point-to-site-portal.md)voor meer informatie.

### <a name="er"></a>ExpressRoute-verbindingen
Met ExpressRoute kunt u een on-premises netwerk verbinden met Azure via een particuliere verbinding. Zie [een ExpressRoute-verbinding maken met behulp van Virtual WAN](virtual-wan-expressroute-portal.md)als u de verbinding wilt maken.

## <a name="locations"></a>Maplocaties

Zie het artikel [virtuele WAN-partners en locaties](virtual-wan-locations-partners.md) voor informatie over de locatie.

## <a name="faq"></a>Veelgestelde vragen

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Volgende stappen

[Een site-naar-site-verbinding maken met behulp van Virtual WAN](virtual-wan-site-to-site-portal.md)
