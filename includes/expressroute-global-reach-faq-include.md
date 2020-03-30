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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175771"
---
### <a name="what-is-expressroute-global-reach"></a>Wat is ExpressRoute Global Reach?

ExpressRoute Global Reach is een Azure-service die uw on-premises netwerken via de ExpressRoute-service verbindt via het wereldwijde netwerk van Microsoft. Als u bijvoorbeeld een privédatacenter in Californië hebt dat is aangesloten op ExpressRoute in Silicon Valley en een ander privédatacenter in Texas is aangesloten op ExpressRoute in Dallas, met ExpressRoute Global Reach, u uw privédatacenters met elkaar verbinden via de twee ExpressRoute-verbindingen en uw cross-datacenterverkeer wordt via de netwerkbackbone van Microsoft doorkruisen.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Hoe schakel ik ExpressRoute Global Reach in of uit?

U schakelt ExpressRoute Global Reach in door uw ExpressRoute-circuits met elkaar te verbinden. U schakelt de functie uit door de circuits los te koppelen. Zie de [configuratie](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Heb ik ExpressRoute Premium nodig voor ExpressRoute Global Reach?

Als uw ExpressRoute-circuits zich in dezelfde geopolitieke regio bevinden, hebt u ExpressRoute Premium niet nodig om ze met elkaar te verbinden. Als twee ExpressRoute-circuits zich in verschillende geopolitieke regio's bevinden, hebt u ExpressRoute Premium voor beide circuits nodig om de verbinding tussen deze circuits mogelijk te maken. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Hoe worden er kosten in rekening gebracht voor ExpressRoute Global Reach?

ExpressRoute maakt connectiviteit mogelijk van uw on-premises netwerk naar Microsoft-cloudservices. ExpressRoute Global Reach maakt connectiviteit tussen uw eigen on-premises netwerken mogelijk via uw bestaande ExpressRoute-circuits, waarbij gebruik wordt gemaakt van het wereldwijde netwerk van Microsoft. ExpressRoute Global Reach wordt apart gefactureerd van de bestaande ExpressRoute-service. Er is een add-on vergoeding voor het inschakelen van deze functie op elk ExpressRoute-circuit. Verkeer tussen uw on-premises netwerken ingeschakeld door ExpressRoute Global Reach wordt gefactureerd voor een uitgangstarief bij de bron en voor een invallend tarief op de bestemming. De tarieven zijn gebaseerd op de zone waar de circuits zich bevinden.

### <a name="where-is-expressroute-global-reach-supported"></a>Waar wordt ExpressRoute Global Reach ondersteund?

ExpressRoute Global Reach wordt ondersteund in [bepaalde landen/regio's of plaatsen.](../articles/expressroute/expressroute-global-reach.md) De ExpressRoute-circuits moeten worden gemaakt op de peeringlocaties in die landen/regio's of plaatsen.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Ik heb meer dan twee on-premises netwerken, elk aangesloten op een ExpressRoute circuit. Kan ik ExpressRoute Global Reach inschakelen om al mijn on-premises netwerken met elkaar te verbinden?

Ja, dat kan, zolang de circuits zich in de ondersteunde landen/regio's bevinden. U moet twee ExpressRoute-circuits tegelijk aansluiten. Als u een volledig mesh-netwerk wilt maken, moet u alle circuitparen opsommen en de configuratie herhalen. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Kan ik ExpressRoute Global Reach inschakelen tussen twee ExpressRoute-circuits op dezelfde peeringlocatie?

Nee. De twee circuits moeten van verschillende peering locaties. Als een metro in een ondersteund land/regio meer dan één ExpressRoute-peeringlocatie heeft, u de ExpressRoute-circuits die op verschillende peeringlocaties in die metro zijn gemaakt, met elkaar verbinden. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Als ExpressRoute Global Reach is ingeschakeld tussen circuit X en circuit Y, en tussen circuit Y en circuit Z, zullen mijn on-premises netwerken die zijn aangesloten op circuit X en circuit Z met elkaar praten via het netwerk van Microsoft?

Nee. Om connectiviteit tussen twee van uw on-premises netwerken mogelijk te maken, moet u de bijbehorende ExpressRoute-circuits expliciet verbinden. In het bovenstaande voorbeeld moet u circuit X en circuit Z aansluiten. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Wat is de netwerkdoorvoer die ik kan verwachten tussen mijn on-premises netwerken nadat ik ExpressRoute Global Reach heb ingeschakeld?

De netwerkdoorvoer tussen uw on-premises netwerken, ingeschakeld door ExpressRoute Global Reach, wordt afgetopt door de kleinere van de twee ExpressRoute-circuits. Het verkeer van lokalen naar Azure en het bedrijfsverkeer delen hetzelfde circuit en zijn onderworpen aan dezelfde bandbreedtelimiet. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>Wat zijn met ExpressRoute Global Reach de beperkingen op het aantal routes dat ik kan adverteren en het aantal routes dat ik ontvang?

Het aantal routes dat u adverteren met Microsoft op Azure private peering blijft op 4000 op een standard circuit of 10000 op een Premium-circuit. Het aantal routes dat u van Microsoft op Azure private peering ontvangt, is de som van de routes van uw virtuele Azure-netwerken en de routes van uw andere on-premises netwerken die zijn verbonden via ExpressRoute Global Reach. Zorg ervoor dat u een passende maximale voorvoegsellimiet instelt op uw on-premises router. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Wat is de SLA voor ExpressRoute Global Reach?

ExpressRoute Global Reach biedt dezelfde [BESCHIKBAARHEID SLA](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) als de reguliere ExpressRoute-service.
