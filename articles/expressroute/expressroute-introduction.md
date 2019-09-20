---
title: 'Uw on-premises netwerk uitbreiden naar Azure via een privéverbinding - Overzicht van ExpressRoute: Azure | Microsoft Docs'
description: In het technische overzicht van ExpressRoute wordt uitgelegd hoe een ExpressRoute-verbinding kan worden gebruikt om uw on-premises netwerk via een persoonlijke verbinding uit te breiden naar Azure.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: overview
ms.date: 09/18/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: a068912857c16d2257d09e221477afc5d4a8d603
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123322"
---
# <a name="expressroute-overview"></a>Overzicht van ExpressRoute
Met ExpressRoute kunt u uw on-premises netwerken in de Microsoft Cloud uitbreiden via een persoonlijke verbinding die wordt gefaciliteerd door een connectiviteitsprovider. Met ExpressRoute kunt u verbindingen tot stand brengen met micro soft-Cloud Services, zoals Microsoft Azure en Office 365.

Via een connectiviteitsprovider in een co-locatiefaciliteit is connectiviteit mogelijk vanuit een any-to-any (IP VPN) netwerk, een point-to-point Ethernet-netwerk of een virtuele overlappende verbinding. ExpressRoute-verbindingen gaan niet via het openbare internet. Hierdoor kunnen ExpressRoute-verbindingen meer betrouw baarheid, hogere snelheden, consistente latenties en betere beveiliging dan gewone verbindingen via internet bieden. Zie [ExpressRoute connectivity models](expressroute-connectivity-models.md) (ExpressRoute-connectiviteitsmodellen) voor meer informatie over verbinding maken tussen uw netwerk en Microsoft met behulp van ExpressRoute.

![Overzicht van ExpressRoute-verbindingen](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Belangrijkste voordelen

* Laag-3-connectiviteit tussen uw on-premises netwerk en de Microsoft Cloud via een connectiviteitsprovider. Connectiviteit is mogelijk van een any-to-any (IPVPN) netwerk, een point-to-point Ethernet-verbinding of langs een virtuele overlappende verbinding via een Ethernet-exchange.
* Connectiviteit met Microsoft Cloud-services tussen alle gebieden in de geopolitieke regio.
* Globale connectiviteit met Microsoft-services tussen alle regio's met de ExpressRoute Premium-invoegtoepassing.
* Dynamische routering tussen uw netwerk en Microsoft via BGP.
* Ingebouwde redundantie op elke peeringlocatie voor hogere betrouwbaarheid.
* [SLA](https://azure.microsoft.com/support/legal/sla/) voor verbindingsbedrijfstijd.
* QoS-ondersteuning voor Skype voor Bedrijven.

Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor meer informatie.

## <a name="features"></a>Functies

### <a name="layer-3-connectivity"></a>Laag-3-connectiviteit
Microsoft maakt gebruik van BGP, een standaardprotocol voor dynamische routering, voor het uitwisselen van routes tussen uw on-premises netwerk, uw exemplaren in Azure en openbare Microsoft-adressen. We stellen meerdere BGP-sessies met uw netwerk in voor verschillende verkeersprofielen. Meer informatie vindt u in het artikel [ExpressRoute circuit and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-circuit en -routeringsdomeinen).

### <a name="redundancy"></a>Redundantie
Elk ExpressRoute-circuit bestaat uit twee verbindingen met twee Microsoft Enterprise-randrouters (MSEE's) van de connectiviteitsprovider/uw netwerkrand. Microsoft vereist een dubbele BGP-verbinding van de connectiviteitsprovider/uw netwerkkant; één voor elke MSEE. U kunt ervoor kiezen om geen redundante apparaten/Ethernet-circuits aan uw kant te implementeren. Connectiviteitsproviders gebruiken redundante apparaten echter om ervoor te zorgen dat uw verbindingen op een redundante manier worden doorgegeven aan Microsoft. Onze [SLA](https://azure.microsoft.com/support/legal/sla/) is alleen geldig als er een redundante Laag-3-connectiviteit is geconfigureerd.

### <a name="connectivity-to-microsoft-cloud-services"></a>Connectiviteit met Microsoft Cloud-services
ExpressRoute-verbindingen maken toegang mogelijk tot de volgende services:
* Microsoft Azure-services
* Microsoft Office 365-services

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

Ga naar de pagina [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor een gedetailleerde lijst met services die via ExpressRoute worden ondersteund.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Connectiviteit met alle regio's binnen een geopolitieke regio
U kunt verbinding maken met Microsoft op een van onze [peeringlocaties](expressroute-locations.md), zodat u toegang hebt tot regio's binnen de geopolitieke regio.

Als u bijvoorbeeld via ExpressRoute verbinding maakt met Microsoft in Amsterdam, hebt u toegang tot alle Microsoft-cloudservices die worden gehost in Noord- en West-Europa. Voor een overzicht van de geopolitieke regio's, bijbehorende Microsoft-cloudregio's en bijbehorende ExpressRoute-peeringlocaties, raadpleegt u het artikel [Partners en peeringlocaties voor ExpressRoute](expressroute-locations.md).

### <a name="global-connectivity-with-expressroute-premium"></a>Wereld wijde connectiviteit met ExpressRoute Premium
U kunt [ExpressRoute Premium](expressroute-faqs.md) inschakelen om connectiviteit uit te breiden tussen de geopolitieke grenzen. Als u bijvoorbeeld via ExpressRoute verbinding maakt met Microsoft in Amsterdam, hebt u toegang tot alle Microsoft Cloud-services die worden gehost in alle regio's van de wereld (uitgezonderd nationale clouds). U hebt op dezelfde manier toegang tot services die zijn geïmplementeerd in Zuid-Amerika of Australië als waarop u toegang hebt tot regio's in Noord- en West-Europa.

### <a name="local-connectivity-with-expressroute-local"></a>Lokale verbinding met ExpressRoute Local
U kunt gegevens kosten effectief overdragen door de [lokale SKU](expressroute-faqs.md) in te scha kelen als u uw gegevens naar een ExpressRoute-locatie in de buurt van uw gewenste Azure-regio kunt brengen. Met lokale gegevens overdracht is opgenomen in de kosten voor de ExpressRoute-poort. 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>Overal on-premises connectiviteit met ExpressRoute Global Reach
U kunt ExpressRoute Global Reach inschakelen zodat er gegevens tussen al uw on-premises sites kunnen worden uitgewisseld, door uw ExpressRoute-circuits met elkaar te verbinden. Als u een privédatacenter in Californië hebt dat is verbonden met ExpressRoute in Silicon Valley en een ander privédatacenter in Texas dat is verbonden met ExpressRoute in Dallas, kunt u met ExpressRoute Global Reach uw privédatacenters met elkaar verbinden via twee ExpressRoute-circuits. Het verkeer tussen datacenters verplaatst zich via het netwerk van Microsoft.

Zie [ExpressRoute Global Reach](expressroute-global-reach.md) voor meer informatie.
### <a name="rich-connectivity-partner-ecosystem"></a>Uitgebreid connectiviteitsecosysteem van partners
ExpressRoute heeft een voortdurend groeiend ecosysteem van connectiviteitsproviders en systeemintegratorpartners. Raadpleeg het artikel [Partners en peeringlocaties voor ExpressRoute](expressroute-locations.md) voor de meest recente informatie.

### <a name="connectivity-to-national-clouds"></a>Connectiviteit met nationale clouds
Microsoft stuurt geïsoleerde cloudomgevingen aan voor speciale geopolitieke regio's en klantsegmenten. Raadpleeg de pagina [Partners en peeringlocaties voor ExpressRoute](expressroute-locations.md) voor een lijst van nationale clouds en providers.

### <a name="expressroute-direct"></a>ExpressRoute Direct
ExpressRoute Direct biedt klanten de mogelijkheid om rechtstreeks verbinding te maken met het wereldwijde netwerk van Microsoft op peeringlocaties die strategisch over de wereld zijn verdeeld. ExpressRoute Direct biedt twee verbindingsmogelijkheden van 100 Gbps, die ondersteuning bieden voor actief/actief-verbindingen op schaal.

Belangrijke functies die ExpressRoute rechtstreeks biedt, zijn onder andere de volgende:

* Massale gegevensopname in services als Storage en Cosmos DB
* Fysieke isolatie voor branches waarvoor bepaalde wettelijke regels gelden en waarvoor speciale en geïsoleerde verbindingen een vereiste zijn, zoals: het bankwezen, de overheid en de detailhandel
* Gedetailleerde controle van circuitdistributie op basis van bedrijfsonderdelen

Zie [About ExpressRoute Direct](https://go.microsoft.com/fwlink/?linkid=2022973) (Over ExpressRoute Direct) voor meer informatie.

### <a name="bandwidth-options"></a>Bandbreedte-opties
U kunt ExpressRoute-circuits aanschaffen voor een breed scala aan bandbreedten. Hieronder vindt u een lijst van de ondersteunde bandbreedten. Controleer ook de lijst met ondersteunde bandbreedten van uw connectiviteitsproviders.

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>Dynamische schaling van bandbreedte
U kunt de bandbreedte van het ExpressRoute-circuit (zo goed mogelijk) vergroten zonder de verbindingen te moeten verbreken. Zie [Wijzigen van een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md#modify) voor meer informatie.

### <a name="flexible-billing-models"></a>Flexibele factureringsmodellen
U kunt een factureringsmodel selecteren dat voor u het meest geschikt is. Kies een van de hieronder vermelde factureringsmodellen. Zie [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor meer informatie.

* **Onbeperkt gegevensverkeer**. De facturering is gebaseerd op een maandtarief; alle binnenkomende en uitgaande gegevensoverdracht is verder gratis.
* **Naar gebruik**. De facturering is gebaseerd op een maandtarief; alle binnenkomende gegevensoverdracht is gratis. Uitgaande gegevensoverdracht wordt in rekening gebracht per GB aan gegevensoverdracht. De tarieven voor gegevensoverdracht verschillen per regio.
* **Premium-invoegtoepassing voor ExpressRoute**. ExpressRoute Premium is een invoegtoepassing voor het ExpressRoute-circuit. De Premium-invoegtoepassing voor ExpressRoute biedt de volgende mogelijkheden: 
  * Ruimere routelimieten voor openbare en persoonlijke Azure-peering van 4000 routes naar 10.000 routes.
  * Globale connectiviteit voor services. Een ExpressRoute-circuit, gemaakt in een willekeurige regio (met uitzondering van nationale clouds), hebben toegang tot resources in elke andere regio ter wereld. Zo is een virtueel netwerk, gemaakt in Europa - west, toegankelijk via een ExpressRoute-circuit dat is ingericht in Silicon Valley.
  * Aantal VNet-koppelingen per ExpressRoute-circuit verhoogd van 10 tot een hogere limiet, afhankelijk van de bandbreedte van het circuit.

## <a name="faq"></a>Veelgestelde vragen
Zie [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor veelgestelde vragen over ExpressRoute.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [ExpressRoute-connectiviteitsmodellen](expressroute-connectivity-models.md).
* Meer informatie over ExpressRoute-verbindingen en -routeringsdomeinen. Zie [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-circuits en -routeringsdomeinen).
* Zoek een serviceprovider Zie [ExpressRoute partners and peering locations](expressroute-locations.md) (ExpressRoute-partners en -peeringlocaties).
* Controleer of aan alle vereisten is voldaan. Zie [ExpressRoute prerequisites](expressroute-prerequisites.md) (Vereisten voor ExpressRoute).
* Raadpleeg de vereisten voor [Routering](expressroute-routing.md), [NAT](expressroute-nat.md) en [QoS](expressroute-qos.md).
* Configureer uw ExpressRoute-verbinding.
  * [Een ExpressRoute-circuit maken en wijzigen](expressroute-howto-circuit-portal-resource-manager.md)
  * [Peering voor een ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-portal-resource-manager.md)
  * [Een virtueel netwerk verbinden met een ExpressRoute-circuit](expressroute-howto-linkvnet-portal-resource-manager.md)
* Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](../networking/networking-overview.md) van Azure.
