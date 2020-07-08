---
title: Over de prijzen voor Virtual WAN
titleSuffix: Azure Virtual WAN
description: In dit artikel worden algemene vragen over Virtual WAN-prijzen beschreven
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: 2d2234ec333746c6f1da59346bdb74247deb616c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568529"
---
# <a name="about-virtual-wan-pricing"></a>Over de prijzen voor Virtual WAN

Azure Virtual WAN brengt meerdere netwerk-en beveiligings Services samen in een uniform Framework. Het is gebaseerd op een hub-en spoke-architectuur, waarbij de hubs door micro soft worden beheerd met verschillende services die in de hub zijn opgenomen, zoals VPN, ExpressRoute, gebruikers-VPN (punt-naar-site), firewall, route ring, enzovoort.

Voor elke service in Virtual WAN is de prijs. Daarom is het Voorst Ellen van een enkele prijs niet van toepassing op virtuele WAN. De [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/) biedt een mechanisme voor het afleiden van de kosten, gebaseerd op de services die zijn ingericht in een virtueel WAN. In dit artikel worden veelgestelde vragen over de prijzen van virtuele WAN besproken.

>[!NOTE]
>Zie [prijzen voor Virtual WAN](https://azure.microsoft.com/pricing/details/virtual-wan/)voor actuele prijs informatie.
>

## <a name="common-pricing-questions"></a><a name="questions"></a>Veelgestelde vragen over prijzen

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>Wat is een schaal eenheid?

Een **schaal eenheid** biedt de eenheid voor de totale capaciteit van site-naar-site (S2S), punt-naar-site (P2S) en ExpressRoute (er) in een virtuele hub. Bijvoorbeeld:

* **1 S2S VPN-schaal eenheid** impliceert een totale capaciteit van 500 Mbps VPN-gateway (Dual instances worden geïmplementeerd voor tolerantie) in een virtuele hub costing $0.361/uur.
* **1** er is een totaal van 2 Gbps er-gateways in Virtual hub costing $0.42/HR.
* **5** er is een totaal van 10 Gbps-gateway eenheden in een virtuele hub VNet geprijsd op $0.42 * 5/HR. Er wordt $0,25/uur verhoogd van de zesde tot de tiende schaal eenheid.

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>Wat is een verbindings eenheid?

Een **verbindings eenheid** is van toepassing op alle on-premises/niet-micro soft-eind punten die verbinding maken met Azure-gateways. Voor site-naar-site-VPN impliceert dit branches. Voor gebruikers VPN (punt-naar-site) duidt dit op externe gebruikers. Voor ExpressRoute duidt dit op ExpressRoute circuit Connections.<br>Bijvoorbeeld:

* Een vertakkings verbinding die verbinding maakt met Azure VPN in een virtuele hub kostte € 0,05/HR. Daarom 100 vertakkings verbindingen die verbinding maken met een virtuele Azure-hub, kosten $0,05 * 100/uur.

* Twee ExpressRoute circuit verbindingen die verbinding maken met een virtuele hub, kosten € 0,05 * 2/uur.

* Drie externe gebruikers verbindingen die verbinding maken met de virtuele P2S-gateway van Azure, kosten $0.03 * 3/HR.

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>Hoe worden kosten voor gegevens overdracht berekend?

* Verkeer dat binnenkomt in azure, wordt niet in rekening gebracht. Verkeer dat Azure verlaat (via VPN-, ExpressRoute-of punt-naar-site-VPN-verbindingen) is onderhevig aan de standaard [kosten voor Azure-gegevens overdracht](https://azure.microsoft.com/pricing/details/bandwidth/).

* Voor de kosten voor gegevens overdracht tussen een virtuele WAN-hub en een externe virtuele WAN-hub of een VNet in een andere regio dan de bron-hub gelden kosten voor gegevens overdracht voor verkeer dat een hub verlaat. Voor beeld: verkeer dat een VS-Oost-hub verlaat, wordt $0,02/GB in rekening gebracht naar een VS West-hub. Er worden geen kosten in rekening gebracht voor verkeer dat binnenkomt op de VS West-hub. In de volgende tabellen worden de kosten weer gegeven.

In de onderstaande tabellen worden de volgende afkortingen gebruikt: {naam: Noord-Amerika}, {EU: Europa}, {MEA: Midden-Oosten-Afrika}, {OC: Oceania (Australië-centraal en Australië-centraal 2)}, {LATAM: Latijns-Amerika} 

**Prijzen binnen continenten (*)**

| Binnen continent| Prijs ($/GB)|
|---|---|
| De nr.|$ 0,02 |
| EU naar EU |$ 0,02 |
| Azië en Azië (met uitzonde ring van China)|$0,10 |
| MEA naar MEA|$0,16 |
| LATAM-LATAM |$0,16 |
| OC-OC|$0,12 |

**Inter-continentale prijzen (*)**

| Inter-continentale| Prijs ($/GB)|
|---|---|
| VAN de in de EU of EU naar de nr. |$0,07 |
| VAN LATAM naar een wille keurige locatie |$0,17 |
| VAN MEA naar een wille keurige locatie |$0,17 |
| VAN OCEANIA naar een wille keurige locatie |$0,12 |
| Vanuit Azië (met uitzonde ring van CHINA) naar elke locatie |$0,12 |

(*) Enkele kosten kunnen van toepassing zijn vanaf 1 augustus 2020.

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>Wat is het verschil tussen een Standard hub-tarief en een standaard hub-verwerkings tarief?

Virtuele WAN is beschikbaar in twee soorten:

* Een **eenvoudig virtueel WAN**, waar gebruikers meerdere hubs kunnen implementeren en gebruikmaken van VPN-site-naar-site-connectiviteit. Een eenvoudig virtueel WAN heeft geen geavanceerde mogelijkheden, zoals volledig gemeshe hubs, ExpressRoute-connectiviteit, VPN-verbinding tussen sites, tussen VPN-verbindingen, VPN-naar-VNet-transitieve connectiviteit, vpn's en ExpressRoute Transit connectiviteit, of Azure Firewall, enzovoort. Er zijn geen basis kosten of gegevens verwerkings kosten voor hubs in een virtueel WAN.

* Een **standaard virtuele WAN** biedt geavanceerde mogelijkheden, zoals volledig gemeshe hubs, ExpressRoute-connectiviteit, VPN-verbinding tussen sites, communicatie via vnet-naar-vnet transitieve verbinding, VPN en ExpressRoute Transit connectiviteit, Azure firewall, enzovoort. Alle route ring van de virtuele hub wordt verschaft door een router waarmee meerdere services in een virtuele hub worden ingeschakeld. Er zijn basis kosten voor de hub, die zijn geprijsd op $0.25/uur. Er zijn ook kosten verbonden aan het verwerken van gegevens in de virtuele hub-router voor de overdracht van VNet-naar-VNet-verbindingen. Zie de volgende afbeelding.

 In het onderstaande **voor beeld** zijn er twee VNETS, vnet 1 en vnet 2. We gaan ervan uitgaan dat er 1 Gbps-gegevens worden verzonden vanaf een virtuele machine in VNET 1 naar een andere virtuele machine in VNET 2. De volgende kosten zijn van toepassing:

* Basis tarief voor virtuele hub $0.25/HR

* Gegevens verwerkings kosten voor Virtual hub van $0,02/GB voor 1 Gbps

**Voorbeeld**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="Voorbeeld":::

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.

* Zie [prijzen voor Virtual WAN](https://azure.microsoft.com/pricing/details/virtual-wan/)voor actuele prijzen.
