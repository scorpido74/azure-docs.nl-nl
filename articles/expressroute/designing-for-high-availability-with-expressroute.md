---
title: 'Azure ExpressRoute: ontwerpen voor hoge beschikbaarheid'
description: Deze pagina bevat architecturale aanbevelingen voor hoge beschikbaarheid tijdens het gebruik van Azure ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4c3c6ae5fbdd91e6e44438be7fef2a3a91564a34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076686"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Ontwerpen voor hoge beschikbaarheid met ExpressRoute

ExpressRoute is ontworpen voor hoge beschikbaarheid om privénetwerkconnectiviteit van carrierkwaliteit te bieden voor Microsoft-bronnen. Met andere woorden, er is geen enkel storingspunt in het ExpressRoute-pad binnen het Microsoft-netwerk. Om de beschikbaarheid te maximaliseren, moeten de klant en het segment van de serviceprovider van uw ExpressRoute-circuit ook worden ontworpen voor hoge beschikbaarheid. In dit artikel, laten we eerst kijken naar de netwerkarchitectuur overwegingen voor het bouwen van robuuste netwerkconnectiviteit met behulp van een ExpressRoute, dan laten we eens kijken naar de fine-tuning functies die u helpen om de hoge beschikbaarheid van uw ExpressRoute circuit te verbeteren.


## <a name="architecture-considerations"></a>Architectuuroverwegingen

De volgende figuur illustreert de aanbevolen manier om verbinding te maken met behulp van een ExpressRoute-circuit voor het maximaliseren van de beschikbaarheid van een ExpressRoute-circuit.

 [![1]][1 1.]

Voor hoge beschikbaarheid is het essentieel om de redundantie van het ExpressRoute-circuit in het hele end-to-end netwerk te behouden. Met andere woorden, u moet redundantie binnen uw on-premises netwerk behouden en mag redundantie binnen uw netwerk van serviceproviders niet in gevaar brengen. Het tot een minimum beperken van redundantie houdt in dat één punt van netwerkstoringen wordt voorkomen. Het hebben van redundante stroom en koeling voor de netwerkapparaten zal de hoge beschikbaarheid verder verbeteren.

### <a name="first-mile-physical-layer-design-considerations"></a>Eerste mijl fysieke laag ontwerp overwegingen

 Als u zowel de primaire als de secundaire verbindingen van een ExpressRoute-circuits op dezelfde Customer Premises Equipment (CPE) beëindigt, brengt u de hoge beschikbaarheid binnen uw on-premises netwerk in gevaar. Als u bovendien zowel de primaire als de secundaire verbindingen configureert via dezelfde poort van een CPE (door de twee verbindingen onder verschillende subinterfaces te beëindigen of door de twee verbindingen binnen het partnernetwerk samen te voegen), dwingt u de partner om ook een hoge beschikbaarheid in hun netwerksegment in gevaar te brengen. Dit compromis wordt geïllustreerd in het volgende cijfer.

[![2]][2 2.]

Aan de andere kant, als u de primaire en secundaire verbindingen van een ExpressRoute-circuits op verschillende geografische locaties beëindigt, u de netwerkprestaties van de connectiviteit in gevaar brengen. Als het verkeer actief wordt belast, wordt het in evenwicht gebracht tussen de primaire en de secundaire verbindingen die op verschillende geografische locaties worden beëindigd, zou een mogelijk aanzienlijk verschil in netwerklatentie tussen de twee paden resulteren in een suboptimaal netwerk Prestaties. 

Zie [Ontwerpen voor noodherstel met ExpressRoute voor][DR]overwegingen met georedundante ontwerp.

### <a name="active-active-connections"></a>Actief actieve verbindingen

Het Microsoft-netwerk is geconfigureerd om de primaire en secundaire verbindingen van ExpressRoute-circuits in actieve modus te bedienen. Via uw routeadvertenties u echter de redundante verbindingen van een ExpressRoute-circuit dwingen om in actief-passieve modus te werken. Reclame meer specifieke routes en BGP AS pad prepending zijn de gemeenschappelijke technieken die worden gebruikt om een pad de voorkeur boven de andere.

Om de hoge beschikbaarheid te verbeteren, is het raadzaam om zowel de verbindingen van een ExpressRoute-circuit in actieve modus te bedienen. Als u de verbindingen in de actieve modus laat werken, laadt het Microsoft-netwerk het verkeer per stroom in evenwicht over de verbindingen.

Het uitvoeren van de primaire en secundaire verbindingen van een ExpressRoute-circuit in actief-passieve modus loopt het risico dat beide verbindingen uitvallen na een storing in het actieve pad. De gemeenschappelijke oorzaken voor mislukking bij omschakeling zijn gebrek aan actief beheer van de passieve verbinding, en passieve verbindingsreclame oude routes.

Als alternatief, het uitvoeren van de primaire en secundaire verbindingen van een ExpressRoute circuit in actief-actieve modus, resulteert in slechts ongeveer de helft van de stromen niet en krijgen omgeleid, na een ExpressRoute verbinding fout. Zo zal de actief-actieve modus aanzienlijk helpen bij het verbeteren van de Mean Time To Recover (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT voor Microsoft-peering 

Microsoft-peering is ontworpen voor communicatie tussen openbare eindpunten. Zo vaak zijn on-premises privéeindpunten Network Address Translated (NATed) met openbare IP op het klant- of partnernetwerk voordat ze communiceren via Microsoft-peering. Ervan uitgaande dat u zowel de primaire als de secundaire verbindingen in de actieve modus gebruikt, waar en hoe u NAT een impact heeft op hoe snel u herstelt na een storing in een van de ExpressRoute-verbindingen. Twee verschillende NAT-opties worden geïllustreerd in de volgende afbeelding:

[![3]][3]

In optie 1 wordt NAT toegepast nadat het verkeer is opgesplitst tussen de primaire en secundaire verbindingen van de ExpressRoute. Om te voldoen aan de stateful eisen van NAT, onafhankelijke NAT zwembaden worden gebruikt tussen de primaire en de secundaire apparaten, zodat het retourverkeer zou komen tot dezelfde rand apparaat waardoor de stroom uitviel.

In optie 2 wordt een gemeenschappelijke NAT-pool gebruikt voordat het verkeer wordt verdeeld tussen de primaire en secundaire verbindingen van de ExpressRoute. Het is belangrijk om het onderscheid te maken dat de gemeenschappelijke NAT-pool voor het splitsen van het verkeer niet betekent dat single-point of failure wordt ingeleid, waardoor de beschikbaarheid in gevaar komt.

Met optie 1 is na een ExpressRoute-verbindingsfout de mogelijkheid om de bijbehorende NAT-pool te bereiken verbroken. Daarom moeten alle verbroken stromen opnieuw worden vastgesteld door TCP of toepassingslaag na de bijbehorende window time-out. Als een van de NAT-pools wordt gebruikt om een van de on-premises servers te belagen en als de bijbehorende connectiviteit mislukt, kunnen de on-premises servers niet worden bereikt vanuit Azure totdat de connectiviteit is opgelost.

Terwijl met optie 2 de NAT zelfs na een primaire of secundaire verbindingsfout bereikbaar is. Daarom kan de netwerklaag zelf de pakketten omleiden en sneller herstellen na de storing. 

> [!NOTE]
> Als u NAT-optie 1 (onafhankelijke NAT-pools voor primaire en secundaire ExpressRoute-verbindingen) en een poort van een IP-adres van een van de NAT-groep in kaart brengt, is de server niet bereikbaar via het ExpressRoute-circuit wanneer de overeenkomstige verbinding mislukt.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Fine-tuning functies voor private peering

Laat ons in deze sectie optionele (afhankelijk van uw Azure-implementatie en hoe gevoelig u bent voor MTTR) bekijken en die helpen bij het verbeteren van de hoge beschikbaarheid van uw ExpressRoute-circuit. Laten we in het bijzonder de zonebewuste implementatie van ExpressRoute-virtuele netwerkgateways en Bidirectionele Forwarding Detection (BFD) bekijken.

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Beschikbaarheidzonebewuste ExpressRoute-gateways voor virtuele netwerken

Een beschikbaarheidszone in een Azure-regio is een combinatie van een foutdomein en een updatedomein. Als u kiest voor zoneredundante Azure IaaS-implementatie, u ook zoneredundante virtuele netwerkgateways configureren die het privé-peering van ExpressRoute beëindigen. Zie [Informatie over redundante virtuele netwerkgateways in Azure Availability Zones][zone redundant vgw]voor meer informatie. Zie Een externe virtuele netwerkgateway maken [in Azure Availability Zones][conf zone redundant vgw]als u de redundante virtuele netwerkgateway voor de zone wilt configureren.

### <a name="improving-failure-detection-time"></a>De detectietijd van storingen verbeteren

ExpressRoute ondersteunt BFD via private peering. BFD vermindert de detectietijd van het laag 2-netwerk tussen Microsoft Enterprise Edge (MTE's) en hun BGP-buren aan de on-premises kant van ongeveer 3 minuten (standaard) tot minder dan een seconde. Snelle storingsdetectietijd helpt het herstel van storingen te versnellen. Zie [BFD configureren via ExpressRoute][BFD]voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

In dit artikel bespraken we hoe te ontwerpen voor hoge beschikbaarheid van een ExpressRoute-circuitconnectiviteit. Een ExpressRoute-circuitpeeringspunt is vastgemaakt aan een geografische locatie en kan daarom worden beïnvloed door een catastrofale storing die de hele locatie beïnvloedt. 

Zie [Ontwerpen voor noodherstel met ExpressRoute private peering][DR]voor ontwerpoverwegingen voor ontwerpoverwegingen voor het bouwen van georedundante netwerkconnectiviteit met Microsoft-backbone die bestand is tegen catastrofale storingen, die van invloed zijn op een hele regio.

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "Aanbevolen manier om verbinding te maken via ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Suboptimale last mile connectiviteit"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT-opties"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




