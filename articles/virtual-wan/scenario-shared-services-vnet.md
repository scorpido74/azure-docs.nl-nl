---
title: 'Scenario: route naar gedeelde services VNets'
titleSuffix: Azure Virtual WAN
description: "Scenario's voor route ring: routes instellen voor toegang tot een gedeeld service-VNet met een workload waarvan u wilt dat alle VNet en vertakkingen toegang hebben."
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 64bb4c85399f811c0ab7ff84b297b64734efc491
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568821"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Scenario: route naar gedeelde services VNets

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In dit scenario is het doel om routes in te stellen voor toegang tot een **gedeelde service** -VNet met een werk belasting waarvan u wilt dat alle VNet en vertakking (VPN/er/P2S) toegang hebben. Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over route ring van virtuele hub.

## <a name="scenario-workflow"></a><a name="workflow"></a>Scenario werk stroom

Als u dit scenario wilt configureren, voert u de volgende stappen uit:

1. Identificeer het **gedeelde services** -VNet.
2. Een aangepaste route tabel maken. In het voor beeld verwijzen we naar deze route tabel als **RT_SHARED**. Zie [virtuele hub-route ring configureren](how-to-virtual-hub-routing.md)voor een overzicht van de stappen voor het maken van een route tabel. Gebruik de volgende waarden als richt lijn:

   * **Organisatie**
     * Voor **VNets, *met uitzonde ring* van de Shared Services VNet**, selecteert u de VNets die u wilt isoleren. Dit betekent dat al deze VNets (met uitzonde ring van de Shared Services VNet) de bestemming kunnen bereiken op basis van de routes van RT_SHARED route tabel.

   * **Doorgifte**
      * Voor **vertakkingen**worden routes door gegeven aan deze route tabel, naast andere route tabellen die u mogelijk al hebt geselecteerd. Vanwege deze stap leert RT_SHARED route tabel routes van alle vertakkings verbindingen (VPN/er/gebruiker VPN).
      * Selecteer voor **VNets**de **Shared Services VNet**. Vanwege deze stap leert RT_SHARED route tabel routes van de VNet-verbinding van de gedeelde services.

     Dit heeft tot gevolg dat de routerings configuratie wordt gewijzigd, zoals in de onderstaande afbeelding wordt weer gegeven

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="VNet Shared Services":::

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
