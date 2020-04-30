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
ms.openlocfilehash: 927c09f61ce0847c72cefb51935116070e956861
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80743099"
---
# <a name="about-azure-virtual-wan"></a>Over Azure Virtual WAN

Azure Virtual WAN is een netwerk service die veel netwerk-, beveiligings-en routerings functionaliteit biedt om één operationele interface te bieden. Deze functies omvatten vertakkings connectiviteit (via connectiviteits automatisering van virtuele WAN-partner apparaten zoals SD-WAN of VPN CPE), site-naar-site-VPN-verbinding, VPN voor externe gebruikers (punt-naar-site) connectiviteit, persoonlijke (ExpressRoute) connectiviteit, intra-Cloud connectiviteit (transitieve connectiviteit voor virtuele netwerken), VPN ExpressRoute interconnectiviteit, route ring, Azure firewall U hoeft niet al deze gebruiks voorbeelden te hebben om virtueel WAN te gaan gebruiken. U kunt gewoon aan de slag met slechts één use-case en uw netwerk aanpassen tijdens het ontwikkelen. De virtuele WAN-architectuur is een hub-en spoke-architectuur met ingebouwde schaal-en prestatie voorzieningen voor vertakkingen (VPN-of SD-WAN-apparaten), gebruikers (Azure VPN/OpenVPN/IKEv2-clients), ExpressRoute-circuits en virtuele netwerken. Hiermee schakelt u de [wereld wijde doorvoer netwerk architectuur](virtual-wan-global-transit-network-architecture.md) in, waarbij de hub van het gehoste netwerk van de Cloud transitieve connectiviteit mogelijk maakt tussen eind punten die kunnen worden gedistribueerd in verschillende typen spokes.

Azure-regio's fungeren als hubs waarmee u verbinding kunt maken. Alle hubs zijn verbonden met een volledig net in een standaard virtuele WAN, waardoor de gebruiker de micro soft-backbone eenvoudig kan gebruiken voor een wille keurige verbinding (wille keurige spoke). Gebruikers kunnen een spoke-verbinding met een SD-WAN/VPN-apparaat gebruiken door deze hand matig in te stellen in azure Virtual WAN of door gebruik te maken van de virtuele WAN CPE-partner oplossing (SD-WAN/VPN) voor het instellen van de verbinding met Azure. Er is een lijst met partners die connectiviteits automatisering ondersteunen (de mogelijkheid om de apparaatgegevens te exporteren naar Azure, de Azure-configuratie te downloaden en connectiviteit te maken) met Azure Virtual WAN. Zie het artikel [virtuele WAN-partners en locaties](virtual-wan-locations-partners.md) voor meer informatie. 

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan1.png)

In dit artikel vindt u een kort overzicht van de netwerk verbinding in azure Virtual WAN. Virtual WAN biedt de volgende voordelen:

* **Geïntegreerde connectiviteits oplossingen in hub en spoke:** Automatiseer site-naar-site-configuratie en connectiviteit tussen on-premises sites en een Azure-hub.
* **Geautomatiseerde installatie en configuratie van spokes:** zorg voor een naadloze verbinding van uw virtuele netwerken en workloads met de Azure-hub.
* **Intuïtieve problemen oplossen:** U kunt de end-to-end-stroom in azure bekijken en vervolgens deze informatie gebruiken om de vereiste acties uit te voeren.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Basis en standaard virtuele Wan's

Er zijn twee typen virtuele Wan's: Basic en Standard. De volgende tabel bevat de beschik bare configuraties voor elk type.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Zie [een virtuele WAN upgraden van Basic naar Standard](upgrade-virtual-wan.md)voor stappen om een virtueel WAN bij te werken.

## <a name="architecture"></a><a name="architecture"></a>Architectuur

Raadpleeg de volgende artikelen voor meer informatie over de Virtual WAN-architectuur en het migreren naar Virtual WAN:

* [Virtuele WAN-architectuur](migrate-from-hub-spoke-topology.md)
* [Wereldwijde architectuur voor doorvoernetwerk](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Virtual WAN-resources

Als u een end-to-end virtuele WAN wilt configureren, maakt u de volgende resources:

* **virtualWAN:** de virtualWAN-resource staat voor een virtuele overlay van uw Azure-netwerk en is een verzameling van meerdere resources. Het bevat koppelingen naar al uw virtuele hubs die u wilt opnemen in het virtuele WAN. Virtual WAN-resources zijn van elkaar geïsoleerd en kunnen geen gemeenschappelijke hub bevatten. Virtuele hubs in een virtueel WAN communiceren niet met elkaar.

* **Hub:** een virtuele hub is een virtueel netwerk dat door Microsoft wordt beheerd. De hub bevat verschillende service-eind punten om connectiviteit mogelijk te maken. Vanuit uw on-premises netwerk (vpnsite) kunt u verbinding maken met een VPN Gateway binnen de virtuele hub, ExpressRoute-circuits verbinden met een virtuele hub, of zelfs mobiele gebruikers verbinden met een punt-naar-site-gateway in de virtuele hub. De hub is de kern van uw netwerk in een regio. Een Azure-regio mag maar één hub bevatten.

  Een hubgateway is niet hetzelfde als een gateway voor het virtuele netwerk die u voor ExpressRoute en VPN Gateway gebruikt. Wanneer u bijvoorbeeld virtueel WAN gebruikt, maakt u geen site-naar-site-verbinding vanaf uw on-premises site rechtstreeks naar uw VNet. In plaats daarvan maakt u een site-naar-site-verbinding met de hub. Het verkeer verloopt altijd via de hubgateway. Dit houdt in dat uw VNet's hun eigen gateway voor het virtuele netwerk niet nodig hebben. In Virtual WAN kunnen uw VNet's eenvoudig schalen via de virtuele hub en de gateway van de virtuele hub.

* **Hub-verbinding met het virtuele netwerk:** de resource van de hub-verbinding met het virtuele netwerk wordt gebruikt om de hub naadloos met het virtuele netwerk te verbinden.

* **(Preview) hub-naar-hub-verbinding** : hubs zijn allemaal verbonden met elkaar in een virtueel WAN. Dit betekent dat een vertakking, gebruiker of VNet dat is verbonden met een lokale hub kan communiceren met een andere vertakking of VNet met behulp van de volledige mesh-architectuur van de verbonden hubs. U kunt VNets ook verbinden binnen een hub die via de virtuele hub wordt door lopen, evenals VNets op de hele hub, met behulp van het hub-to-hub Connected Framework.

* **De tabel met de hubroute:** u kunt een virtuele-hubroute maken en de route toepassen op de routetabel van de virtuele hub. U kunt meerdere routes toepassen op de routetabel van de virtuele hub.

**Aanvullende virtuele WAN-resources**

  * **Site:** Deze bron wordt alleen gebruikt voor site-naar-site-verbindingen. De site resource is **vpnsite**. Het staat voor uw on-premises VPN-apparaat en de bijbehorende instellingen. Wanneer u met een Virtual WAN-partner werkt, beschikt u over een geïntegreerde oplossing die deze gegevens automatisch naar Azure exporteert.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Typen connectiviteit

Met Virtual WAN kunt u de volgende typen connectiviteit: site-naar-site-VPN, gebruikers-VPN (punt-naar-site) en ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Site-naar-site VPN-verbindingen

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan.png)

Wanneer u een virtuele WAN-site-naar-site-verbinding maakt, kunt u werken met een beschik bare partner. Als u geen partner wilt gebruiken, kunt u de verbinding hand matig configureren. Zie [een site-naar-site-verbinding maken met behulp van Virtual WAN](virtual-wan-site-to-site-portal.md)voor meer informatie.

#### <a name="virtual-wan-partner-workflow"></a><a name="s2spartner"></a>Virtuele WAN-partner werk stroom

Wanneer u met een virtuele WAN-partner werkt, is de werk stroom:

1. De controller van het vertakkings apparaat (VPN/SDWAN) wordt geverifieerd om site-georiënteerde informatie te exporteren naar Azure met behulp van een [Azure-Service-Principal](../active-directory/develop/howto-create-service-principal-portal.md).
2. De controller (VPN/SD-WAN) voor filiaalapparaten ontvangt de Azure-verbindingsconfiguratie en werkt het lokale apparaat bij. Op deze manier wordt het downloaden van de configuratie, het bewerken en het bijwerken van het on-premises VPN-apparaat geautomatiseerd.
3. Wanneer het apparaat eenmaal de juiste Azure-configuratie heeft, wordt een verbinding tussen de locaties (twee actieve tunnels) en Azure WAN tot stand gebracht. Azure ondersteunt zowel IKEv1 als IKEv2. BGP is optioneel.

#### <a name="partners-for-site-to-site-virtual-wan-connections"></a><a name="partners"></a>Partners voor site-naar-site virtuele WAN-verbindingen

Zie het artikel [virtuele WAN-partners en locaties](virtual-wan-locations-partners.md) voor een lijst met de beschik bare partners en locaties.

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>VPN-verbindingen (punt-naar-site)

U kunt verbinding maken met uw resources in azure via een IPsec/IKE-of OpenVPN-verbinding. Voor dit type verbinding moet een VPN-client worden geconfigureerd op de client computer. Zie [een punt-naar-site-verbinding maken](virtual-wan-point-to-site-portal.md)voor meer informatie.

### <a name="expressroute-connections"></a><a name="er"></a>ExpressRoute-verbindingen
Met ExpressRoute kunt u een on-premises netwerk verbinden met Azure via een particuliere verbinding. Zie [een ExpressRoute-verbinding maken met behulp van Virtual WAN](virtual-wan-expressroute-portal.md)als u de verbinding wilt maken.

## <a name="locations"></a><a name="locations"></a>Locaties

Zie het artikel [virtuele WAN-partners en locaties](virtual-wan-locations-partners.md) voor informatie over de locatie.

## <a name="faq"></a><a name="faq"></a>Veelgestelde vragen

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Volgende stappen

[Een site-naar-site-verbinding maken met behulp van Virtual WAN](virtual-wan-site-to-site-portal.md)
