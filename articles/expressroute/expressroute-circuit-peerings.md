---
title: 'Azure ExpressRoute: circuits en peering'
description: Deze pagina geeft een overzicht van ExpressRoute circuits en routing domeinen/peering.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mialdrid
ms.openlocfilehash: c68ffd019937f902567c3deda8d879448dc082da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281351"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute-circuits en peering

ExpressRoute-circuits verbinden uw on-premises infrastructuur met Microsoft via een connectiviteitsprovider. Dit artikel helpt u bij het begrijpen van ExpressRoute-circuits en routeringsdomeinen/peering. De volgende afbeelding toont een logische weergave van de connectiviteit tussen uw WAN en Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Azure public peering is afgeschaft en is niet beschikbaar voor nieuwe ExpressRoute-circuits. Nieuwe circuits ondersteunen Microsoft-peering en private peering.  
>

## <a name="expressroute-circuits"></a><a name="circuits"></a>ExpressRoute-circuits

Een ExpressRoute-circuit vormt een logische verbinding tussen uw on-premises infrastructuur en Microsoft-cloudservices via een connectiviteitsprovider. U meerdere ExpressRoute-circuits bestellen. Elk circuit kan zich in dezelfde of verschillende regio's bevinden en kan via verschillende connectiviteitsproviders met uw locatie worden verbonden.

ExpressRoute-circuits worden niet toegewezen aan fysieke entiteiten. Een circuit is uniek geïdentificeerd door een standaard GUID genoemd als een service key (s-key). De servicesleutel is het enige stukje informatie dat wordt uitgewisseld tussen Microsoft, de connectiviteitsprovider en u. De s-key is geen geheim voor veiligheidsdoeleinden. Er is een 1:1 mapping tussen een ExpressRoute circuit en de s-key.

Nieuwe ExpressRoute-circuits kunnen twee onafhankelijke peerings bevatten: Private peering en Microsoft-peering. Terwijl bestaande ExpressRoute-circuits drie peerings kunnen bevatten: Azure Public, Azure Private en Microsoft. Elke peering is een paar onafhankelijke BGP-sessies, elk van hen redundant geconfigureerd voor hoge beschikbaarheid. Er is een 1:N (1 <= N <= 3) mapping tussen een ExpressRoute circuit en routing domeinen. Een ExpressRoute-circuit kan elke één, twee of alle drie de peerings per ExpressRoute-circuit hebben ingeschakeld.

Elk circuit heeft een vaste bandbreedte (50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 10 Gbps) en is toegewezen aan een connectiviteitsprovider en een peeringlocatie. De bandbreedte die u selecteert, wordt gedeeld in alle circuitpeeringen

### <a name="quotas-limits-and-limitations"></a><a name="quotas"></a>Quota, beperkingen en beperkingen

Standaardquota en limieten gelden voor elk ExpressRoute-circuit. Raadpleeg de pagina [Azure-abonnements- en servicelimieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md) voor actuele informatie over quota.

## <a name="expressroute-peering"></a><a name="routingdomains"></a>ExpressRoute-peering

Aan een ExpressRoute-circuit zijn meerdere routeringsdomeinen/peerings gekoppeld: Azure public, Azure private en Microsoft. Elke peering is identiek geconfigureerd op een paar routers (in actieve of load sharing configuratie) voor hoge beschikbaarheid. Azure-services worden gecategoriseerd als *Azure public* en *Azure private* om de IP-adresseringsschema's weer te geven.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a><a name="privatepeering"></a>Persoonlijke Azure-peering

Azure compute services, namelijk virtual machines (IaaS) en cloud services (PaaS), die worden geïmplementeerd binnen een virtueel netwerk, kunnen worden verbonden via het private peering-domein. Het domein voor privé-peering wordt beschouwd als een vertrouwde uitbreiding van uw kernnetwerk in Microsoft Azure. U bidirectionele connectiviteit instellen tussen uw kernnetwerk en Virtuele Azure-netwerken (VNets). Met deze peering u rechtstreeks verbinding maken met virtuele machines en cloudservices op hun privé-IP-adressen.  

U meer dan één virtueel netwerk verbinden met het domein voor privé-peering. Bekijk de [faq-pagina](expressroute-faqs.md) voor informatie over limieten en beperkingen. U de pagina [Azure-abonnements- en servicelimieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md) bezoeken voor actuele informatie over limieten.  Raadpleeg de [routeringspagina](expressroute-routing.md) voor gedetailleerde informatie over de configuratie van de routering.

### <a name="microsoft-peering"></a><a name="microsoftpeering"></a>Microsoft-peering

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Connectiviteit met Online Services van Microsoft (Office 365- en Azure PaaS-services) vindt plaats via Microsoft-peering. We maken bidirectionele connectiviteit mogelijk tussen uw WAN- en Microsoft-cloudservices via het Microsoft-peering-routeringsdomein. U moet alleen verbinding maken met Microsoft-cloudservices via openbare IP-adressen die eigendom zijn van u of uw connectiviteitsprovider en u moet zich aan alle gedefinieerde regels houden. Zie voor meer informatie de pagina [Met voorwaarden voor ExpressRoute.](expressroute-prerequisites.md)

Zie de [faq-pagina](expressroute-faqs.md) voor meer informatie over ondersteunde services, kosten en configuratiegegevens. Zie de pagina [Locatie expressroute](expressroute-locations.md) voor informatie in de lijst met connectiviteitsproviders die Microsoft-peering-ondersteuning bieden.

## <a name="peering-comparison"></a><a name="peeringcompare"></a>Peering vergelijking

In de volgende tabel worden de drie peerings vergeleken:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

U een of meer van de routeringsdomeinen inschakelen als onderdeel van uw ExpressRoute-circuit. U ervoor kiezen om alle routeringsdomeinen op dezelfde VPN te laten plaatsen als u ze wilt combineren tot één routeringsdomein. U ze ook op verschillende routeringsdomeinen plaatsen, vergelijkbaar met het diagram. De aanbevolen configuratie is dat private peering rechtstreeks is verbonden met het kernnetwerk en dat de koppelingen naar het publiek en Microsoft-peering zijn verbonden met uw DMZ.

Elke peering vereist afzonderlijke BGP-sessies (één paar voor elk peeringtype). De BGP-sessieparen bieden een zeer beschikbare link. Als u verbinding maakt via layer 2-connectiviteitsproviders, bent u verantwoordelijk voor het configureren en beheren van routering. U meer informatie vinden door de [werkstromen](expressroute-workflows.md) voor het instellen van ExpressRoute te bekijken.

## <a name="expressroute-health"></a><a name="health"></a>ExpressRoute-status

ExpressRoute-circuits kunnen worden gecontroleerd op beschikbaarheid, connectiviteit met VNets en bandbreedtegebruik met behulp van [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM).

NPM bewaakt de status van Azure private peering en Microsoft-peering. Bekijk onze [post](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Zoek een serviceprovider Zie [ExpressRoute-serviceproviders en -locaties](expressroute-locations.md).
* Controleer of aan alle vereisten is voldaan. Zie [ExpressRoute prerequisites](expressroute-prerequisites.md) (Vereisten voor ExpressRoute).
* Configureer uw ExpressRoute-verbinding.
  * [Create and manage ExpressRoute circuits](expressroute-howto-circuit-portal-resource-manager.md) (ExpressRoute-circuits maken en beheren)
  * [Routering (peering) configureren voor ExpressRoute-circuits](expressroute-howto-routing-portal-resource-manager.md)
