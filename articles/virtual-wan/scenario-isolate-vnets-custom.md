---
title: 'Scenario: aangepaste isolatie voor VNets'
titleSuffix: Azure Virtual WAN
description: "Scenario's voor route ring: hiermee voor komt u dat geselecteerde VNets elkaar kunnen bereiken"
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3719956df0dce62ee69d8e306ff2cad27197616d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568525"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Scenario: aangepaste isolatie voor VNets

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In een aangepast isolatie scenario voor VNets is het doel om te voor komen dat specifieke sets van VNets een andere set VNets kunnen bereiken. De VNets zijn echter vereist om alle branches te bereiken (VPN/er/gebruikers-VPN).

In dit scenario worden VPN-, ExpressRoute-en gebruikers-VPN-verbindingen (samen met de naam branches) gekoppeld aan dezelfde route tabel (standaard route tabel). Alle VPN-, ExpressRoute-en gebruikers-VPN-verbindingen sturen routes door naar dezelfde set route tabellen. Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.

## <a name="scenario-workflow"></a><a name="architecture"></a>Scenario werk stroom

In **afbeelding 1**zijn er blauwe en rode VNet-verbindingen.

* Blue-verbonden VNets kunnen elkaar bereiken en alle vertakkingen (VPN/er/P2S) bereiken.
* Rode VNets kan elkaar bereiken en alle filialen (VPN/er/P2S)-verbindingen bereiken.

Houd rekening met de volgende stappen bij het instellen van route ring.

1. Maak twee aangepaste route tabellen in de Azure Portal, **RT_BLUE** en **RT_RED**.
2. Voor route tabel **RT_BLUE**onder:
   * **Koppeling**: alle blauwe VNets selecteren
   * **Doorgifte**: voor vertakkingen selecteert u de optie voor branches, die vertakkingen (VPN/er/P2S)-verbindingen geven, worden routes door gegeven aan deze route tabel.
3. Herhaal dezelfde stappen voor **RT_RED** route tabel voor rode VNets en vertakkingen (VPN/er/P2S).

Dit heeft tot gevolg dat de routerings configuratie wordt gewijzigd, zoals in de onderstaande afbeelding wordt weer gegeven

**Afbeelding 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="afbeelding 1":::

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
