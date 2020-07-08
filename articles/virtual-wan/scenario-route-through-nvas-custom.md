---
title: "Scenario: verkeer routeren via Nva's met aangepaste instellingen"
titleSuffix: Azure Virtual WAN
description: Dit scenario helpt u bij het routeren van verkeer via Nva's met behulp van een andere NVA voor Internet verkeer.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3fcad59bcfb75b8de6af14eae7e0bcb4962b17eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568825"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Scenario: verkeer routeren via Nva's (preview)

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In dit scenario van NVA (virtueel netwerk apparaat) is het doel om verkeer te routeren via een NVA voor VNet <-> vertakking en een andere NVA te gebruiken voor Internet verkeer. Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over route ring van virtuele hub.

## <a name="scenario-architecture"></a><a name="architecture"></a>Scenario-architectuur

In **afbeelding 1**bevindt zich één hub, **hub 1**.

* **Hub 1** is rechtstreeks verbonden met NVA VNets **Vnet 4** en **vnet 5**.

* Verkeer tussen VNETs 1, 2 en 3 en branches (VPN/er/P2S) wordt naar verwachting via **VNET 4 NVA** 10.4.0.5.

* Alle Internet gebonden verkeer van VNETs 1, 2 en 3 wordt verwacht door te gaan via **VNET 5 NVA** 10.5.0.5.

**Afbeelding 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Afbeelding 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Scenario werk stroom

Als u route ring via NVA wilt instellen, kunt u overwegen de volgende stappen uit te voeren:

1. Om het Internet-gebonden verkeer te laten door lopen via VNET5, hebt u VNETs 1, 2 en 3 nodig om rechtstreeks verbinding te maken via VNet-peering met VNET 5. U moet ook een UDR instellen in de VNets voor 0.0.0.0/0 en de 10.5.0.5 van de volgende hop. Op dit moment staat Virtual WAN geen volgende hop-NVA in de virtuele hub voor 0.0.0.0/0 toe.

1. In de Azure Portal gaat u naar uw virtuele hub en maakt u een aangepaste route tabel **RT_Shared** waarin routes worden weer gegeven via door geven van alle VNets-en vertakkings verbindingen. In **afbeelding 2**wordt dit weer gegeven als een lege aangepaste Route tabel **RT_Shared**.

   * **Routes:** U hoeft geen statische routes toe te voegen.

   * **Koppeling:** Selecteer VNETs 4 en 5. Dit betekent dat de VNETs 4-en 5-verbindingen die zijn gekoppeld aan de route tabel **RT_Shared**.

   * **Doorgifte:** Omdat u wilt dat alle vertakkingen en VNet-verbindingen hun routes dynamisch door geven aan deze route tabel, selecteert u vertakkingen en alle VNets.

1. Maak een aangepaste route tabel **RT_V2B** voor het omleiden van verkeer van VNETs 1, 2 en 3 naar branches.

   * **Routes:** Voeg een geaggregeerde statische route vermelding toe voor vertakkingen (VPN/er/P2S) (10.2.0.0/16 in **afbeelding 2**) met de volgende hop als de VNET 4-verbinding. U moet ook een statische route configureren in de verbinding van VNET 4 voor de vertakkings voorvoegsels en aangeven dat de volgende hop het specifieke IP-adres van de NVA in VNET 4 is.

   * **Koppeling:** Selecteer alle VNETs 1, 2 en 3. Dit betekent dat de VNet-verbindingen 1, 2 en 3 aan deze route tabel zullen worden gekoppeld en dat er routes (statisch en dynamisch via doorgifte) in deze route tabel kunnen worden weer gegeven.

   * **Doorgifte:** Verbindingen geven routes door aan route tabellen. Als u VNETs 1, 2 en 3 selecteert, worden de routes van VNETs 1, 2 en 3 aan deze route tabel door gegeven. U hoeft geen routes van de vertakkings verbindingen naar RT_V2B door te geven, omdat vertakkings-VNet-verkeer via de NVA in en VNET4 gaat.
  
1. Bewerk de **DefaultRouteTable**voor de standaard route tabel.

   Alle VPN-, ExpressRoute-en gebruikers-VPN-verbindingen zijn gekoppeld aan de standaard route tabel. Alle VPN-, ExpressRoute-en gebruikers-VPN-verbindingen sturen routes door naar dezelfde set route tabellen.

   * **Routes:** Voeg een geaggregeerde statische route vermelding toe voor VNETs 1, 2 en 3 (10.1.0.0/16 in **afbeelding 2**) met de volgende hop als de VNET 4-verbinding. U moet ook een statische route configureren in de verbinding van VNET 4 voor VNET 1, 2 en 3 geaggregeerde voor voegsels en aangeven dat de volgende hop het specifieke IP-adres van de NVA in VNET 4 is.

   * **Koppeling:** Zorg ervoor dat de optie voor vertakkingen (VPN/er/P2S) is geselecteerd, zodat de on-premises vertakkings verbindingen worden gekoppeld aan de *defaultroutetable*.

   * **Doorgifte van:** Zorg ervoor dat de optie voor vertakkingen (VPN/er/P2S) is geselecteerd, zodat op de lokale verbindingen routes worden door gegeven aan de *defaultroutetable*.

**Afbeelding 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Afbeelding 2":::

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
