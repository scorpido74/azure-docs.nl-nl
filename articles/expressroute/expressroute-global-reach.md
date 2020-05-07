---
title: 'Azure-ExpressRoute: verbinding maken met Microsoft Cloud met behulp van Global Reach'
description: In dit artikel worden ExpressRoute Global Reach beschreven.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: cherylmc
ms.openlocfilehash: 7e98f4a401a9fcab12bd7a276c47df67c7936b1d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853412"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute is een privé-en robuuste manier om uw on-premises netwerken te verbinden met Microsoft Cloud. U hebt toegang tot veel micro soft-Cloud Services, zoals Azure, en Office 365 vanuit uw privé Data Center of uw bedrijfs netwerk. Zo kunt u bijvoorbeeld een filiaal in San Francisco hebben met een ExpressRoute-circuit in Silicon dal en een ander filiaal in Londen met een ExpressRoute-circuit in dezelfde plaats. Beide filialen kunnen hoge snelheid hebben voor de verbinding met Azure-resources in VS West en UK-zuid. De filialen kunnen gegevens echter niet rechtstreeks met elkaar uitwisselen. Met andere woorden, 10.0.1.0/24 kan gegevens verzenden naar 10.0.3.0/24 en 10.0.4.0/24, maar niet op 10.0.2.0/24.

![gewijzigd][1]

Met **ExpressRoute Global Reach**kunt u ExpressRoute-circuits samen koppelen om een particulier netwerk tussen uw on-premises netwerken te maken. In het bovenstaande voor beeld, met de toevoeging van ExpressRoute Global Reach, kan uw San Francisco Office (10.0.1.0/24) gegevens rechtstreeks uitwisselen met uw kantoor in Londen (10.0.2.0/24) via de bestaande ExpressRoute-circuits en via het wereld wijde netwerk van micro soft. 

![met][2]

## <a name="use-case"></a>Gebruiksvoorbeeld
ExpressRoute Global Reach is ontworpen voor een aanvulling op de WAN-implementatie van uw service provider en verbindt uw filialen over de hele wereld. Als uw service provider bijvoorbeeld voornamelijk in de Verenigde Staten werkt en al uw branches in de V.S. is gekoppeld, maar de service provider niet werkt in Japan en Hong Kong, met ExpressRoute Global Reach u met een lokale service provider kunt werken en micro soft uw vertakkingen in de VS verbindt met ExpressRoute en ons wereld wijde netwerk.

![use-case][3]

## <a name="availability"></a>Beschikbaarheid 
ExpressRoute Global Reach op dit moment wordt momenteel ondersteund op de volgende locaties.

* Australië
* Canada
* Frankrijk
* Duitsland
* Hongkong SAR
* Ierland
* Japan
* Korea
* Nederland
* Nieuw-Zeeland
* Noorwegen
* Singapore
* Zwitserland
* Verenigd Koninkrijk
* Verenigde Staten

Uw ExpressRoute-circuits moeten worden gemaakt op de [ExpressRoute-peering locaties](expressroute-locations.md) in de bovenstaande landen of regio's. Om ExpressRoute Global Reach te scha kelen tussen [verschillende geopolitieke regio's](expressroute-locations.md), moeten uw circuits een Premium-SKU zijn.

## <a name="next-steps"></a>Volgende stappen
1. [De veelgestelde vragen over Global Reach weer geven](expressroute-faqs.md#globalreach)
2. [Meer informatie over het inschakelen van Global Reach](expressroute-howto-set-global-reach.md)
3. [Meer informatie over het koppelen van een ExpressRoute-circuit aan uw virtuele netwerk](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram zonder globaal bereik"
[2]: ./media/expressroute-global-reach/2.png "diagram met wereld wijd bereik"
[3]: ./media/expressroute-global-reach/3.png "gebruik van wereld wijd bereik"
