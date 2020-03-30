---
title: 'Azure ExpressRoute: verbinding maken met Microsoft Cloud met global reach'
description: In dit artikel wordt ExpressRoute Global Reach uitgelegd.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: cherylmc
ms.openlocfilehash: f574576044b7e4ddd34289b5cc45fe5ca353f180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538501"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute is een privé- en veerkrachtige manier om uw on-premises netwerken aan te sluiten op Microsoft Cloud. U hebt toegang tot veel Microsoft-cloudservices, zoals Azure en Office 365, vanuit uw privédatacenter of uw bedrijfsnetwerk. U bijvoorbeeld een bijkantoor in San Francisco hebben met een ExpressRoute-circuit in Silicon Valley en een ander filiaal in Londen met een ExpressRoute-circuit in dezelfde stad. Beide filialen kunnen snelle connectiviteit hebben met Azure-bronnen in het westen van de VS en het verenigd Koninkrijk. De filialen kunnen echter niet rechtstreeks met elkaar gegevens uitwisselen. Met andere woorden, 10.0.1.0/24 kan gegevens verzenden naar 10.0.3.0/24 en 10.0.4.0/24, maar NIET naar 10.0.2.0/24.

![Zonder][1]

Met **ExpressRoute Global Reach**u ExpressRoute-circuits met elkaar verbinden om een privénetwerk te maken tussen uw on-premises netwerken. In het bovenstaande voorbeeld, met de toevoeging van ExpressRoute Global Reach, kan uw kantoor in San Francisco (10.0.1.0/24) rechtstreeks gegevens uitwisselen met uw kantoor in Londen (10.0.2.0/24) via de bestaande ExpressRoute-circuits en via het wereldwijde netwerk van Microsoft. 

![Met][2]

## <a name="use-case"></a>Gebruiksvoorbeeld
ExpressRoute Global Reach is ontworpen om de WAN-implementatie van uw serviceprovider aan te vullen en uw filialen over de hele wereld met elkaar te verbinden. Als uw serviceprovider bijvoorbeeld voornamelijk actief is in de Verenigde Staten en al uw vestigingen in de VS heeft gekoppeld, maar de serviceprovider niet actief is in Japan en Hong Kong, u met ExpressRoute Global Reach samenwerken met een lokale serviceprovider en Microsoft zal uw vestigingen daar verbinden met die in de VS via ExpressRoute en ons wereldwijde netwerk.

![use case][3]

## <a name="availability"></a>Beschikbaarheid 
ExpressRoute Global Reach wordt momenteel op de volgende plaatsen ondersteund.

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
* Singapore
* Zwitserland
* Verenigd Koninkrijk
* Verenigde Staten

Uw ExpressRoute-circuits moeten worden gemaakt op de [topeerlocaties van ExpressRoute](expressroute-locations.md) in de bovengenoemde landen of regio.Your ExpressRoute circuits must be created at the ExpressRoute peering locaties in the above countries or region. Om ExpressRoute Global Reach tussen [verschillende geopolitieke regio's](expressroute-locations.md)mogelijk te maken, moeten uw circuits Premium SKU zijn.

## <a name="next-steps"></a>Volgende stappen
1. [Meer informatie over ExpressRoute Global Reach](expressroute-faqs.md)
2. [ExpressRoute Global Reach inschakelen](expressroute-howto-set-global-reach.md)
3. [ExpressRoute-circuit koppelen aan virtueel Azure-netwerk](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram zonder globaal bereik"
[2]: ./media/expressroute-global-reach/2.png "diagram met globaal bereik"
[3]: ./media/expressroute-global-reach/3.png "use case van wereldwijd bereik"
