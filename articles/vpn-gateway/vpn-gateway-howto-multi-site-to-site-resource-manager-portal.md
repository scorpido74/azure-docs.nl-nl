---
title: 'Meerdere VPN-gatewaysite-verbindingen toevoegen aan een VNet: Azure Portal'
description: S2S-verbindingen met meerdere locaties toevoegen aan een VPN-gateway met een bestaande verbinding
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 7b438f2b966dc43d41b91a138b39193d230d5546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779685"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Een site-to-site-verbinding toevoegen aan een VNet met een bestaande VPN-gatewayverbinding

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klassiek)](vpn-gateway-multi-site.md)
>
> 

Met dit artikel u Site-to-Site -verbindingen toevoegen aan een VPN-gateway die een bestaande verbinding heeft met behulp van de Azure-portal. Dit type verbinding wordt vaak aangeduid als een "multi-site" configuratie. U een S2S-verbinding toevoegen aan een VNet dat al een S2S-verbinding, Point-to-Site-verbinding of VNet-verbinding heeft. Er zijn enkele beperkingen bij het toevoegen van verbindingen. Controleer de [sectie Voordat u in](#before) dit artikel begint om te controleren voordat u uw configuratie start. 

Dit artikel is van toepassing op VNets van Resource Manager met een RouteBased VPN-gateway. Deze stappen zijn niet van toepassing op nieuwe samenbestaande verbindingsconfiguraties van ExpressRoute/Site-to-Site. Als u echter alleen een nieuwe VPN-verbinding toevoegt aan een reeds bestaande coëxistentieconfiguratie, u deze stappen gebruiken. Zie [ExpressRoute/S2S naast elkaar bestaande verbindingen](../expressroute/expressroute-howto-coexist-resource-manager.md) voor informatie over naast elkaar bestaande verbindingen.

### <a name="deployment-models-and-methods"></a>Implementatiemodellen en -methoden
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

We werken deze tabel bij als er nieuwe artikelen en extra tools beschikbaar komen voor deze configuratie. Wanneer een artikel beschikbaar is, koppelen we er rechtstreeks naar vanuit deze tabel.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before-you-begin"></a><a name="before"></a>Voordat u begint
Controleer de volgende items:

* U configureert geen nieuwe naast elkaar bestaande ExpressRoute- en VPN-gatewayconfiguratie.
* U hebt een virtueel netwerk dat is gemaakt met behulp van het implementatiemodel ResourceBeheer met een bestaande verbinding.
* De virtuele netwerkgateway voor uw VNet is RouteBased. Als u een PolicyBased VPN-gateway hebt, moet u de virtuele netwerkgateway verwijderen en een nieuwe VPN-gateway als RouteBased maken.
* Geen van de adresbereiken overlapt voor een van de VNets waarmee deze VNet verbinding maakt.
* Je hebt een compatibel VPN-apparaat en iemand die het kan configureren. Zie [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Over VPN-apparaten). Als u niet weet hoe u uw VPN-apparaat moet configureren of de IP-adresbereiken in uw on-premises netwerkconfiguratie niet kent, moet u contact opnemen met iemand die u hierbij kan helpen en de benodigde gegevens kan verstrekken.
* Je hebt een extern gericht openbaar IP-adres voor je VPN-apparaat. Dit IP-adres kan zich niet achter een NAT bevinden.

## <a name="part-1---configure-a-connection"></a><a name="part1"></a>Deel 1 - Een verbinding configureren
1. Navigeer via een browser naar de [Azure Portal](https://portal.azure.com) en log, indien nodig, in met uw Azure-account.
2. Klik op **Alle bronnen** en zoek uw **virtuele netwerkgateway** in de lijst met bronnen en klik erop.
3. Klik op de pagina **Virtuele netwerkgateway** op **Verbindingen**.
   
    ![Pagina Verbindingen](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Pagina Verbindingen")<br>
4. Klik **op** de pagina Verbindingen op **+Toevoegen**.
   
    ![Knop Verbinding toevoegen](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Knop Verbinding toevoegen")<br>
5. Vul op de pagina **Verbinding toevoegen** de volgende velden in:
   
   * **Naam:** De naam die u wilt geven aan de site waarnaar u de verbinding maakt.
   * **Verbindingstype:** Selecteer **Site-to-site (IPsec)**.
     
     ![Verbindingspagina toevoegen](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Verbindingspagina toevoegen")<br>

## <a name="part-2---add-a-local-network-gateway"></a><a name="part2"></a>Deel 2 - Een lokale netwerkgateway toevoegen
1. Klik op **Lokale netwerkgateway** ***Kies een lokale netwerkgateway***. Hiermee wordt de pagina **Lokale netwerkgateway kiezen** geopend.
   
    ![Lokale netwerkgateway kiezen](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Lokale netwerkgateway kiezen")<br>
2. Klik **op Nieuw maken** om de pagina Lokale **netwerkgateway maken** te openen.
   
    ![Pagina lokale netwerkgateway maken](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Lokale netwerkgateway maken")<br>
3. Vul op de pagina **Lokale netwerkgateway maken** de volgende velden in:
   
   * **Naam:** De naam die u wilt geven aan de lokale netwerkgatewaybron.
   * **IP-adres:** Het openbare IP-adres van het VPN-apparaat op de site waarmee u verbinding wilt maken.
   * **Adresruimte:** De adresruimte die u wilt doorsturen naar de nieuwe lokale netwerksite.
4. Klik op **OK** op de pagina **Lokale netwerkgateway maken** om de wijzigingen op te slaan.

## <a name="part-3---add-the-shared-key-and-create-the-connection"></a><a name="part3"></a>Deel 3 - Voeg de gedeelde sleutel toe en maak de verbinding
1. Voeg op de pagina **Verbinding toevoegen** de gedeelde sleutel toe die u wilt gebruiken om uw verbinding te maken. U de gedeelde sleutel van uw VPN-apparaat ophalen, of hier een maken en vervolgens uw VPN-apparaat configureren om dezelfde gedeelde sleutel te gebruiken. Het belangrijkste is dat de toetsen precies hetzelfde zijn.
   
    ![Gedeelde sleutel](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Gedeelde sleutel")<br>
2. Klik onder aan de pagina op **OK** om de verbinding te maken.

## <a name="part-4---verify-the-vpn-connection"></a><a name="part4"></a>Deel 4 - De VPN-verbinding verifiëren


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Volgende stappen

Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie het [leerpad voor virtuele machines](/learn/paths/deploy-a-website-with-azure-virtual-machines/) voor meer informatie.
