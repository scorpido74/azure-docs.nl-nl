---
title: 'Virtueel WAN: Virtuele hubroutetabel maken naar NVA: Azure-portal'
description: Virtuele WAN virtuele hub route tabel om verkeer te sturen naar een netwerk virtueel toestel met behulp van de portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 0807b535adc45093b439dba5ab8a0ea26b2a0721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402926"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Een virtuele WAN-hubroutetabel maken voor NVA's: Azure-portal

In dit artikel ziet u hoe u verkeer sturen van een filiaal (on-premises site) die via een Network Virtual Appliance (NVA) is aangesloten op de Virtual WAN-hub met een Spoke virtueel netwerk (VNet).

![Virtual WAN-diagram](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Voordat u begint

Controleer of u aan de volgende criteria hebt voldaan:

*  Je hebt een Network Virtual Appliance (NVA). Een network virtual appliance is een software van derden naar keuze die doorgaans vanuit Azure Marketplace in een virtueel netwerk is ingericht.

    * Een privé-IP-adres moet worden toegewezen aan de NVA-netwerkinterface.

    * De NVA wordt niet geïmplementeerd in de virtuele hub. Het moet worden geïmplementeerd in een apart virtueel netwerk.

    *  Het virtuele NVA-netwerk kan een of meer virtuele netwerken hebben aangesloten. In dit artikel verwijzen we naar het virtuele NVA netwerk als een 'indirect gesproken VNet'. Deze virtuele netwerken kunnen worden aangesloten op de NVA VNet met behulp van VNet peering. De VNet Peering-koppelingen worden afgebeeld door zwarte pijlen in de bovenstaande figuur tussen VNet 1, VNet 2 en NVA VNet.
*  U hebt twee virtuele netwerken gemaakt. Ze zullen worden gebruikt als spaak VNets.

    * De VNet spaakadresruimten zijn: VNet1: 10.0.2.0/24 en VNet2: 10.0.3.0/24. Zie [Een virtueel netwerk maken](../virtual-network/quick-create-portal.md)als u informatie nodig hebt over het maken van een virtueel netwerk.

    * Zorg ervoor dat er geen virtuele netwerkgateways in een van de VNets zitten.

    * De VNets vereisen geen gatewaysubnet.

## <a name="1-sign-in"></a><a name="signin"></a>1. Log hier in

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. Maak een virtuele WAN

Maak een virtuele WAN. Gebruik de volgende voorbeeldwaarden:

* **Virtuele WAN naam:** myVirtualWAN
* **Resourcegroep:** testRG
* **Locatie:** West-VS

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. Een hub maken

Maak de hub. Gebruik de volgende voorbeeldwaarden:

* **Locatie:** West-VS
* **Naam:** westushub
* **Privéadresruimte na afnaal:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. Een hubroutetabel maken en toepassen

Werk de hub bij met een hubroutetabel. Gebruik de volgende voorbeeldwaarden:

* **SpaakVNet-adresruimten:** (VNet1 en VNet2) 10.0.2.0/24 en 10.0.3.0/24
* **DMZ NVA-netwerkinterface privé-IP-adres:** 10.0.4.5

1. Navigeer naar uw virtuele WAN.
2. Klik op de hub waarvoor u een routetabel wilt maken.
3. Klik op de **...** en klik vervolgens op **Virtuele hub bewerken**.
4. Schuif op de pagina **Virtuele hub bewerken** naar beneden en schakel het selectievakje Tabel Gebruiken voor **routering in.**
5. Voeg in het **voorvoegsel Als van** bestemming de adresruimten toe. Voeg in de kolom **Verzenden naar volgende hop** het privé-IP-adres van de DMZ NVA-netwerkinterface toe.
6. Klik **op Bevestigen** om de hubbron bij te werken met de routetabelinstellingen.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. De VNet-verbindingen maken

Maak een virtuele netwerkverbinding van elke indirecte spaak VNet (VNet1 en VNet2) naar de hub. Deze virtuele netwerkverbindingen worden afgebeeld door de blauwe pijlen in de bovenstaande figuur. Maak vervolgens een VNet-verbinding van de NVA VNet naar de hub (zwarte pijl in de figuur).

 Voor deze stap u de volgende waarden gebruiken:

| Naam van virtueel netwerk| Verbindingsnaam|
| --- | --- |
| VNet1 | testverbinding1 |
| VNet2 | testverbinding2 |
| NVAVNet (NVAVNet) | testverbinding3 |

Herhaal de volgende procedure voor elk virtueel netwerk dat u wilt verbinden.

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
