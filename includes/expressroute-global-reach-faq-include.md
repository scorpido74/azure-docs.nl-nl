---
title: bestand opnemen
description: bestand opnemen
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317a480c13c5c6e00653fd61878a379df3f65ac4
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67175771"
---
### <a name="what-is-expressroute-global-reach"></a>Wat is ExpressRoute Global Reach?

ExpressRoute Global Reach is een Azure-service die uw on-premises netwerken verbindt via de ExpressRoute-service via het wereld wijde netwerk van micro soft. Als u bijvoorbeeld een persoonlijk Data Center in Californië hebt aangesloten op ExpressRoute in silicone dal en een ander persoonlijk Data Center in Texas dat is verbonden met ExpressRoute in Amsterdam, met ExpressRoute Global Reach, kunt u uw persoonlijke data centers met elkaar verbinden via de twee ExpressRoute-verbindingen en wordt uw verkeer via het cross Data Center via de netwerk-backbone van micro soft gepasseerd.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>ExpressRoute Global Reach Hoe kan ik in-of uitschakelen?

U schakelt ExpressRoute Global Reach in door uw ExpressRoute-circuits samen te koppelen. U kunt de functie uitschakelen door de verbinding met de circuits te verbreken. Zie de [configuratie](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Heb ik ExpressRoute Premium nodig voor ExpressRoute Global Reach?

Als uw ExpressRoute-circuits zich in dezelfde geopolitieke regio bevinden, hebt u geen ExpressRoute Premium nodig om ze met elkaar te verbinden. Als twee ExpressRoute-circuits zich in verschillende geopolitieke regio's bevinden, hebt u ExpressRoute Premium voor beide circuits nodig om de connectiviteit tussen beide te kunnen inschakelen. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Hoe worden er kosten in rekening gebracht voor ExpressRoute Global Reach?

ExpressRoute maakt connectiviteit mogelijk vanaf uw on-premises netwerk naar micro soft-Cloud Services. ExpressRoute Global Reach maakt connectiviteit mogelijk tussen uw eigen on-premises netwerken via uw bestaande ExpressRoute-circuits, met het wereld wijde netwerk van micro soft. ExpressRoute Global Reach wordt afzonderlijk in rekening gebracht op basis van de bestaande ExpressRoute-service. Er zijn extra kosten verbonden aan het inschakelen van deze functie op elk ExpressRoute-circuit. Verkeer tussen uw on-premises netwerken die worden ingeschakeld door ExpressRoute Global Reach wordt gefactureerd voor een uitgangs frequentie bij de bron en voor een ingangs snelheid bij de bestemming. De tarieven zijn gebaseerd op de zone waar de circuits zich bevinden.

### <a name="where-is-expressroute-global-reach-supported"></a>Waar wordt ExpressRoute Global Reach ondersteund?

ExpressRoute Global Reach wordt ondersteund in [geselecteerde landen/regio's of locaties](../articles/expressroute/expressroute-global-reach.md). De ExpressRoute-circuits moeten worden gemaakt op de peering-locaties in die landen/regio's of locaties.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Ik heb meer dan twee on-premises netwerken, die elk zijn verbonden met een ExpressRoute-circuit. Kan ik ExpressRoute Global Reach gebruiken om alle on-premises netwerken met elkaar te verbinden?

Ja, u kunt, zolang de circuits zich in de ondersteunde landen/regio's bevinden. U moet per keer twee ExpressRoute-circuits verbinden. Als u een volledig gemesh netwerk wilt maken, moet u alle circuit paren opsommen en de configuratie herhalen. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Kan ik ExpressRoute Global Reach tussen twee ExpressRoute-circuits op dezelfde peering-locatie inschakelen?

Nee. De twee circuits moeten afkomstig zijn van verschillende locaties op hetzelfde niveau. Als een metro in een ondersteund land/regio meer dan één ExpressRoute-peering-locatie heeft, kunt u verbinding maken met de ExpressRoute-circuits die zijn gemaakt op verschillende peering locaties in die metro lijn. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Als ExpressRoute Global Reach is ingeschakeld tussen circuit X en circuit Y, en tussen circuit Y en circuit Z, zullen mijn on-premises netwerken verbonden zijn met circuit X en circuit Z met elkaar communiceren via het netwerk van micro soft?

Nee. Als u de connectiviteit tussen twee van uw on-premises netwerken wilt inschakelen, moet u de bijbehorende ExpressRoute-circuits expliciet verbinden. In het bovenstaande voor beeld moet u verbinding maken met circuit X en circuit Z. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Wat is de netwerk doorvoer die ik kan verwachten tussen mijn on-premises netwerken nadat ik ExpressRoute Global Reach inschakelen?

De netwerk doorvoer tussen uw on-premises netwerken, ingeschakeld door ExpressRoute Global Reach, wordt beperkt door de kleinste van de twee ExpressRoute-circuits. Lokaal verkeer van gebouwen naar Azure en premises-naar-locatie verkeer delen hetzelfde circuit en zijn onderworpen aan dezelfde bandbreedte limiet. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>Wat zijn de limieten voor het aantal routes dat ik kan adverteren en het aantal routes dat ik ontvang met ExpressRoute Global Reach?

Het aantal routes dat u kunt adverteren voor micro soft op persoonlijke Azure-peering, blijft 4000 op een standaard circuit of 10000 op een Premium-circuit. Het aantal routes dat u van micro soft ontvangt op persoonlijke Azure-peering, is de som van de routes van uw virtuele Azure-netwerken en de routes van uw andere on-premises netwerken die zijn verbonden via ExpressRoute Global Reach. Zorg ervoor dat u een geschikte maximum limiet voor het voor voegsel voor uw on-premises router hebt ingesteld. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Wat is de SLA voor ExpressRoute-Global Reach?

ExpressRoute Global Reach biedt dezelfde SLA voor de [Beschik baarheid](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) als de normale ExpressRoute-service.
