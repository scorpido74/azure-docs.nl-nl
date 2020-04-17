---
title: 'Azure ExpressRoute: Over versleuteling'
description: Meer informatie over ExpressRoute-versleuteling.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: cherylmc
ms.openlocfilehash: d48f83df4e83fd1d1fe38eea76432e1b1c711b76
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461460"
---
# <a name="expressroute-encryption"></a>ExpressRoute-versleuteling
 
ExpressRoute ondersteunt een aantal versleutelingstechnologieën om de vertrouwelijkheid en integriteit van de gegevens die tussen uw netwerk en het netwerk van Microsoft worden doorkruisen, te waarborgen.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Point-to-point encryptie door MACsec FAQ
MACsec is een [IEEE-standaard.](https://1.ieee802.org/security/802-1ae/) Het versleutelt gegevens op het MAC-niveau (Media Access Control) of Network Layer 2. U MACsec gebruiken om de fysieke koppelingen tussen uw netwerkapparaten en de netwerkapparaten van Microsoft te versleutelen wanneer u verbinding maakt met Microsoft via [ExpressRoute Direct.](expressroute-erdirect-about.md) MACsec is standaard uitgeschakeld op ExpressRoute Direct-poorten. U brengt uw eigen MACsec-sleutel mee voor versleuteling en slaat deze op in [Azure Key Vault.](../key-vault/general/overview.md) U bepaalt zelf wanneer u de sleutel wilt draaien. Zie andere veelgestelde vragen hieronder.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Kan ik MACsec inschakelen op mijn ExpressRoute-circuit dat is ingericht door een ExpressRoute-provider?
Nee. MACsec versleutelt al het verkeer op een fysieke koppeling met een sleutel die eigendom is van één entiteit (d.w.z. de klant). Daarom is het alleen beschikbaar op ExpressRoute Direct.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Kan ik een aantal van de ExpressRoute-circuits op mijn ExpressRoute Direct-poorten versleutelen en andere circuits op dezelfde poorten onversleuteld laten? 
Nee. Zodra MACsec is ingeschakeld, worden al het netwerkbeheerverkeer, bijvoorbeeld het BGP-gegevensverkeer en het klantgegevensverkeer, versleuteld. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>Wanneer ik MACsec in- of uitschakelen of MACsec-sleutel bijwerken, verliest mijn on-premises netwerk dan de verbinding met Microsoft via ExpressRoute?
Ja. Voor de MACsec-configuratie ondersteunen we alleen de vooraf gedeelde sleutelmodus. Het betekent dat u de sleutel op zowel uw apparaten als op die van Microsoft moet bijwerken (via onze API). Deze wijziging is niet atoom, dus je verliest de verbinding als er een belangrijke mismatch tussen de twee zijden. We raden u ten zeerste aan een onderhoudsvenster in te plannen voor de configuratiewijziging. Om de downtime te minimaliseren, raden we u aan de configuratie op de ene link van ExpressRoute Direct op een moment bij te werken nadat u uw netwerkverkeer naar de andere koppeling hebt overgeschakeld.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>Blijft het verkeer stromen als er een mismatch is in de MACsec-sleutel tussen mijn apparaten en die van Microsoft?
Nee. Als MACsec is geconfigureerd en er een belangrijke mismatch optreedt, verliest u de verbinding met Microsoft. Met andere woorden, we zullen niet terugvallen naar een onversleutelde verbinding, waardoor uw gegevens worden blootgesteld. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Zal het inschakelen van MACsec op ExpressRoute Direct de prestaties van het netwerk verslechteren?
MACsec encryptie en decryptie vindt plaats in hardware op de routers die we gebruiken. Er is geen impact op de prestaties aan onze kant. U moet echter contact opnemen met de netwerkleverancier voor de apparaten die u gebruikt en kijken of MACsec enige prestatie-implicatie heeft.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>welke cipher suites worden ondersteund voor encryptie?
Wij ondersteunen AES128 en AES256.

## <a name="end-to-end-encryption-by-ipsec-faq"></a>End-to-end encryptie door IPsec FAQ
IPsec is een [IETF standaard](https://tools.ietf.org/html/rfc6071). Het versleutelt gegevens op ip-niveau (Internet Protocol) of Network Layer 3. U IPsec gebruiken om een end-to-end verbinding tussen uw on-premises netwerk en uw virtuele netwerk (VNET) op Azure te versleutelen. Zie andere veelgestelde vragen hieronder.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Kan ik IPsec inschakelen naast MACsec op mijn ExpressRoute Direct-poorten?
Ja. MACsec beveiligt de fysieke verbindingen tussen u en Microsoft. IPsec beveiligt de end-to-end verbinding tussen u en uw virtuele netwerken op Azure. U ze onafhankelijk inschakelen. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>Kan ik azure VPN-gateway gebruiken om de IPsec-tunnel in te stellen tussen mijn on-premises netwerk en mijn virtuele Azure-netwerk?
Ja. U deze IPsec-tunnel instellen boven Microsoft Peering van uw ExpressRoute-circuit. Volg onze [configuratiegids.](site-to-site-vpn-over-microsoft-peering.md)
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Kan ik azure VPN-gateway gebruiken om de IPsec-tunnel via Azure Private Peering in te stellen?
Als u Azure Virtual WAN adopteert, u [deze stappen](../virtual-wan/vpn-over-expressroute.md) volgen om de end-to-end-verbinding te versleutelen. Als u regelmatig Azure VNET hebt, u een VPN-gateway van derden implementeren in uw VNET en een IPsec-tunnel instellen tussen deze gateway en uw on-premises VPN-gateway.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Wat is de doorvoer die ik krijg na het inschakelen van IPsec op mijn ExpressRoute-verbinding?
Als Azure VPN-gateway wordt gebruikt, controleert u hier de [prestatienummers.](../vpn-gateway/vpn-gateway-about-vpngateways.md) Als een VPN-gateway van derden wordt gebruikt, neemt u contact op met de leverancier voor de prestatienummers.

## <a name="next-steps"></a>Volgende stappen
Zie [MACsec configureren](expressroute-howto-macsec.md) voor meer informatie over de MACsec-configuratie.

Zie [IPsec configureren](site-to-site-vpn-over-microsoft-peering.md) voor meer informatie over de IPsec-configuratie.
