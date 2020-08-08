---
title: 'Scenario: route naar gedeelde services VNets'
titleSuffix: Azure Virtual WAN
description: "Scenario's voor route ring: routes instellen voor toegang tot een gedeeld service-VNet met een workload waarvan u wilt dat alle VNet en vertakkingen toegang hebben."
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/07/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 6045c491ea68d759b2a1739e20aa2f12b8520c87
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88006482"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Scenario: route naar gedeelde services VNets

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In dit scenario is het doel om routes in te stellen voor toegang tot een **gedeelde service** -VNet met werk belastingen waarvan u wilt dat alle VNet en vertakking (VPN/er/P2S) toegang hebben. Voor beelden van deze gedeelde werk belastingen zijn bijvoorbeeld Virtual Machines met services zoals domein controllers of bestands shares, of Azure-Services die intern worden weer gegeven via [Azure private-eind punten](../private-link/private-endpoint-overview.md).

Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.

## <a name="design"></a><a name="design"></a>Ontwerp

We kunnen een verbindings matrix gebruiken om de vereisten van dit scenario samen te vatten. In de matrix wordt in elke cel beschreven of een virtuele WAN-verbinding (de ' aan ' kant van de stroom, de rijkoppen in de tabel) een bestemmings voorvoegsel (de ' aan '-zijde van de stroom, de kolom koppen in de tabel cursief) voor een specifieke verkeers stroom.

**Verbindings matrix**

| Van             | Aan:   |*Geïsoleerde VNets*|*Gedeeld VNet*|*Vertakkingen*|
|---|---|---|---|---|
|**Geïsoleerde VNets**|&#8594;|                |        X        |       X      |
|**Gedeelde VNets**  |&#8594;|       X        |        X        |       X      |
|**Vertakkingen**      |&#8594;|       X        |        X        |       X      |

Net als bij het [geïsoleerde VNet-scenario](scenario-isolate-vnets.md)biedt deze verbindings matrix we twee verschillende rijtypen, die worden omgezet in twee route tabellen (de VNets van de gedeelde services en de vertakkingen hebben dezelfde connectiviteits vereisten). Virtuele WAN heeft al een standaard route tabel, dus we hebben een andere aangepaste route tabel nodig, waarin we **RT_SHARED** in dit voor beeld zullen aanroepen.

VNets wordt gekoppeld aan de **RT_SHARED** route tabel. Omdat ze connectiviteit met vertakkingen en de gedeelde service VNets nodig hebben, moeten de gedeelde service-VNet en-vertakkingen worden door gegeven aan **RT_SHARED** (anders zou de VNets de vertakking en gedeelde VNet-voor voegsels niet kunnen leren). Omdat de vertakkingen altijd zijn gekoppeld aan de standaard route tabel en de connectiviteits vereisten hetzelfde zijn voor VNets voor gedeelde services, zullen we de gedeelde service VNets ook koppelen aan de standaard route tabel.

Als gevolg hiervan is dit het laatste ontwerp:

* Geïsoleerde virtuele netwerken:
  * Gekoppelde route tabel: **RT_SHARED**
  * Door geven aan route tabellen: **standaard**
* Virtuele netwerken van gedeelde services:
  * Gekoppelde route tabel: **standaard**
  * Door geven aan route tabellen: **RT_SHARED** en **standaard**
* Sleutel
  * Gekoppelde route tabel: **standaard**
  * Door geven aan route tabellen: **RT_SHARED** en **standaard**

> [!NOTE]
> Als uw virtuele WAN wordt geïmplementeerd in meerdere regio's, moet u de **RT_SHARED** route tabel maken in elke hub, en routes van elke gedeelde services VNet en de vertakkings verbinding moeten worden door gegeven aan de route tabellen in elke virtuele hub met behulp van door gegeven labels.

Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.

## <a name="workflow"></a><a name="workflow"></a>Werkstroom

Houd rekening met de volgende stappen als u het scenario wilt configureren:

1. Identificeer het **gedeelde services** -VNet.
2. Een aangepaste route tabel maken. In het voor beeld verwijzen we naar de route tabel als **RT_SHARED**. Zie [virtuele hub-route ring configureren](how-to-virtual-hub-routing.md)voor een overzicht van de stappen voor het maken van een route tabel. Gebruik de volgende waarden als richt lijn:

   * **Organisatie**
     * Voor **VNets, *met uitzonde ring* van de Shared Services VNet**, selecteert u de VNets die u wilt isoleren. Dit betekent dat al deze VNets (met uitzonde ring van de Shared Services VNet) de bestemming kunnen bereiken op basis van de routes van RT_SHARED route tabel.

   * **Doorgifte**
      * Voor **vertakkingen**worden routes door gegeven aan deze route tabel, naast andere route tabellen die u mogelijk al hebt geselecteerd. Vanwege deze stap leert de RT_SHARED route tabel routes van alle vertakkings verbindingen (VPN/geen of gebruiker VPN).
      * Selecteer voor **VNets**de **Shared Services VNet**. Vanwege deze stap leert RT_SHARED route tabel routes van de VNet-verbinding van de gedeelde services.

Dit heeft tot gevolg dat de routerings configuratie wordt weer gegeven in de volgende afbeelding:

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="VNet Shared Services" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
