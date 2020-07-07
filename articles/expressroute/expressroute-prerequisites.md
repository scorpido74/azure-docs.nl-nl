---
title: 'Azure-ExpressRoute: vereisten'
description: Deze pagina bevat een lijst met vereisten waaraan moet worden voldaan voordat u een Azure ExpressRoute-circuit kunt bestellen. Het bevat een controle lijst.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.openlocfilehash: a72eba9bde0745e66bdf8e7efd8eaec7d6a0b186
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74083360"
---
# <a name="expressroute-prerequisites--checklist"></a>Vereisten en controlelijst voor ExpressRoute
Als u ExpressRoute wilt gebruiken om verbinding te maken met Microsoft Cloud-services, moet u controleren of er is voldaan aan de vereisten die in de volgende secties worden genoemd.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure-account
* Een geldig en actief Microsoft Azure-account. Dit account is vereist om het ExpressRoute-circuit in te stellen. ExpressRoute-circuits zijn resources in Azure-abonnementen. Een Azure-abonnement is een vereiste, zelfs als connectiviteit is beperkt tot niet-Azure micro soft-Cloud Services, zoals Office 365.
* Een actief Office 365-abonnement (als u Office 365-services gebruikt). Voor meer informatie raadpleegt u de sectie Specifieke vereisten voor Office 365 in dit artikel.

## <a name="connectivity-provider"></a>Connectiveitsprovider

* U kunt met een [ExpressRoute-connectiviteitspartner](expressroute-locations.md#partners) werken om verbinding te maken met de Microsoft Cloud. U kunt op [drie manieren](expressroute-introduction.md) een verbinding instellen tussen uw on-premises netwerk en Microsoft.
* Als uw provider geen ExpressRoute-connectiviteitspartner is, kunt u via een [cloudexchange-provider](expressroute-locations.md#connectivity-through-exchange-providers) verbinding maken met de Microsoft Cloud.

## <a name="network-requirements"></a>Netwerkvereisten
* **Redundantie op elke peering-locatie**: micro soft vereist dat REDUNDANTe BGP-sessies worden ingesteld tussen de routers van micro soft en de peering routers op elk ExpressRoute-circuit (zelfs wanneer u slechts [één fysieke verbinding hebt met een Cloud-Exchange](expressroute-faqs.md#onep2plink)).
* **Redundantie voor nood herstel**: micro soft raadt u ten zeerste aan om ten minste twee ExpressRoute-circuits in verschillende peering-locaties in te stellen om een single point of failure te voor komen.
* **Routering**: afhankelijk van hoe u verbinding maakt met de Microsoft Cloud, moet u of uw provider de BGP-sessies voor [routeringsdomeinen](expressroute-circuit-peerings.md) instellen en beheren. Sommige Ethernet-connectiviteitsproviders of cloudexchange-providers bieden BGP-beheer aan als extra service.
* **NAT**: Microsoft accepteert alleen openbare IP-adressen via Microsoft-peering. Als u privé IP-adressen in uw on-premises netwerk gebruikt, moet u of uw provider de privé IP-adressen [met behulp van de NAT](expressroute-nat.md) vertalen naar openbare IP-adressen.
* **QoS**: Skype voor Bedrijven heeft verschillende services (zoals spraak, video, tekst) die allemaal een andere QoS-behandeling vereisen. U en uw provider moeten de [QoS-vereisten](expressroute-qos.md) volgen.
* **Netwerkbeveiliging**: overweeg [netwerkbeveiliging](../best-practices-network-security.md) als u via ExpressRoute verbinding maakt met de Microsoft Cloud.

## <a name="office-365"></a>Office 365
Als u Office 365 op ExpressRoute wilt inschakelen, raadpleegt u de volgende documenten voor meer informatie over de vereisten voor Office 365.

* [Overview of ExpressRoute for Office 365 (Overzicht van ExpressRoute voor Office 365)](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Routing with ExpressRoute for Office 365 (Routering met ExpressRoute voor Office 365)](https://support.office.com/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Hoge beschikbaarheid en failover met ExpressRoute](https://aka.ms/erhighavailability)
* [Office 365-URL's en IP-adresbereiken](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Network planning and performance tuning for Office 365 (Netwerkplanning en prestatieafstemming voor Office 365)](https://support.office.com/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Network bandwidth calculators and tools (Calculators en hulpmiddelen voor het berekenen van de netwerkbandbreedte)](https://support.office.com/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Office 365 integration with on-premises environments (Office 365-integratie met on-premises omgevingen)](https://support.office.com/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Geavanceerde trainingsvideo's voor ExpressRoute in Office 365](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Volgende stappen
* Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md)voor meer informatie over ExpressRoute.
* Zoek een ExpressRoute-connectiviteitsprovider. Zie [ExpressRoute partners and peering locations](expressroute-locations.md) (ExpressRoute-partners en -peeringlocaties).
* Raadpleeg de vereisten voor [Routering](expressroute-routing.md), [NAT](expressroute-nat.md) en [QoS](expressroute-qos.md).
* Configureer uw ExpressRoute-verbinding.
  * [Een ExpressRoute-circuit maken](expressroute-howto-circuit-arm.md)
  * [Routering configureren](expressroute-howto-routing-arm.md)
  * [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
