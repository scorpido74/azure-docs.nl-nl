---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 608b148dc3929065df44530da65e695df19be03e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79485978"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Wordt BGP ondersteunt op alle Azure VPN-gateway SKU’s?
BGP wordt ondersteund op alle Azure VPN Gateawy Sku's behalve de basis-SKU.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Kan ik BGP gebruiken met beleid-gebaseerde VPN-gateways van Azure?
Nee, BGP wordt alleen ondersteund op route-gebaseerde VPN-gateways.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>Kan ik persoonlijke ASN's (autonome systeemnummers) gebruiken?
Ja, u kunt uw eigen openbare ASN's of persoonlijke ASN's voor zowel uw on-premises netwerken en virtuele netwerken van Azure gebruiken.

### <a name="can-i-use-32-bit-4-byte-asns-autonomous-system-numbers"></a>Kan ik 32-bits (4-bytes) Asn's (autonome systeem nummers) gebruiken?
Ja, Azure VPN-gateways ondersteunen nu 32-bits (4-bytes) Asn's. Gebruik Power shell/CLI/SDK om te configureren met behulp van ASN in decimale notatie.

### <a name="are-there-asns-reserved-by-azure"></a>Zijn er ASN's die zijn gereserveerd door Azure?
Ja, de volgende ASN's zijn gereserveerd door Azure voor zowel interne als externe koppelingen:

* Openbare ASN's: 8074, 8075, 12076
* Privé-ASNs: 65515, 65517, 65518, 65519, 65520

U kunt deze ASN's niet opgeven voor uw on-premises VPN-apparaten wanneer u verbinding maakt met Azure VPN-gateways.

### <a name="are-there-any-other-asns-that-i-cant-use"></a>Zijn er andere ASN's die ik niet kan gebruiken?
Ja. De volgende ASN's zijn [gereserveerd door IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) en kunnen niet worden geconfigureerd voor uw Azure VPN-gateway:

23456, 64496-64511, 65535-65551 en 429496729

### <a name="what-private-asns-can-i-use"></a>Welke persoonlijke Asn's kan ik gebruiken?
Het bruikbaarste bereik aan persoonlijke Asn's dat kan worden gebruikt, zijn:

* 64512-65514, 65521-65534

Deze Asn's zijn niet gereserveerd door IANA of Azure voor gebruik en kunnen daarom worden gebruikt voor het toewijzen van uw Azure-VPN Gateway.

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Kan ik hetzelfde ASN gebruiken voor on-premises VPN-netwerken en Azure VNets?
Nee, u moet verschillende ASN’s toewijzen aan uw on-premises netwerken en uw Azure Vnets als u ze beide verbindt met BGP. Aan Azure VPN-gateways wordt standaard een ASN van 65515 toegewezen, onafhankelijk van of BGP is ingeschakeld voor verbinding tussen gebouwen. U kunt deze standaardwaarde onderdrukken door een andere ASN toe te wijzen bij het aanmaken van de VPN-gateway of door het ASN te wijzigen nadat de gateway is aangemaakt. U moet uw lokale ASN's toewijzen aan de bijbehorende on-premises netwerkgateways van Azure.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Welke adresvoorvoegsels maakt Azure VPN-gateways aan mij bekend?
Azure VPN-gateway maakt de volgende routes bekend aan uw on-premises BGP-apparaten:

* Uw VNet-adresvoorvoegsels
* Adresvoorvoegsels voor alle lokale netwerkgateways die zijn verbonden met de Azure VPN-gateway
* Routes die afkomstig zijn van andere BGP-peeringsessies die zijn verbonden met de Azure VPN-gateway, **behalve standaardroutes of routes die overlappen met een VNet-voorvoegsel**.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Hoeveel voor voegsels kan ik adverteren voor Azure VPN-gateway?
We ondersteunen Maxi maal 4000 voor voegsels. De BGP-sessie wordt verwijderd als het aantal voorvoegsels de limiet overschrijdt.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Kan ik standaardroute (0.0.0.0/0) adverteren naar Azure VPN-gateways?
Ja.

Houd er rekening mee dat dit al het uitgaande verkeer van het VNet naar uw on-premises site stuurt en voorkomt dat de virtuele machines van het VNet openbare communicatie accepteren die rechtstreeks van internet afkomstig is, zoals RDP of SSH van internet naar de virtuele machines.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Kan ik de exacte voorvoegsels als de voorvoegsels van mijn virtuele netwerk adverteren?

Nee, het adverteren van dezelfde voorvoegsels als een van de adresvoorvoegsels van uw virtuele netwerk wordt door het Azure-platform geblokkeerd of gefilterd. U kunt echter een voorvoegsel aankondigen dat een superset is van wat u in Virtual Network hebt. 

Als uw virtueel netwerk bijvoorbeeld adresruimte 10.0.0.0/16 gebruikt, kunt u 10.0.0.0/8 adverteren. U kunt echter niet 10.0.0.0/16 of 10.0.0.0/24 adverteren.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Kan ik BGP in combinatie met mijn VNet-naar-VNet-verbindingen gebruiken?
Ja, u kunt BGP zowel gebruiken voor verbindingen tussen gebouwen als voor VNet-naar-VNet-verbindingen.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Kan ik BGP combineren met niet-BGP-verbindingen voor mijn Azure VPN-gateways?
Ja, u kunt zowel BGP- als niet-BGP-verbindingen combineren voor dezelfde VPN-gateway.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Biedt Azure VPN-gateway ondersteuning voor BGP-transitroutering?
Ja, BGP-transitroutering wordt ondersteund, met uitzondering dat Azure VPN-gateways **GEEN** standaardroutes bekendmaakt aan andere BGP-peers. Om transitroutering op meerdere VPN-gateways mogelijk te maken, moet u BGP op alle tussenliggende VNet-naar-VNet-verbindingen inschakelen. Zie [over BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)voor meer informatie.

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Kan ik meer dan één tunnel aanbrengen tussen een Azure VPN-gateway en mijn on-premises netwerk?
Ja, u kunt meer dan één S2S-VPN-tunnel aanbrengen tussen een Azure VPN-gateway en uw on-premises netwerk. Houd er wel rekening mee dat deze tunnels meetellen voor het totaal aantal tunnels voor uw Azure VPN-gateways, en dat u BGP op beide tunnels moet inschakelen.

Als u bijvoorbeeld twee redundante tunnels heeft tussen uw Azure VPN-gateway en een van uw on-premises netwerken, gebruiken ze 2 tunnels van het totaalaantal voor uw Azure VPN-gateway (10 voor Standard en 30 voor HighPerformance).

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Kan ik meerdere tunnels tussen twee Azure VNets met BGP hebben?
Ja, maar minimaal één van de gateways voor het virtuele netwerk moet de actief/actief-configuratie hebben.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>Kan ik BGP gebruiken voor S2S-VPN in een configuratie waarin zowel ExpressRoute als S2S-VPN wordt gebruikt?
Ja. 

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Welk adres gebruikt Azure VPN-gateway voor BGP-peer-IP?
De Azure VPN-gateway wijst één IP-adres toe uit het GatewaySubnet-bereik voor VPN-gateways voor actieve stand-by, of twee IP-adressen voor actieve-actief VPN-gateways. U kunt het werkelijke BGP-IP-adres (sen) ophalen dat is toegewezen met behulp van Power shell (Get-AzVirtualNetworkGateway, zoeken naar de eigenschap ' bgpPeeringAddress ') of in de Azure Portal (onder de eigenschap BGP ASN configureren op de pagina configuratie van de gateway).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Wat zijn de vereisten voor de BGP-peer-IP-adressen op mijn VPN-apparaat?
Het adres van uw on-premises BGP-peer **mag niet** hetzelfde zijn als het open bare IP-adres van uw VPN-apparaat of de Vnet-adres ruimte van de VPN gateway. Gebruik een ander IP-adres op het VPN-apparaat voor uw BGP-peer-IP. Dit mag een adres zijn dat is toegewezen aan de loopback-interface op het apparaat, maar het mag geen APIPA-adres (169.254.x.x) zijn. Specificeer dit adres in de bijbehorende lokale netwerkgateway die de locatie vertegenwoordigt.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Wat moet ik opgeven als adresvoorvoegsels voor mijn lokale netwerkgateway wanneer ik BGP gebruik?
Azure Local Network Gateway geeft u de eerste adresvoorvoegsels voor het on-premises netwerk. Bij BGP moet u het voorvoegsel van de host (/32-voorvoegsel) van uw BGP-peer-IP-adres toewijzen als de adresruimte voor dat on-premises netwerk. Als uw BGP-peer-IP-adres 10.52.255.254 is, moet u "10.52.255.254/32" opgeven als de lokale netwerkadresruimte van de lokale netwerkgateway voor dit on-premises netwerk. Hierdoor bent u ervan verzekerd dat de Azure VPN-gateway de BGP-sessie opricht via de S2S-VPN-tunnel.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Wat moet ik toevoegen aan mijn on-premises VPN-apparaat voor de BGP-peeringsessie?
U moet een hostroute van het Azure BGP-peer-IP-adres toevoegen aan uw VPN-apparaat die verwijst naar de IPSec-S2S VPN-tunnel. Als het Azure VPN-peer-IP-adres bijvoorbeeld "10.12.255.30" is, dient u een hostroute toe te voegen voor "10.12.255.30" met een nexthop-interface van de overeenkomende IPsec-tunnelinterface op uw VPN-apparaat.
