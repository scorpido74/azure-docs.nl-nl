---
title: 'Scenario: any-to-any'
titleSuffix: Azure Virtual WAN
description: "Scenario's voor route ring: any-to-any"
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ecc2b3cf236cb2a78fd595189649e7f6b176d709
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568528"
---
# <a name="scenario-any-to-any"></a>Scenario: any-to-any

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In een wille keurig scenario kan elke spoke een andere spoke bereiken. Als er meerdere hubs bestaan, is hub-naar-hub-route ring (ook wel inter-hub genoemd) standaard ingeschakeld in standaard virtueel WAN. 

In dit scenario worden VPN-, ExpressRoute-en gebruikers-VPN-verbindingen aan dezelfde route tabel gekoppeld. Alle VPN-, ExpressRoute-en gebruikers-VPN-verbindingen sturen routes door naar dezelfde set route tabellen. Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.

## <a name="scenario-architecture"></a><a name="architecture"></a>Scenario-architectuur

In **afbeelding 1**kunnen alle VNets en branches (VPN, EXPRESSROUTE, P2S) elkaar bereiken. In een virtuele hub werken verbindingen als volgt:

* Een VPN-verbinding verbindt een VPN-site met een VPN-gateway.
* Een virtuele netwerk verbinding verbindt een virtueel netwerk met een virtuele hub. De router van de virtuele hub biedt de doorvoer functionaliteit tussen VNets.
* Een ExpressRoute-verbinding verbindt een ExpressRoute-circuit met een ExpressRoute-gateway.

Deze verbindingen (standaard bij het maken) zijn gekoppeld aan de standaard route tabel, tenzij u de routerings configuratie van de verbinding instelt op **geen**of een aangepaste route tabel. Deze verbindingen geven routes ook standaard door aan de standaard route tabel. Dit biedt een wille keurig scenario waarbij elke spoke (VNet, VPN, er, P2S) elkaar kan bereiken.

**Afbeelding 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="afbeelding 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Scenario werk stroom

Dit scenario is standaard ingeschakeld voor virtuele WAN. Als de instelling voor vertakking-naar-vertakking is uitgeschakeld in de WAN-configuratie, wordt de verbinding tussen vertakkings Spaak niet toegestaan. VPN/ExpressRoute/gebruiker VPN worden beschouwd als vertakkings spaak in virtueel WAN

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
