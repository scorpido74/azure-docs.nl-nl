---
title: Interconnect met China met Azure Virtual WAN en secure Hub
description: Meer informatie over virtuele WAN-geautomatiseerde schaalbare branch-to-branch-connectiviteit, beschikbare regio's en partners.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: e05aa6e8f656001eb9eb2a3717dde6c16f030e33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337154"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Interconnect met China met Azure Virtual WAN en Secure Hub

Wanneer we kijken naar gemeenschappelijke auto's, productie, logistieke industrieën of andere instituten zoals ambassades, is er vaak de vraag over hoe de interconnectie met China te verbeteren. Deze verbeteringen zijn meestal relevant voor het gebruik van Cloud Services zoals Office 365, Azure Global Services of interconnect-branches in China met een klantbackbone.

In de meeste gevallen worstelen klanten met hoge latencies, lage bandbreedte, instabiele verbinding en hoge kosten die verbinding maken met buiten China (bijvoorbeeld Europa of de Verenigde Staten).

Een reden voor deze strijd is de "Grote Firewall van China", die het Chinese deel van Internet beschermt en verkeer aan China filtert. Bijna al verkeer dat van Het Vasteland van China aan buiten China loopt, behalve de speciale beleidsstreken zoals Hong Kong en Macau, gaat de Grote Firewall over. Het verkeer dat door Hong Kong en Macau loopt raakt niet de Grote Firewall in volle kracht, het wordt behandeld door een subset van de Great Firewall.

![Provider interconnect](./media/interconnect-china/provider.png)

Met Behulp van Virtual WAN kan een klant een performanterte en stabielere verbinding met Microsoft Cloud Services en een verbinding met hun bedrijfsnetwerk tot stand brengen zonder de Chinese cyberbeveiligingswet te overtreden.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Vereisten en werkstroom

Als u wilt blijven voldoen aan de Chinese cybersecurity wet, moet u voldoen aan een reeks van bepaalde voorwaarden.

Eerst moet u samenwerken met een netwerk en ISP die eigenaar is van een ICP (Internet Content Provider) licentie voor China. In de meeste gevallen kom je uit bij een van de volgende providers:

* China Telecom Global Ltd.
* China Mobile Ltd.
* China Unicom Ltd.
* PCCW Global Ltd.
* Hong Kong Telecom Ltd.

Afhankelijk van de provider en uw behoeften, moet u nu een van de volgende netwerkconnectiviteitsservices aanschaffen om uw vestigingen binnen China met elkaar te verbinden.

* Een MPLS/IPVPN-netwerk 
* Een software defined WAN (SDWAN)
* Speciale internettoegang

Vervolgens moet u het eens worden met die provider om een uitbraak te geven aan het Microsoft Global Network en zijn Edge Network in Hong Kong, niet in Beijing of Shanghai. In dit geval is Hongkong erg belangrijk vanwege de fysieke verbinding en locatie met China.

Terwijl de meeste klanten denken dat het gebruik van Singapore voor interconnect is het beste geval, omdat het lijkt dichter bij China bij het kijken op de kaart, dit is niet waar. Wanneer u netwerkvezelkaarten volgt, gaan bijna alle netwerkverbindingen door Peking, Shanghai, en Hong Kong. Dit maakt Hong Kong een betere locatie keuze om verbinding te maken met China.

Afhankelijk van de provider u verschillende serviceaanbiedingen krijgen. De onderstaande tabel toont een voorbeeld van aanbieders en de service die zij aanbieden, op basis van informatie op het moment dat dit artikel werd geschreven.

| Service | Voorbeelden van leveranciers |
| --- | --- |
| MPLS/IPVPN-netwerk |PCCW, China Telecom Global |
|SDWAN (SDWAN)| PCCW, China Telecom Global|
| Speciale internettoegang | PCCW, Hong Kong Telecom, China Mobil, PCCW |

Met uw provider u overeenkomen welke van de volgende twee oplossingen u moet gebruiken om de wereldwijde backbone van Microsoft te bereiken:

* Een Microsoft Azure ExpressRoute laten beëindigen in Hong Kong. Dat zou het geval zijn voor het gebruik van MPLS/IPVPN. Momenteel is alleen de enige ICP-licentieprovider met ExpressRoute naar Hong Kong China Telecom Global. Ze kunnen echter ook met de andere providers praten als ze gebruikmaken van Cloud Exchange Providers zoals Megaport of InterCloud. Zie [ExpressRoute-connectiviteitsproviders](../expressroute/expressroute-locations-providers.md#partners)voor meer informatie.

* Een dedicated internettoegang rechtstreeks gebruiken op een van de volgende Internet Exchange-punten of via een verbinding met een privénetwerk.

De volgende lijst toont Internet Exchanges mogelijk in Hong Kong:

* AMS-IX Hong Kong
* BBIX Hong Kong
* Equinix Hongkong
* HKIX HKIX

Wanneer u deze verbinding gebruikt, moet uw volgende BGP-hop voor Microsoft Services Microsoft Autonomous System Number (AS#) 8075 zijn. Als u één locatie of SDWAN-oplossing gebruikt, is dat de keuze van de verbinding.

Hoe dan ook, we raden je nog steeds aan om een tweede en regelmatige Internet Breakout naar het Chinese vasteland te brengen. Dit is om het verkeer te verdelen tussen bedrijfsverkeer naar cloudservices zoals Microsoft 365 en Azure, en door de wet geregeld internetverkeer.

Een compatibele netwerkarchitectuur binnen China kan er als volgt uitzien:

![Meerdere branches](./media/interconnect-china/multi-branch.png)

In dit voorbeeld, met een interconnect met het Microsoft Global Network in Hong Kong, u nu beginnen met het gebruik van de [Azure Virtual WAN Global Transit Architecture](virtual-wan-global-transit-network-architecture.md) en aanvullende services, zoals Azure secure Virtual WAN hub, om services te gebruiken en verbinding te maken met uw vestigingen en datacenter buiten China.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Hub-to-hub communicatie

In deze sectie gebruiken we virtual WAN hub-to-hub-communicatie om met elkaar te verbinden. In dit scenario maakt u een nieuwe Virtual WAN-hubbron om verbinding te maken met een virtuele WAN-hub in Hongkong, andere regio's die u verkiest, een regio waar u al Azure-bronnen hebt of waar u verbinding wilt maken.

Een voorbeeldarchitectuur kan er uitzien als het volgende voorbeeld:

![Voorbeeld VAN WAN](./media/interconnect-china/sample.png)

In dit voorbeeld maken de Chinese branches verbinding met Azure Cloud China en met elkaar via VPN- of MPLS-verbindingen. Branches die verbonden moeten zijn met Global Services maken gebruik van MPLS of internetservices die rechtstreeks met Hongkong zijn verbonden. Als u ExpressRoute in Hong Kong en in de andere regio wilt gebruiken, moet u [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) configureren om beide ExpressRoute-circuits met elkaar te verbinden.

ExpressRoute Global Reach is in sommige regio's niet beschikbaar. Als u bijvoorbeeld verbinding moet maken met Brazilië of India, moet u [cloudexchangeproviders](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) gebruiken om de routeringsservices aan te bieden.

De onderstaande afbeelding toont beide voorbeelden voor dit scenario.

![Globaal bereik](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Secure Internet breakout for Office 365 Secure Internet breakout for Office 365 Secure Internet breakout for Office 365 Secure Internet

Een andere overweging is netwerkbeveiliging en logging voor het ingangspunt tussen China en de virtuele WAN gevestigde backbone component, en de klant backbone. In de meeste gevallen is er een noodzaak om breakout naar het internet in Hong Kong om direct te bereiken van de Microsoft Edge Network en, met dat, de Azure Front Door Servers gebruikt voor Microsoft 365 Services.

Voor beide scenario's met Virtual WAN maakt u gebruik van de [Azure Virtual WAN secured hub.](../firewall-manager/secured-virtual-hub.md) Met Azure Firewall Manager u een gewone Virtuele WAN-hub wijzigen in een beveiligde hub en vervolgens een Azure Firewall implementeren en beheren binnen die hub.

De volgende figuur geeft een voorbeeld van dit scenario weer:

![Internetbreakout voor web- en Microsoft-servicesverkeer](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Architectuur en verkeersstromen

Afhankelijk van uw keuze met betrekking tot de verbinding met Hong Kong, kan de algehele architectuur enigszins veranderen. Deze sectie toont drie beschikbare architecturen in verschillende combinatie met VPN of SDWAN en/of ExpressRoute.

Al deze opties maken gebruik van Azure Virtual WAN secured hub voor directe M365-connectiviteit in Hong Kong. Deze architecturen ondersteunen ook de nalevingsvereisten voor [Office 365 Multi-Geo](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo) en houden dat verkeer in de buurt van de volgende Office 365-frontdoorlocatie. Als gevolg daarvan is het ook een verbetering voor het gebruik van Microsoft 365 uit China.

Wanneer u Azure Virtual WAN samen met internetverbindingen gebruikt, kan elke verbinding profiteren van aanvullende services zoals [Microsoft Azure Peering Services (MAPS).](https://docs.microsoft.com/azure/peering-service/about) MAPS is gebouwd om het verkeer dat naar het Microsoft Global Network komt van internetserviceproviders van derden te optimaliseren.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Optie 1: SDWAN of VPN

In deze sectie wordt een ontwerp besproken dat SDWAN of VPN gebruikt voor Hong Kong en naar andere branches. Deze optie toont het gebruik en de verkeersstroom bij het gebruik van een zuivere internetverbinding op beide sites van de Virtuele WAN-backbone. In dit geval wordt de verbinding naar Hong Kong gebracht met behulp van speciale internettoegang of een ICP-provider SDWAN-oplossing. Andere branches maken ook gebruik van puur internet of SDWAN Solutions.

![China naar Hongkong verkeer](./media/interconnect-china/china-traffic.png)

In deze architectuur is elke site verbonden met het Microsoft Global Network met behulp van VPN en Azure Virtual WAN. Het verkeer tussen de sites en Hong Kong wordt verzonden via het Microsoft Network en maakt alleen gebruik van regelmatige internetverbinding op de laatste mijl.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Optie 2: ExpressRoute en SDWAN of VPN

In deze sectie wordt een ontwerp besproken dat ExpressRoute in Hong Kong en andere branches gebruikt met VPN/SDWAN-vestigingen. Deze optie toont het gebruik van en ExpressRoute beëindigd in Hong Kong en andere takken verbonden via SDWAN of VPN. ExpressRoute in Hong Kong is momenteel beperkt tot een korte lijst van providers, die u vinden in de lijst van [Express Route Partners.](../expressroute/expressroute-locations-providers.md#global-commercial-azure)

![China naar Hong Kong verkeer ExpressRoute](./media/interconnect-china/expressroute.png)

Er zijn ook opties om ExpressRoute uit China te beëindigen, bijvoorbeeld in Zuid-Korea of Japan. Maar gezien de naleving, regelgeving en latentie is Hongkong momenteel de beste keuze.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Optie 3: Alleen ExpressRoute

Deze sectie bespreekt een ontwerp dat waar ExpressRoute wordt gebruikt voor Hong Kong en andere takken. Deze optie toont de interconnect met ExpressRoute aan beide uiteinden. Hier heb je een andere verkeersstroom dan de andere. Het Microsoft 365-verkeer stroomt naar de virtuele WAN beveiligde hub van Azure en van daaruit naar het Microsoft Edge Network en het internet.

Het verkeer dat naar de onderling verbonden vestigingen gaat of van hen naar de locaties in China zal een andere aanpak volgen binnen die architectuur. Momenteel ondersteunt virtueel WAN geen ExpressRoute naar ExpressRoute-transit. Het verkeer maakt gebruik van ExpressRoute Global Reach of de 3rd Party interconnect zonder de virtuele WAN Hub te passeren. Het zal rechtstreeks van de ene Microsoft Enterprise Edge (MSEE) naar de andere stromen.

![ExpressRoute Global Reach](./media/interconnect-china/expressroute-virtual.png)

Momenteel is ExpressRoute Global Reach niet in elk land beschikbaar, maar u een oplossing configureren met Azure Virtual WAN.

U bijvoorbeeld een ExpressRoute configureren met Microsoft Peering en een VPN-tunnel verbinden via die peering met Azure Virtual WAN. Nu hebt u opnieuw de doorvoer tussen VPN en ExpressRoute zonder Global Reach en 3rd party provider en service, zoals Megaport Cloud, ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie:

* [Global Transit-netwerkarchitectuur met Azure Virtual WAN](virtual-wan-global-transit-network-architecture.md)

* [Een virtuele WAN-hub maken](virtual-wan-site-to-site-portal.md)

* [Een virtuele WAN beveiligde hub configureren](../firewall-manager/secure-cloud-network.md)

* [Overzicht van Azure Peering Service Preview](https://docs.microsoft.com/azure/peering-service/about)
