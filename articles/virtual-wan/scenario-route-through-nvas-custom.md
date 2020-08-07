---
title: "Scenario: verkeer routeren via Nva's met aangepaste instellingen"
titleSuffix: Azure Virtual WAN
description: Dit scenario helpt u bij het routeren van verkeer via Nva's met behulp van een andere NVA voor Internet verkeer.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 5546fc63b01d1da6b4033e071ac071574ab9699a
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987193"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Scenario: verkeer routeren via Nva's (preview)

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In dit scenario van NVA (virtueel netwerk apparaat) is het doel om verkeer te routeren via een NVA voor communicatie tussen VNets en filialen, en een ander NVA te gebruiken voor Internet-afhankelijk verkeer. Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.

## <a name="design"></a><a name="design"></a>Ontwerp

In dit scenario gebruiken we de naamgevings Conventie:

* ' Service-VNet ' voor virtuele netwerken waarin gebruikers een NVA (VNet 4 in **afbeelding 1**) hebben geïmplementeerd om niet-Internet verkeer te controleren.
* ' DMZ VNet ' voor virtuele netwerken waarbij gebruikers een NVA hebben geïmplementeerd die moeten worden gebruikt voor het controleren van het Internet-gebonden verkeer (VNet 5 in **afbeelding 1**).
* ' NVA spokes ' voor virtuele netwerken die zijn verbonden met een NVA VNet (VNet 1, VNet 2 en VNet 3 in **afbeelding 1**).
* Hubs voor door micro soft beheerde virtuele WAN-hubs.

De volgende verbindings matrix bevat een overzicht van de stromen die in dit scenario worden ondersteund:

**Verbindings matrix**

| Van          | Aan:|*NVA-spokes*|*Service-VNet*|*DMZ VNet*|*Statische vertakkingen*|
|---|---|---|---|---|---|
| **NVA-spokes**| &#8594;|      X |            X |   Peering |    Statisch    |
| **Service-VNet**| &#8594;|    X |            X |      X    |      X       |
| **DMZ VNet** | &#8594;|       X |            X |      X    |      X       |
| **Vertakkingen** | &#8594;|  Statisch |            X |      X    |      X       |

Elk van de cellen in de verbindings matrix beschrijft of een virtuele WAN-verbinding (de ' aan ' kant van de stroom, de rijkoppen) een bestemmings voorvoegsel (de ' aan '-zijde van de stroom, de kolom koppen cursief) voor een specifieke verkeers stroom leert. Laten we de verschillende rijen in detail bekijken:

* NVA spaken:
  * Spokes bereiken rechtstreeks via virtuele WAN-hubs andere spokes.
  * Spokes krijgen verbindingen met vertakkingen via een statische route die verwijst naar het service-VNet. Ze moeten geen specifieke voor voegsels van de vertakkingen kennen (anders is dat iets meer specifiek en wordt de samen vatting overschreven).
  * Spokes verzenden via een directe VNet-peering Internet verkeer naar het DMZ VNet.
* Sleutel
  * Vertakkingen krijgen toegang tot spokes via een statische route ring die verwijst naar het service-VNet. Ze moeten geen specifieke voor voegsels van de VNets leren die de samenvatte statische route overschrijven.
* Het service-VNet is vergelijkbaar met een VNet voor gedeelde services dat bereikbaar moet zijn vanuit elk VNet en elke vertakking.
* Het DMZ VNet hoeft niet echt verbinding te hebben via Virtual WAN, omdat het enige verkeer dat het ondersteunt, meer dan direct VNet-peerings zal zijn. We gebruiken echter hetzelfde connectiviteits model als voor de DMZ VNet om de configuratie te vereenvoudigen.

Daarom biedt onze verbindings matrix ons drie verschillende verbindings patronen, die worden omgezet in drie route tabellen. De koppelingen met de verschillende VNets zijn als volgt:

* NVA spaken:
  * Gekoppelde route tabel: **RT_V2B**
  * Door geven aan route tabellen: **RT_V2B** en **RT_SHARED**
* NVA VNets (intern en Internet):
  * Gekoppelde route tabel: **RT_SHARED**
  * Door geven aan route tabellen: **RT_SHARED**
* Sleutel
  * Gekoppelde route tabel: **standaard**
  * Door geven aan route tabellen: **RT_SHARED** en **standaard**

We hebben deze statische routes nodig om ervoor te zorgen dat VNet-naar-vertakking-en vertakkings-naar-VNet-verkeer via de NVA in het service-VNet (VNet 4):

| Beschrijving | Routetabel | Statische route              |
| ----------- | ----------- | ------------------------- |
| Vertakkingen    | RT_V2B      | 10.2.0.0/16-> vnet4conn  |
| NVA-spokes  | Standaard     | 10.1.0.0/16-> vnet4conn  |

Het virtuele WAN weet nu naar welke verbinding de pakketten moeten worden verzonden, maar de verbinding moet weten wat er moet gebeuren wanneer deze pakketten worden ontvangen: hier worden de verbindings route tabellen gebruikt.

| Beschrijving | Verbinding | Statische route            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16-> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16-> 10.4.0.5 |

Op dit moment moet alles aanwezig zijn.

Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.

## <a name="architecture"></a><a name="architecture"></a>Architectuur

In **afbeelding 1**bevindt zich één hub, **hub 1**.

* **Hub 1** is rechtstreeks verbonden met NVA VNets **Vnet 4** en **vnet 5**.

* Verkeer tussen VNets 1, 2 en 3 en branches (VPN/er/P2S) wordt naar verwachting via **VNet 4 NVA** 10.4.0.5.

* Alle Internet gebonden verkeer van VNets 1, 2 en 3 wordt verwacht door te gaan via **VNet 5 NVA** 10.5.0.5.

**Afbeelding 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Afbeelding 1":::

## <a name="workflow"></a><a name="workflow"></a>Werkstroom

Als u route ring via NVA wilt instellen, kunt u overwegen de volgende stappen uit te voeren:

1. Als u Internet-gebonden verkeer wilt gaan via VNet 5, hebt u VNets 1, 2 en 3 nodig om rechtstreeks verbinding te maken via VNet-peering met VNet 5. U moet ook een UDR instellen in de VNets voor 0.0.0.0/0 en de 10.5.0.5 van de volgende hop. Op dit moment staat Virtual WAN geen volgende hop-NVA in de virtuele hub voor 0.0.0.0/0 toe.

1. In de Azure Portal gaat u naar uw virtuele hub en maakt u een aangepaste route tabel **RT_Shared** waarin routes worden weer gegeven via door geven van alle VNets-en vertakkings verbindingen. In **afbeelding 2**wordt dit weer gegeven als een lege aangepaste Route tabel **RT_Shared**.

   * **Routes:** U hoeft geen statische routes toe te voegen.

   * **Koppeling:** Selecteer VNets 4 en 5. Dit betekent dat de VNets 4-en 5-verbindingen die zijn gekoppeld aan de route tabel **RT_Shared**.

   * **Doorgifte:** Omdat u wilt dat alle vertakkingen en VNet-verbindingen hun routes dynamisch door geven aan deze route tabel, selecteert u vertakkingen en alle VNets.

1. Maak een aangepaste route tabel **RT_V2B** voor het omleiden van verkeer van VNets 1, 2 en 3 naar branches.

   * **Routes:** Voeg een geaggregeerde statische route vermelding toe voor vertakkingen (VPN/er/P2S) (10.2.0.0/16 in **afbeelding 2**) met de volgende hop als de VNet 4-verbinding. U moet ook een statische route configureren in de verbinding van VNet 4 voor de vertakkings voorvoegsels en aangeven dat de volgende hop het specifieke IP-adres van de NVA in VNet 4 is.

   * **Koppeling:** Selecteer alle VNets 1, 2 en 3. Dit betekent dat de VNet-verbindingen 1, 2 en 3 aan deze route tabel zullen worden gekoppeld en dat er routes (statisch en dynamisch via doorgifte) in deze route tabel kunnen worden weer gegeven.

   * **Doorgifte:** Verbindingen geven routes door aan route tabellen. Als u VNets 1, 2 en 3 selecteert, worden de routes van VNets 1, 2 en 3 aan deze route tabel door gegeven. U hoeft geen routes van vertakkings verbindingen naar RT_V2B door te geven, omdat vertakkings-VNet-verkeer via de NVA in VNet 4 gaat.
  
1. Bewerk de **DefaultRouteTable**voor de standaard route tabel.

   Alle VPN-, ExpressRoute-en gebruikers-VPN-verbindingen zijn gekoppeld aan de standaard route tabel. Alle VPN-, ExpressRoute-en gebruikers-VPN-verbindingen sturen routes door naar dezelfde set route tabellen.

   * **Routes:** Voeg een geaggregeerde statische route vermelding toe voor VNets 1, 2 en 3 (10.1.0.0/16 in **afbeelding 2**) met de volgende hop als de VNet 4-verbinding. U moet ook een statische route configureren in de verbinding van VNet 4 voor VNet 1, 2 en 3 geaggregeerde voor voegsels en aangeven dat de volgende hop het specifieke IP-adres van de NVA in VNet 4 is.

   * **Koppeling:** Zorg ervoor dat de optie voor vertakkingen (VPN/er/P2S) is geselecteerd, zodat de on-premises vertakkings verbindingen worden gekoppeld aan de *defaultroutetable*.

   * **Doorgifte van:** Zorg ervoor dat de optie voor vertakkingen (VPN/er/P2S) is geselecteerd, zodat op de lokale verbindingen routes worden door gegeven aan de *defaultroutetable*.

**Afbeelding 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Afbeelding 2" lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
