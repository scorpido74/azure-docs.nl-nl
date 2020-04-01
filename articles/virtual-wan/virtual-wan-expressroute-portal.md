---
title: Zelfstudie - ExpressRoute-verbindingen maken met Azure Virtual WAN
description: In deze zelfstudie leert u hoe u Azure Virtual WAN gebruikt om ExpressRoute-verbindingen met Azure en on-premises omgevingen te maken.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 35ca071cd8495611f0f350511ef9406f82c5be23
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77209423"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Zelfstudie: Een ExpressRoute-koppeling maken met Azure Virtual WAN

In deze zelfstudie ziet u hoe u Virtual WAN gebruiken om verbinding te maken met uw resources in Azure via een ExpressRoute-circuit. Zie het [Virtual WAN-overzicht](virtual-wan-about.md)voor meer informatie over Virtual WAN- en Virtual WAN-bronnen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel WAN maken
> * Een hub en een gateway maken
> * Een VNet verbinden met een hub
> * Een circuit verbinden met een hubgateway
> * Connectiviteit testen
> * Een gatewaygrootte wijzigen
> * Een standaardroute adverteren

## <a name="before-you-begin"></a>Voordat u begint

Controleer voordat u met de configuratie begint of u aan de volgende criteria hebt voldaan:

* U hebt een virtueel netwerk waarmee u verbinding wilt maken. Controleer of geen van de subnetten van uw on-premises netwerken overlapt met de virtuele netwerken waarmee u verbinding wilt maken. Zie [Snelstart](../virtual-network/quick-create-portal.md)als u een virtueel netwerk wilt maken in de Azure-portal.

* Uw virtuele netwerk heeft geen virtuele netwerkgateways. Als uw virtuele netwerk een gateway heeft (VPN of ExpressRoute), moet u alle gateways verwijderen. Deze configuratie vereist dat virtuele netwerken in plaats daarvan zijn verbonden met de Virtual WAN-hubgateway.

* Zorg dat u een IP-adresbereik krijgt voor uw hubregio. De hub is een virtueel netwerk dat wordt gemaakt en gebruikt door Virtual WAN. Het adresbereik dat u voor de hub opgeeft, kan niet overlappen met een van uw bestaande virtuele netwerken waarmee u verbinding maakt. Dit bereik mag ook niet overlappen met de adresbereiken waarmee u on-premises verbinding wilt maken. Als u niet bekend bent met de IP-adresbereiken in uw on-premises netwerkconfiguratie, coördineert u met iemand die deze gegevens voor u kan verstrekken.

* Het ExpressRoute-circuit moet een Premium-circuit zijn om verbinding te maken met de hubgateway.

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan.

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Een virtueel WAN maken

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

1. Navigeer naar de pagina Virtueel WAN. Klik in de portal op **+Een resource maken**. Typ **Virtueel WAN** in het zoekvak en selecteer Enter.
2. Selecteer **Virtueel WAN** in de resultaten. Klik op de pagina Virtueel WAN op **Maken** om de pagina WAN maken te openen.
3. Vul op de pagina **WAN maken** op het tabblad **Basisbeginselen** de volgende velden in:

   ![WAN maken](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Abonnement** - selecteer het abonnement dat u wilt gebruiken.
   * **Resourcegroep** - maak een nieuwe resourcegroep of gebruik een bestaande.
   * **Locatie resourcegroep** - Kies een resourcelocatie in de vervolgkeuzelijst. Een WAN een globale resource en bevindt zich niet in een bepaalde regio. U moet echter een regio selecteren om de WAN-resource die u maakt eenvoudiger te kunnen beheren en vinden.
   * **Naam** - Typ de naam die u uw WAN wilt noemen.
   * **Type** - Selecteer **Standaard**. U geen ExpressRoute-gateway maken met de Basic SKU.
4. Nadat u klaar bent met het invullen van de velden, selecteert u **Controleren +Maken**.
5. Zodra de validatie is doorgegeven, selecteert **u Maken** om het virtuele WAN te maken.

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>Een virtuele hub en gateway maken

Een virtuele hub is een virtueel netwerk dat wordt gemaakt en gebruikt door Virtual WAN. Het kan verschillende gateways bevatten, zoals VPN en ExpressRoute. In deze sectie maakt u een ExpressRoute-gateway voor uw virtuele hub. U de gateway maken wanneer u [een nieuwe virtuele hub maakt,](#newhub)of u de gateway in een bestaande [hub](#existinghub) maken door deze te bewerken. 

ExpressRoute gateways zijn ingericht in eenheden van 2 Gbps. 1 schaaleenheid = 2 Gbps met ondersteuning tot 10 schaaleenheden = 20 Gbps. Het duurt ongeveer 30 minuten voor een virtuele hub en gateway volledig te maken.

### <a name="to-create-a-new-virtual-hub-and-a-gateway"></a><a name="newhub"></a>Een nieuwe virtuele hub en een gateway maken

Maak een nieuwe virtuele hub. Zodra een hub is gemaakt, worden er kosten in rekening gebracht voor de hub, zelfs als u geen sites koppelt.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="to-create-a-gateway-in-an-existing-hub"></a><a name="existinghub"></a>Een gateway maken in een bestaande hub

U ook een gateway maken in een bestaande hub door deze te bewerken.

1. Navigeer naar de virtuele hub die u wilt bewerken en selecteer deze.
2. Schakel op de pagina **Virtuele hub bewerken** het selectievakje **ExpressRoute-gateway opnemen in.**
3. Selecteer **Bevestigen** om uw wijzigingen te bevestigen. Het duurt ongeveer 30 minuten voordat de hub- en hubbronnen volledig zijn gemaakt.

   ![bestaande hub](./media/virtual-wan-expressroute-portal/edithub.png "een hub bewerken")

### <a name="to-view-a-gateway"></a>Een gateway weergeven

Zodra u een ExpressRoute-gateway hebt gemaakt, u gatewaydetails bekijken. Navigeer naar de hub, selecteer **ExpressRoute**en bekijk de gateway.

![Gateway weergeven](./media/virtual-wan-expressroute-portal/viewgw.png "bekijk gateway")

## <a name="connect-your-vnet-to-the-hub"></a><a name="connectvnet"></a>Sluit uw VNet aan op de hub

In deze sectie maakt u de peeringverbinding tussen uw hub en een VNet. Herhaal deze stappen voor elk VNet dat u wilt verbinden.

1. Klik op de pagina voor uw virtuele WAN op **Virtuele netwerkverbinding**.
2. Klik op de pagina Virtuele netwerkverbinding op **+Verbinding toevoegen**.
3. Vul de volgende velden in op de pagina **Verbinding toevoegen**:

    * **Verbindingsnaam** - voer een naam in voor uw verbinding.
    * **Hubs** - selecteer de hub die u wilt koppelen aan deze verbinding.
    * **Abonnement** - controleer of het abonnement klopt.
    * **Virtueel netwerk** - selecteer het virtuele netwerk dat met deze hub wilt verbinden. Het virtuele netwerk kan geen reeds bestaande virtuele netwerkgateway hebben (noch VPN, noch ExpressRoute).

## <a name="connect-your-circuit-to-the-hub-gateway"></a><a name="connectcircuit"></a>Sluit uw circuit aan op de hubgateway

Zodra de gateway is gemaakt, u er een [ExpressRoute-circuit](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) op aansluiten. ExpressRoute Premium-circuits die zich in expressroute-locaties met Global Reach bevinden, kunnen verbinding maken met een virtuele WAN ExpressRoute-gateway.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Het circuit aansluiten op de hubgateway

Ga in de portal naar de pagina **Virtual hub -> Connectivity -> ExpressRoute.** Als u toegang hebt tot uw abonnement op een ExpressRoute-circuit, ziet u het circuit dat u wilt gebruiken in de lijst met circuits. Als u geen circuits ziet, maar wel een autorisatiesleutel en peer circuit URI hebt gekregen, u een circuit inwisselen en aansluiten. Zie [Verbinding maken door een autorisatiesleutel in te wisselen](#authkey).

1. Selecteer het circuit.
2. Selecteer **Schakel circuit(en) aan.**

   ![schakelingen aansluiten](./media/virtual-wan-expressroute-portal/cktconnect.png "schakelingen aansluiten")

### <a name="to-connect-by-redeeming-an-authorization-key"></a><a name="authkey"></a>Verbinding maken door een autorisatiesleutel in te wisselen

Gebruik de autorisatiesleutel en circuit-URI die u hebt geleverd om verbinding te maken.

1. Klik op de pagina ExpressRoute op **+Autorisatiesleutel inwisselen**

   ![Inwisselen](./media/virtual-wan-expressroute-portal/redeem.png "Inwisselen")
2. Vul op de pagina Autorisatiesleutel inwisselen de waarden in.

   ![kernwaarden inwisselen](./media/virtual-wan-expressroute-portal/redeemkey2.png "kernwaarden inwisselen")
3. Selecteer **Toevoegen** om de sleutel toe te voegen.
4. Bekijk het circuit. Een ingewisseld circuit toont alleen de naam (zonder het type, provider en andere informatie) omdat het in een ander abonnement dan dat van de gebruiker.

## <a name="to-test-connectivity"></a>Connectiviteit testen

Nadat de verbinding met het circuit is ingesteld, wordt de status van de hubverbinding aangegeven op 'deze hub', wat impliceert dat de verbinding tot stand wordt gebracht met de hub ExpressRoute-gateway. Wacht ongeveer 5 minuten voordat u de connectiviteit van een client achter uw ExpressRoute-circuit test, bijvoorbeeld een VM in het VNet die u eerder hebt gemaakt.

Als u sites hebt die zijn verbonden met een Virtual WAN VPN-gateway in dezelfde hub als de ExpressRoute-gateway, u bidirectionele connectiviteit hebben tussen VPN- en ExpressRoute-eindpunten. Dynamic routing (BGP) wordt ondersteund. De ASN van de gateways in de hub is vast en kan op dit moment niet worden bewerkt.

## <a name="to-change-the-size-of-a-gateway"></a>De grootte van een gateway wijzigen

Als u de grootte van uw ExpressRoute-gateway wilt wijzigen, zoekt u de ExpressRoute-gateway in de hub en selecteert u de schaaleenheden in de vervolgkeuzelijst. Sla je wisselgeld op. Het duurt ongeveer 30 minuten om de hubgateway bij te werken.

![gatewaygrootte wijzigen](./media/virtual-wan-expressroute-portal/changescale.png "gatewaygrootte wijzigen")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Standaardroute 0.0.0.0/0 naar eindpunten adverteren

Als u wilt dat de virtuele Azure-hub de standaardroute 0.0.0.0/0 naar uw ExpressRoute-eindpunten adverteert, moet u 'Standaardroute voor uitdragen' inschakelen.

1. Selecteer uw **>->-> bewerkingsverbinding**bewerken .

   ![Verbinding bewerken](./media/virtual-wan-expressroute-portal/defaultroute1.png "Verbinding bewerken")

2. Selecteer **Inschakelen** om de standaardroute te propageren.

   ![Standaardroute voorverspreiden](./media/virtual-wan-expressroute-portal/defaultroute2.png "Standaardroute voorverspreiden")

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
