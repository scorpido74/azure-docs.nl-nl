---
title: 'Meerdere VPN-gateway site-to-site-verbindingen toevoegen aan een VNet: Azure Portal'
description: Multi-site S2S-verbindingen toevoegen aan een VPN-gateway die een bestaande verbinding heeft
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 5a74f1ca8d1055885561a35dbd18899193d3924d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984141"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Een site-naar-site-verbinding met een VNet toevoegen met een bestaande VPN-gateway verbinding

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [Power shell (klassiek)](vpn-gateway-multi-site.md)
>
> 

Dit artikel helpt u bij het toevoegen van site-naar-site (S2S)-verbindingen met een VPN-gateway die een bestaande verbinding heeft met behulp van de Azure Portal. Dit type verbinding wordt vaak een ' multi-site ' configuratie genoemd. U kunt een S2S-verbinding toevoegen aan een VNet dat al een S2S-verbinding, punt-naar-site-verbinding of VNet-naar-VNet-verbinding heeft. Er zijn enkele beperkingen bij het toevoegen van verbindingen. Raadpleeg de sectie [voordat u begint](#before) in dit artikel om te controleren voordat u de configuratie start. 

Dit artikel is van toepassing op Resource Manager-VNets die een RouteBased VPN-gateway hebben. Deze stappen zijn niet van toepassing op nieuwe ExpressRoute/site-to-site naast elkaar bestaande verbindings configuraties. Als u echter alleen maar een nieuwe VPN-verbinding toevoegt aan een bestaande naast elkaar aanwezige configuratie, kunt u deze stappen gebruiken. Zie [ExpressRoute/S2S-bestaande verbindingen](../expressroute/expressroute-howto-coexist-resource-manager.md) voor informatie over naast elkaar bestaande verbindingen.

### <a name="deployment-models-and-methods"></a>Implementatiemodellen en -methoden
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Deze tabel wordt bijgewerkt naar nieuwe artikelen en er worden extra hulpprogram ma's beschikbaar voor deze configuratie. Wanneer er een artikel beschikbaar is, wordt er rechtstreeks vanuit deze tabel een koppeling naar de sjabloon.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before-you-begin"></a><a name="before"></a>Voordat u begint
Controleer de volgende items:

* U configureert geen nieuwe cobestaande ExpressRoute-en VPN Gateway configuratie.
* U hebt een virtueel netwerk dat is gemaakt met behulp van het Resource Manager-implementatie model met een bestaande verbinding.
* De virtuele netwerk gateway voor uw VNet is RouteBased. Als u een PolicyBased VPN-gateway hebt, moet u de gateway van het virtuele netwerk verwijderen en een nieuwe VPN-gateway maken als RouteBased.
* Geen van de adresbereiken overlapt voor een van de VNets waarmee dit VNet verbinding maakt.
* U hebt een compatibel VPN-apparaat en iemand die dit kan configureren. Zie [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Over VPN-apparaten). Als u niet weet hoe u uw VPN-apparaat moet configureren of de IP-adresbereiken in uw on-premises netwerkconfiguratie niet kent, moet u contact opnemen met iemand die u hierbij kan helpen en de benodigde gegevens kan verstrekken.
* U hebt een extern gericht openbaar IP-adres voor uw VPN-apparaat. Dit IP-adres kan zich niet achter een NAT bevinden.

## <a name="part-1---configure-a-connection"></a><a name="part1"></a>Deel 1: een verbinding configureren
1. Navigeer via een browser naar de [Azure Portal](https://portal.azure.com) en log, indien nodig, in met uw Azure-account.
2. Klik op **alle resources** en zoek de gateway van uw **virtuele netwerk** in de lijst met resources en klik erop.
3. Klik op de pagina **virtuele netwerk gateway** op **verbindingen**.
   
    ![Pagina verbindingen](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Pagina verbindingen")<br>
4. Klik op de pagina **verbindingen** op **+ toevoegen**.
   
    ![Knop verbinding toevoegen](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Knop verbinding toevoegen")<br>
5. Vul de volgende velden in op de pagina **verbinding toevoegen** :
   
   * **Naam:** De naam die u wilt toekennen aan de site waarmee u de verbinding maakt.
   * **Verbindings type:** Selecteer **site-naar-site (IPSec)**.
     
     ![Verbindings pagina toevoegen](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Verbindings pagina toevoegen")<br>

## <a name="part-2---add-a-local-network-gateway"></a><a name="part2"></a>Deel 2: een lokale netwerk gateway toevoegen
1. Klik op **lokale netwerk gateway** ***Kies een lokale netwerk gateway***. Hiermee opent u de pagina **lokale netwerk gateway kiezen** .
   
    ![Lokale netwerk gateway kiezen](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Lokale netwerk gateway kiezen")<br>
2. Klik op **nieuwe maken** om de pagina **lokale netwerk gateway maken** te openen.
   
    ![Pagina lokale netwerk gateway maken](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Lokale netwerk gateway maken")<br>
3. Vul op de pagina **lokale netwerk gateway maken** de volgende velden in:
   
   * **Naam:** De naam die u aan de lokale netwerk gateway resource wilt geven.
   * **IP-adres:** Het open bare IP-adres van het VPN-apparaat op de site waarmee u verbinding wilt maken.
   * **Adres ruimte:** De adres ruimte die u wilt omleiden naar de nieuwe lokale netwerk site.
4. Klik op **OK** op de pagina **lokale netwerk gateway maken** om de wijzigingen op te slaan.

## <a name="part-3---add-the-shared-key-and-create-the-connection"></a><a name="part3"></a>Deel 3: de gedeelde sleutel toevoegen en de verbinding maken
1. Voeg op de pagina **verbinding toevoegen** de gedeelde sleutel toe die u wilt gebruiken om uw verbinding te maken. U kunt de gedeelde sleutel ophalen van uw VPN-apparaat of hier een van maken en vervolgens uw VPN-apparaat configureren voor het gebruik van dezelfde gedeelde sleutel. Het belangrijkste is dat de sleutels precies hetzelfde zijn.
   
    ![Gedeelde sleutel](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Gedeelde sleutel")<br>
2. Klik onder aan de pagina op **OK** om de verbinding te maken.

## <a name="part-4---verify-the-vpn-connection"></a><a name="part4"></a>Deel 4: de VPN-verbinding controleren


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Volgende stappen

Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie het [leer traject voor virtuele machines](/learn/paths/deploy-a-website-with-azure-virtual-machines/) voor meer informatie.
