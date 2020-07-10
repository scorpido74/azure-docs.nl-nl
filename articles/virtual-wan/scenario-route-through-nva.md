---
title: 'Scenario: verkeer routeren via een NVA'
titleSuffix: Azure Virtual WAN
description: Verkeer routeren via een NVA
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed64b9d281cfbbf8202a99335ea2759b27a6fc42
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142961"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Scenario: verkeer routeren via een NVA

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In dit NVA-scenario is het doel om verkeer te routeren via een NVA (virtueel netwerk apparaat) voor vertakking naar VNet en VNet naar vertakking. Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over route ring van virtuele hub.

> [!NOTE]
> Sommige van de routerings mogelijkheden kunnen nog steeds worden geïmplementeerd. Als de implementatie nog niet in uw regio is gebeurd, moet u de stappen in deze versies van de artikelen in de tussen tijd gebruiken:
>* [Azure Portal artikel](virtual-wan-route-table-nva-portal.md)
>* [Power shell-artikel](virtual-wan-route-table-nva.md)
>

## <a name="scenario-architecture"></a><a name="architecture"></a>Scenario-architectuur

In **afbeelding 1**zijn er twee hubs; **Hub 1** en **hub 2**.

* **Hub 1** en **hub 2** zijn rechtstreeks verbonden met NVA VNets **VNET 2** en **VNET 4**.

* **Vnet 5** en **vnet 6** zijn gekoppeld aan **vnet 2**.

* **Vnet 7** en **vnet 8** zijn gekoppeld aan **vnet 4**.

* **VNETs 5, 6, 7, 8** zijn indirecte spokes, niet rechtstreeks verbonden met een virtuele hub.

**Afbeelding 1**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Afbeelding 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Scenario werk stroom

Als u route ring via NVA wilt instellen, kunt u overwegen de volgende stappen uit te voeren:

1. Identificeer de NVA spoke VNet-verbinding. In **afbeelding 1**zijn dit **VNET 2-verbinding (Eastusconn)** en **vnet 4-verbinding (weconn)**.

   Zorg ervoor dat er Udr's zijn ingesteld:
   * Van VNET 5 en 6 tot VNET 2 NVA IP
   * Van VNET 7 en 8 tot VNET 4 NVA IP 
   
   U hoeft VNET 5, 6, 7 en 8 niet rechtstreeks aan de virtuele hubs te koppelen. Zorg ervoor dat Nsg's in VNETs 5, 6, 7, 8 verkeer toestaan voor Branch (VPN/er/P2S) of VNETs die is verbonden met hun externe VNETs. VNET 5, 6 moet bijvoorbeeld waarborgen dat Nsg's verkeer toestaan voor on-premises adres voorvoegsels en VNETs 7, 8 die zijn verbonden met de externe hub 2. 

2. Voeg een geaggregeerde statische route vermelding toe voor de standaard route tabel van VNETs 2, 5, 6 naar hub 1. 

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Voorbeeld":::

3. Configureer een statische route voor VNETs 5, 6 in de virtuele netwerk verbinding van VNET 2. Zie [virtuele hub-route ring](how-to-virtual-hub-routing.md#routing-configuration)voor het instellen van routerings configuratie voor een virtuele netwerk verbinding.

4. Voeg een geaggregeerde statische route vermelding toe voor de standaard route tabel van VNETs 4, 7, 8 naar hub 1.

5. Herhaal stap 2, 3 en 4 voor de standaard route tabel van hub 2.

Dit heeft tot gevolg dat de routerings configuratie wordt gewijzigd, zoals in de onderstaande afbeelding wordt weer gegeven

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Resultaat":::

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
