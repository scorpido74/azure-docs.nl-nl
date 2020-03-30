---
title: Informatie over redundante virtuele netwerkgateways in Azure Availability Zones
description: Meer informatie over VPN Gateway- en ExpressRoute-gateways in beschikbaarheidszones.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: cherylmc
ms.openlocfilehash: f1bbaab99b6422de4053839e2099869d2d08db95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864295"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Informatie over redundante virtuele netwerkgateways in Azure Availability Zones

U VPN- en ExpressRoute-gateways implementeren in [Azure Availability Zones.](../availability-zones/az-overview.md) Dit zorgt in een virtuele netwerkgateway voor tolerantie, schaalbaarheid en hoge beschikbaarheid. Gateways fysiek en logisch implementeren in Azure-beschikbaarheidszones scheidt gateways binnen een regio, terwijl uw on-premises netwerkconnectiviteit met Azure wordt beschermd tegen fouten op zoneniveau.

### <a name="zone-redundant-gateways"></a><a name="zrgw"></a>Zone-redundante gateways

Als u uw virtuele netwerkgateways automatisch wilt implementeren in beschikbaarheidszones, u zoneredundante virtuele netwerkgateways gebruiken. Met zoneredundante gateways u profiteren van zonetolerantie om toegang te krijgen tot uw bedrijfskritieke, schaalbare services op Azure.

<br>
<br>

![zoneredundante gateways-afbeelding](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zonal-gateways"></a><a name="zgw"></a>Zonale gateways

Als u gateways in een bepaalde zone wilt implementeren, u zonale gateways gebruiken. Wanneer u een zonale gateway implementeert, worden alle exemplaren van de gateway geïmplementeerd in dezelfde beschikbaarheidszone.

<br>
<br>

![afbeelding van zonale gateways](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gateway-skus"></a><a name="gwskus"></a>Gateway-SKU's

Zone-redundante en zonale gateways zijn beschikbaar als nieuwe gateway SKU's. We hebben nieuwe virtuele netwerkgateway SKU's toegevoegd in Azure AZ-regio's. Deze SKU's zijn vergelijkbaar met de overeenkomstige bestaande SKU's voor ExpressRoute en VPN Gateway, behalve dat ze specifiek zijn voor zoneredundante en zonale gateways. U deze SKU's identificeren aan de AZ in de SKU-naam.

Zie [VPN-gateway SKU's](vpn-gateway-about-vpngateways.md#gwsku) en [ExpressRoute-gateway SKU's voor](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku)informatie over gateway-SKU's.

## <a name="public-ip-skus"></a><a name="pipskus"></a>Openbare IP-SKU's

Zoneredundante gateways en zonale gateways zijn beide afhankelijk van de Azure public IP-bron *Standard* SKU. De configuratie van de openbare IP-bron van Azure bepaalt of de gateway die u implementeert zoneredundant of zonale is. Als u een openbare IP-bron maakt met een Basis-SKU, heeft de gateway geen redundantie in de zone en zijn de gatewaybronnen regionaal. *Basic*

### <a name="zone-redundant-gateways"></a><a name="pipzrg"></a>Zone-redundante gateways

Wanneer u een openbaar IP-adres maakt met de **standaard** openbare IP-SKU zonder een zone op te geven, verschilt het gedrag afhankelijk van of de gateway een VPN-gateway of een ExpressRoute-gateway is. 

* Voor een VPN-gateway worden de twee gateway-exemplaren geïmplementeerd in twee van deze drie zones om zoneredundantie te bieden. 
* Voor een ExpressRoute-gateway, omdat er meer dan twee exemplaren kunnen zijn, kan de gateway zich over alle drie de zones uitstrekken.

### <a name="zonal-gateways"></a><a name="pipzg"></a>Zonale gateways

Wanneer u een openbaar IP-adres maakt met de **standaard** openbare IP-SKU en de zone (1, 2 of 3) opgeeft, worden alle gateway-exemplaren in dezelfde zone geïmplementeerd.

### <a name="regional-gateways"></a><a name="piprg"></a>Regionale gateways

Wanneer u een openbaar IP-adres maakt met de **Basic** public IP SKU, wordt de gateway geïmplementeerd als een regionale gateway en is er geen zoneredundantie ingebouwd in de gateway.

## <a name="faq"></a><a name="faq"></a>Veelgestelde vragen

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Wat verandert er als ik deze nieuwe SKU's implementeer?

Vanuit uw perspectief u uw gateways implementeren met zoneredundantie. Dit betekent dat alle exemplaren van de gateways worden geïmplementeerd in Azure Availability Zones en dat elke beschikbaarheidszone een ander fout- en updatedomein is. Dit maakt uw gateways betrouwbaarder, beschikbaar en bestandtegen zonefouten.

### <a name="can-i-use-the-azure-portal"></a>Kan ik de Azure-portal gebruiken?

Ja, u de Azure-portal gebruiken om de nieuwe SKU's te implementeren. U ziet deze nieuwe SKU's echter alleen in azure-regio's met Azure-beschikbaarheidszones.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Welke regio's zijn beschikbaar voor mij om de nieuwe SKU's te gebruiken?

De nieuwe SKU's zijn beschikbaar in Azure-regio's met Azure Availability Zones - Centraal VS, Frankrijk Centraal, Noord-Europa, West-Europa en West-VS 2 regio's, Oost-VS, Oost-VS 2, Zuidoost-Azië, Japan East, UK South. In de toekomst maken we zoneredundante gateways beschikbaar in andere Azure Public Regions.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Kan ik mijn bestaande virtuele netwerkgateways wijzigen/migreren/upgraden naar zoneredundante of zonale gateways?

Het migreren van uw bestaande virtuele netwerkgateways naar zoneredundante of zonale gateways wordt momenteel niet ondersteund. U echter uw bestaande gateway verwijderen en een zoneredundante of zonale gateway opnieuw maken.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Kan ik zowel VPN- als Express Route-gateways implementeren in hetzelfde virtuele netwerk?

Coëxistentie van zowel VPN- als Express Route-gateways in hetzelfde virtuele netwerk wordt ondersteund. U moet echter een IP-adresbereik van / 27 reserveren voor het gateway-subnet.

## <a name="next-steps"></a>Volgende stappen

[Een zone-redundante virtuele netwerkgateway maken](create-zone-redundant-vnet-gateway.md)
