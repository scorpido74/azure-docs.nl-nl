---
title: 'Scenario: VNets isoleren'
titleSuffix: Azure Virtual WAN
description: Scenario's voor het isoleren van VNets
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: f43f17a0f3742831920836e448de3ef757f2dfa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568523"
---
# <a name="scenario-isolating-vnets"></a>Scenario: VNets isoleren

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In dit scenario is het doel om te voor komen dat VNets andere kunnen bereiken. Dit staat bekend als het isoleren van VNets. De werk belasting binnen het VNet blijft geïsoleerd en kan niet communiceren met andere VNets, zoals in een wille keurig scenario. De VNets zijn echter vereist om alle branches te bereiken (VPN, er en gebruikers-VPN). In dit scenario zijn alle VPN-, ExpressRoute-en gebruikers-VPN-verbindingen gekoppeld aan dezelfde en één route tabel. Alle VPN-, ExpressRoute-en gebruikers-VPN-verbindingen sturen routes door naar dezelfde set route tabellen. Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over route ring van virtuele hub.

## <a name="scenario-workflow"></a><a name="workflow"></a>Scenario werk stroom

Als u dit scenario wilt configureren, voert u de volgende stappen uit:

1. Een aangepaste route tabel maken. In het voor beeld is de route tabel **RT_VNet**. Zie [virtuele hub-route ring configureren voor meer informatie over](how-to-virtual-hub-routing.md)het maken van een route tabel. Zie [about virtuele hub Routing](about-virtual-hub-routing.md)voor meer informatie over route tabellen.
2. Wanneer u de **RT_VNet** route tabel maakt, configureert u de volgende instellingen:

   * **Koppeling**: Selecteer de VNets die u wilt isoleren.
   * **Doorgifte**: Selecteer de optie voor vertakkingen, die vertakkingen (VPN/er/P2S)-verbindingen geven, worden routes door gegeven aan deze route tabel.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="Geïsoleerde VNets":::

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
