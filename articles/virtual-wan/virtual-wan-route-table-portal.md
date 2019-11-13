---
title: 'Virtueel WAN: route tabel voor virtuele hub maken naar NVA: Azure Portal'
description: Route tabel virtuele WAN-hub voor het stuur verkeer naar een virtueel netwerk apparaat via de portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 8f24b94226daffb769993c9f6659909fdff039b6
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014989"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Een route tabel voor virtuele WAN-hub maken voor Nva's: Azure Portal

In dit artikel wordt beschreven hoe u verkeer van een hub naar een virtueel netwerk apparaat (NVA) kunt sturen.

![Virtual WAN-diagram](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Voordat u begint

Controleer of u aan de volgende criteria hebt voldaan:

*  U hebt een virtueel netwerk apparaat (NVA). Een virtueel netwerk apparaat is een software van derden van uw keuze die doorgaans wordt ingericht vanuit Azure Marketplace in een virtueel netwerk.

    * Een privé-IP-adres moet worden toegewezen aan de NVA-netwerk interface.

    * De NVA is niet geïmplementeerd in de virtuele hub. Het moet worden geïmplementeerd in een afzonderlijk VNet.

    *  Er zijn mogelijk een of meer virtuele netwerken verbonden aan het NVA VNet. In dit artikel verwijzen we naar het NVA VNet als een ' indirecte spoke-VNet '. Deze VNets kunnen worden verbonden met het NVA VNet met behulp van VNet-peering.
*  U hebt 2 VNets gemaakt. Ze worden gebruikt als spoke VNets.

    * Voor deze oefening zijn de adres ruimten van de VNet-spoke: VNet1:10.0.2.0/24 en VNet2:10.0.3.0/24. Zie [een virtueel netwerk maken](../virtual-network/quick-create-portal.md)als u meer informatie nodig hebt over het maken van een VNet.

    * Zorg ervoor dat er geen virtuele netwerk gateways aanwezig zijn in een van de VNets.
    * Voor deze configuratie is voor deze VNets geen gateway-subnet vereist.

## <a name="signin"></a>1. aanmelden

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

## <a name="vwan"></a>2. een virtueel WAN maken

Maak een virtueel WAN. Voor de doel einden van deze oefening kunt u de volgende waarden gebruiken:

* **Virtuele WAN-naam:** myVirtualWAN
* **Resource groep:** testRG
* **Locatie:** VS-West

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. een hub maken

Maak de hub. Voor de doel einden van deze oefening kunt u de volgende waarden gebruiken:

* **Locatie:** VS-West
* **Naam:** westushub
* **Privé-adres ruimte van hub:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. een hub-route tabel maken en Toep assen

De hub bijwerken met een route tabel van de hub. Voor de doel einden van deze oefening kunt u de volgende waarden gebruiken:

* **VNet-adres ruimten voor indirecte spaken:** (VNet1 en VNet2) 10.0.2.0/24 en 10.0.3.0/24
* **Privé IP-adres van DMZ NVA Network Interface:** 10.0.4.5

1. Navigeer naar uw virtuele WAN.
2. Klik op de hub waarvoor u een route tabel wilt maken.
3. Klik op de **...** en klik vervolgens op **virtuele hub bewerken**.
4. Schuif op de pagina **virtuele hub bewerken** omlaag en selecteer de selectie vakje **tabel gebruiken voor route ring**.
5. Voeg in de kolom **als het voor voegsel** van de bestemming de adres ruimten toe. Voeg in de kolom **verzenden naar volgende hop** het privé IP-adres van de DMZ NVA-netwerk interface toe.
6. Klik op **bevestigen** om de resource hub bij te werken met de instellingen van de route tabel.

## <a name="connections"></a>5. de VNet-verbindingen maken

Maak een verbinding van elke indirecte spoke VNet (VNet1 en VNet2) naar de hub. Maak vervolgens een verbinding van de NVA VNet naar de hub.

 Voor deze stap kunt u de volgende waarden gebruiken:

| VNet-naam| Verbindingsnaam|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Herhaal de volgende procedure voor elk VNet dat u wilt verbinden.

1. Klik op de pagina voor uw virtuele WAN op **Virtuele netwerkverbinding**.
2. Klik op de pagina Virtuele netwerkverbinding op **+Verbinding toevoegen**.
3. Vul de volgende velden in op de pagina **Verbinding toevoegen**:

    * **Verbindingsnaam** - voer een naam in voor uw verbinding.
    * **Hubs** - selecteer de hub die u wilt koppelen aan deze verbinding.
    * **Abonnement** - controleer of het abonnement klopt.
    * **Virtueel netwerk** - selecteer het virtuele netwerk dat met deze hub wilt verbinden. Het virtuele netwerk mag geen bestaande virtuele netwerkgateway hebben.
4. Klik op **OK** om de verbinding te maken.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).