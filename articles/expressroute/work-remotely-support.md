---
title: Azure ExpressRoute gebruiken om externe gebruikers te ondersteunen
description: Op deze pagina wordt beschreven hoe u Azure ExpressRoute gebruiken om werken op afstand mogelijk te maken vanwege de COVID-19-pandemie.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 2fe990fd9c5922dd2e059dbb212cd8bb1cd3726e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336653"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Azure ExpressRoute gebruiken om hybride connectiviteit te maken om externe gebruikers te ondersteunen

In dit artikel wordt beschreven hoe u ExpressRoute, Azure, Microsoft-netwerk en het Azure-partnerecosysteem gebruiken om op afstand te werken.

## <a name="connecting-to-azure-services-with-expressroute"></a>Verbinding maken met Azure-services met ExpressRoute

>[!NOTE]
>In dit artikel wordt beschreven hoe u ExpressRoute, Azure, Microsoft-netwerk en het Azure-partnerecosysteem gebruiken om op afstand te werken en netwerkproblemen te beperken waarmee u wordt geconfronteerd vanwege de COVID-19-crisis.
>

[ExpressRoute](expressroute-introduction.md) is een Azure-service die privéconnectiviteit mogelijk maakt tussen Microsoft-datacenters en infrastructuur die zich op uw locatie of in een colocatiefaciliteit bevindt. ExpressRoute-verbindingen gaan niet via het openbare internet. Ze bieden veilige connectiviteit, betrouwbaarheid en snelheden, met lagere en consistente latencies dan typische verbindingen via internet.

## <a name="useful-links"></a>Handige koppelingen

* [Hoe de bandbreedte voor bestaande ExpressRoute circuit te verhogen](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [ExpressRoute monitoring, metrics en alerts](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Routeoptimalisatie via ExpressRoute](expressroute-optimize-routing.md)
* [Azure ExpressRoute voor O365](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Asymmetrische routeringsoverwegingen](expressroute-asymmetric-routing.md)
* [Een ondersteuningsaanvraag openen in de Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>Problemen oplossen

* [ExpressRoute-connectiviteit controleren](expressroute-troubleshooting-expressroute-overview.md)
* ARP-tabel krijgen in [Resource Manager](expressroute-troubleshooting-arp-resource-manager.md) en [Klassiek](expressroute-troubleshooting-arp-classic.md)
* [Een mislukt circuit opnieuw instellen](reset-circuit.md)
* [Problemen met de netwerkprestaties oplossen](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over ExpressRoute-connectiviteitsmodellen](expressroute-connectivity-models.md)
* Meer informatie over ExpressRoute-verbindingen en -routeringsdomeinen. Zie [ExpressRoute-circuits en routeringsdomeinen](expressroute-circuit-peerings.md)
* Zoek een serviceprovider Zie [ExpressRoute-partners en peeringlocaties](expressroute-locations.md)
* Controleer of aan alle vereisten is voldaan. Zie [ExpressRoute prerequisites](expressroute-prerequisites.md) (Vereisten voor ExpressRoute).
* [Een ExpressRoute-circuit maken en wijzigen](expressroute-howto-circuit-portal-resource-manager.md)
* [Peering voor een ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-portal-resource-manager.md)
* [Een virtueel netwerk verbinden met een ExpressRoute-circuit](expressroute-howto-linkvnet-portal-resource-manager.md)
