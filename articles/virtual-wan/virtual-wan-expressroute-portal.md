---
title: 'Zelf studie: ExpressRoute-verbindingen maken met behulp van Azure Virtual WAN'
description: In deze zelfstudie leert u hoe u Azure Virtual WAN gebruikt om ExpressRoute-verbindingen met Azure en on-premises omgevingen te maken.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 35ca071cd8495611f0f350511ef9406f82c5be23
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77209423"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Zelf studie: een ExpressRoute-koppeling maken met behulp van Azure Virtual WAN

In deze zelf studie ziet u hoe u met behulp van virtueel WAN verbinding maakt met uw resources in azure via een ExpressRoute-circuit. Zie het [overzicht van virtuele](virtual-wan-about.md)WAN-netwerken voor meer informatie over virtuele WAN-en virtuele WAN-resources.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel WAN maken
> * Een hub en een gateway maken
> * Een VNet verbinden met een hub
> * Een circuit verbinden met een hub-gateway
> * Connectiviteit testen
> * De grootte van een gateway wijzigen
> * Een standaard route adverteren

## <a name="before-you-begin"></a>Voordat u begint

Controleer voordat u met de configuratie begint of u aan de volgende criteria hebt voldaan:

* U hebt een virtueel netwerk waarmee u verbinding wilt maken. Controleer of geen van de subnetten van uw on-premises netwerken overlapt met de virtuele netwerken waarmee u verbinding wilt maken. Als u een virtueel netwerk in de Azure Portal wilt maken, raadpleegt u [Quick](../virtual-network/quick-create-portal.md)start.

* Het virtuele netwerk heeft geen virtuele netwerk gateways. Als uw virtuele netwerk een gateway heeft (VPN of ExpressRoute), moet u alle gateways verwijderen. Voor deze configuratie moeten virtuele netwerken in plaats daarvan zijn verbonden met de virtuele WAN hub-gateway.

* Zorg dat u een IP-adresbereik krijgt voor uw hubregio. De hub is een virtueel netwerk dat wordt gemaakt en gebruikt door Virtual WAN. Het adres bereik dat u voor de hub opgeeft, mag niet overlappen met een van de bestaande virtuele netwerken waarmee u verbinding maakt. Dit bereik mag ook niet overlappen met de adresbereiken waarmee u on-premises verbinding wilt maken. Als u niet bekend bent met de IP-adresbereiken die zich in uw on-premises netwerk configuratie bevinden, coördineert u met iemand die deze gegevens voor u kan opgeven.

* Het ExpressRoute-circuit moet een Premium-circuit zijn om verbinding te maken met de hub-gateway.

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Een virtueel WAN maken

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

1. Ga naar de virtuele WAN-pagina. Klik in de portal op **+Een resource maken**. Typ **Virtual WAN** in het zoekvak en selecteer ENTER.
2. Selecteer **virtueel WAN** in de resultaten. Klik op de pagina virtueel WAN op **maken** om de pagina WAN maken te openen.
3. Vul op de pagina **WAN maken** op het tabblad **basis beginselen** de volgende velden in:

   ![WAN maken](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Abonnement** - selecteer het abonnement dat u wilt gebruiken.
   * **Resourcegroep** - maak een nieuwe resourcegroep of gebruik een bestaande.
   * **Locatie van resource groep** : Kies een resource locatie in de vervolg keuzelijst. Een WAN een globale resource en bevindt zich niet in een bepaalde regio. U moet echter een regio selecteren om de WAN-resource die u maakt eenvoudiger te kunnen beheren en vinden.
   * **Naam** : Typ de naam die u voor uw WAN wilt aanroepen.
   * **Type** -Selecteer **standaard**. U kunt geen ExpressRoute-gateway maken met behulp van de basis-SKU.
4. Wanneer u klaar bent met het invullen van de velden, selecteert u **controleren + maken**.
5. Wanneer de validatie is geslaagd, selecteert u **maken** om het virtuele WAN te maken.

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>Een virtuele hub en gateway maken

Een virtuele hub is een virtueel netwerk dat door virtuele WAN wordt gemaakt en gebruikt. Het kan verschillende gateways bevatten, zoals VPN en ExpressRoute. In deze sectie maakt u een ExpressRoute-gateway voor uw virtuele hub. U kunt de gateway maken wanneer u [een nieuwe virtuele hub maakt](#newhub), of u kunt de gateway maken in een [bestaande hub](#existinghub) door deze te bewerken. 

ExpressRoute-gateways worden ingericht in eenheden van 2 Gbps. 1 schaal eenheid = 2 Gbps met ondersteuning van Maxi maal 10 schaal eenheden = 20 Gbps. Het duurt ongeveer 30 minuten voordat een virtuele hub en gateway volledig worden gemaakt.

### <a name="to-create-a-new-virtual-hub-and-a-gateway"></a><a name="newhub"></a>Een nieuwe virtuele hub en een gateway maken

Maak een nieuwe virtuele hub. Zodra een hub is gemaakt, worden er kosten in rekening gebracht voor de hub, zelfs als u geen sites koppelt.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="to-create-a-gateway-in-an-existing-hub"></a><a name="existinghub"></a>Een gateway maken in een bestaande hub

U kunt ook een gateway maken in een bestaande hub door deze te bewerken.

1. Ga naar de virtuele hub die u wilt bewerken en selecteer deze.
2. Schakel op de pagina **virtuele hub bewerken** het selectie vakje **ExpressRoute-gateway toevoegen**in.
3. Selecteer **bevestigen** om uw wijzigingen te bevestigen. Het duurt ongeveer 30 minuten om de hub-en hub-resources volledig te maken.

   ![bestaande hub](./media/virtual-wan-expressroute-portal/edithub.png "een hub bewerken")

### <a name="to-view-a-gateway"></a>Een gateway weer geven

Wanneer u een ExpressRoute-gateway hebt gemaakt, kunt u de gateway gegevens weer geven. Ga naar de hub, selecteer **ExpressRoute**en Bekijk de gateway.

![Gateway weer geven](./media/virtual-wan-expressroute-portal/viewgw.png "gateway weer geven")

## <a name="connect-your-vnet-to-the-hub"></a><a name="connectvnet"></a>Verbind uw VNet met de hub

In deze sectie maakt u de peering-verbinding tussen uw hub en een VNet. Herhaal deze stappen voor elk VNet dat u wilt verbinden.

1. Klik op de pagina voor uw virtuele WAN op **Virtuele netwerkverbinding**.
2. Klik op de pagina Virtuele netwerkverbinding op **+Verbinding toevoegen**.
3. Vul de volgende velden in op de pagina **Verbinding toevoegen**:

    * **Verbindingsnaam** - voer een naam in voor uw verbinding.
    * **Hubs** - selecteer de hub die u wilt koppelen aan deze verbinding.
    * **Abonnement** - controleer of het abonnement klopt.
    * **Virtueel netwerk** - selecteer het virtuele netwerk dat met deze hub wilt verbinden. Het virtuele netwerk kan geen bestaande gateway van een virtueel netwerk (VPN of ExpressRoute) hebben.

## <a name="connect-your-circuit-to-the-hub-gateway"></a><a name="connectcircuit"></a>Uw circuit verbinden met de hub gateway

Zodra de gateway is gemaakt, kunt u er een [ExpressRoute-circuit](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) aan koppelen. ExpressRoute Premium-circuits die zich bevinden in ExpressRoute Global Reach ondersteunde locaties kunnen verbinding maken met een virtuele WAN ExpressRoute-gateway.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Het circuit verbinden met de hub gateway

Ga in de portal naar de pagina **Virtual hub-> Connectivity-> ExpressRoute** . Als u in uw abonnement toegang hebt tot een ExpressRoute-circuit, ziet u het circuit dat u wilt gebruiken in de lijst met circuits. Als er geen circuits worden weer gegeven, maar wel zijn voorzien van een autorisatie sleutel en een peer circuit-URI, kunt u een circuit inwisselen en aansluiten. Zie [verbinding maken door een autorisatie sleutel in te wisselen](#authkey).

1. Selecteer het circuit.
2. Selecteer **verbinding maken circuit (s)**.

   ![circuits verbinden](./media/virtual-wan-expressroute-portal/cktconnect.png "circuits verbinden")

### <a name="to-connect-by-redeeming-an-authorization-key"></a><a name="authkey"></a>Verbinding maken door een autorisatie sleutel in te wisselen

Gebruik de autorisatie sleutel en circuit-URI die u hebt gekregen om verbinding te maken.

1. Klik op de pagina ExpressRoute op **+ autorisatie sleutel inwisselen**

   ![inwisselen](./media/virtual-wan-expressroute-portal/redeem.png "inwisselen")
2. Vul de waarden in op de pagina autorisatie sleutel voor inwisselen.

   ![sleutel waarden inwisselen](./media/virtual-wan-expressroute-portal/redeemkey2.png "sleutel waarden inwisselen")
3. Selecteer **toevoegen** om de sleutel toe te voegen.
4. Bekijk het circuit. Een ingewisseld circuit bevat alleen de naam (zonder het type, de provider en andere informatie) omdat deze zich in een ander abonnement bevindt dan die van de gebruiker.

## <a name="to-test-connectivity"></a>De connectiviteit testen

Nadat de verbinding met het circuit tot stand is gebracht, geeft de status van de hub-verbinding ' This hub ' aan. Dit betekent dat de verbinding tot stand wordt gebracht met de hub ExpressRoute-gateway. Wacht ongeveer 5 minuten voordat u de verbinding van een client achter uw ExpressRoute-circuit test, bijvoorbeeld een virtuele machine in het VNet dat u eerder hebt gemaakt.

Als u sites hebt verbonden met een virtuele WAN-VPN-gateway in dezelfde hub als de ExpressRoute-gateway, kunt u een bidirectionele verbinding hebben tussen VPN-en ExpressRoute-eind punten. Dynamische route ring (BGP) wordt ondersteund. De ASN van de gateways in de hub is vast en kan op dit moment niet worden bewerkt.

## <a name="to-change-the-size-of-a-gateway"></a>De grootte van een gateway wijzigen

Als u de grootte van uw ExpressRoute-gateway wilt wijzigen, gaat u naar de ExpressRoute-gateway in de hub en selecteert u de schaal eenheden in de vervolg keuzelijst. Sla de wijziging op. Het duurt ongeveer 30 minuten om de hub-gateway bij te werken.

![Gateway grootte wijzigen](./media/virtual-wan-expressroute-portal/changescale.png "Gateway grootte wijzigen")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Standaard route 0.0.0.0/0 aan eind punten adverteren

Als u wilt dat de virtuele Azure-hub de standaard route 0.0.0.0/0 aan uw ExpressRoute-eind punten aankondigt, moet u de standaard route door geven inschakelen.

1. Selecteer uw **circuit->...-> verbinding bewerken**.

   ![Verbinding bewerken](./media/virtual-wan-expressroute-portal/defaultroute1.png "Verbinding bewerken")

2. Selecteer **inschakelen** om de standaard route door te geven.

   ![Standaard route door geven](./media/virtual-wan-expressroute-portal/defaultroute2.png "Standaard route door geven")

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
