---
title: Een virtuele netwerkgateway verbinden met een Azure Virtual WAN
description: Met dit artikel u een Azure virtual network gateway verbinden met een Azure Virtual WAN VPN-gateway
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: 688183bc07aa14d5e5df182d7de0897cec93f0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066238"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>Een VPN-gateway (virtuele netwerkgateway) verbinden met Virtual WAN

Met dit artikel u connectiviteit instellen van een Azure VPN Gateway (virtual network gateway) naar een Azure Virtual WAN (VPN-gateway). Het maken van een verbinding van een VPN Gateway (virtual network gateway) naar een Virtual WAN (VPN-gateway) is vergelijkbaar met het instellen van connectiviteit met een virtuele WAN van vpn-sites van branch.

Om mogelijke verwarring tussen twee functies te minimaliseren, zullen we de gateway voorafgaan met de naam van de functie waar we naar verwijzen. Bijvoorbeeld VPN Gateway virtual network gateway en Virtual WAN VPN gateway.

## <a name="before-you-begin"></a>Voordat u begint

Maak voordat u begint de volgende bronnen:

Azure Virtual WAN

* [Een virtueel WAN maken.](virtual-wan-site-to-site-portal.md#openvwan)
* [Een hub maken.](virtual-wan-site-to-site-portal.md#hub) De virtuele hub bevat de Virtual WAN VPN-gateway.

Azure Virtual Network

* Maak een virtueel netwerk zonder virtuele netwerkgateways. Controleer of geen van de subnetten van uw on-premises netwerken overlapt met de virtuele netwerken waarmee u verbinding wilt maken. Zie [Snelstart](../virtual-network/quick-create-portal.md)als u een virtueel netwerk wilt maken in de Azure-portal.

## <a name="1-create-an-azure-virtual-network-gateway"></a><a name="vnetgw"></a>1. Een Azure-virtuele netwerkgateway maken

Maak een VPN Gateway virtuele netwerkgateway voor uw virtuele netwerk in actief-actieve modus voor uw virtuele netwerk. Wanneer u de gateway maakt, u bestaande openbare IP-adressen gebruiken voor de twee exemplaren van de gateway of nieuwe openbare IP-adressen maken. U gebruikt deze openbare IP's bij het instellen van de Virtuele WAN-sites. Zie [Actieve actieve verbindingen configureren](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway)voor meer informatie over actief actieve modus.

### <a name="active-active-mode-setting"></a><a name="active-active"></a>Instelling actieve modus

![actief-actief](./media/connect-virtual-network-gateway-vwan/active.png "actief-actief")

### <a name="bgp-setting"></a><a name="BGP"></a>BGP-instelling

De BGP ASN kan niet 65515 zijn. 66515 wordt gebruikt door Azure Virtual WAN.

![BGP](./media/connect-virtual-network-gateway-vwan/bgp.png "Bgp")

### <a name="public-ip-addresses"></a><a name="pip"></a>Openbare IP-adressen

Wanneer de gateway is gemaakt, navigeert u naar de pagina **Eigenschappen.** De eigenschappen en configuratie-instellingen zijn vergelijkbaar met het volgende voorbeeld. Let op de twee openbare IP-adressen die worden gebruikt voor de gateway.

![Eigenschappen](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. Maak Virtual WAN VPN-sites

Als u virtual WAN VPN-sites wilt maken, navigeert u naar uw virtuele WAN en selecteert u onder **Connectiviteit** **VPN-sites**. In deze sectie maakt u twee Virtual WAN VPN-sites die overeenkomen met de virtuele netwerkgateways die u in de vorige sectie hebt gemaakt.

1. Selecteer **+Site maken**.
2. Typ op de pagina **VPN-sites maken** de volgende waarden:

   * **Regio** - (Dezelfde regio als de virtual network gateway van Azure VPN Gateway)
   * **Apparaatleverancier** - Voer de apparaatleverancier in (elke naam)
   * **Werkruimte voor privéadres** - (Voer een waarde in of laat deze leeg wanneer BGP is ingeschakeld)
   * **Border Gateway Protocol** - (Instellen **als inschakelen** als de virtuele Netwerkgateway van Azure VPN-gateway BGP heeft ingeschakeld)
   * **Verbinding maken met hubs** (Hub selecteren die u hebt gemaakt in de vereisten van de vervolgkeuzelijst)
3. Voer **onder Koppelingen**de volgende waarden in:

   * **Providernaam** - Voer een koppelingsnaam en een providernaam in (elke naam)
   * **Snelheid** - Snelheid (elk getal)
   * **IP-adres** - Voer IP-adres in (hetzelfde als het eerste openbare IP-adres dat wordt weergegeven onder de eigenschappen van de virtuele netwerkgateway (VPN Gateway)
   * **BGP-adres** en **ASN** - BGP-adres en ASN. Deze moeten hetzelfde zijn als een van de IP-adressen van BGP-peer en ASN van de virtuele netwerkgateway VPN Gateway die u in [stap 1](#vnetgw)hebt geconfigureerd.
4. Controleer en selecteer **Bevestigen** om de site te maken.
5. Herhaal de vorige stappen om de tweede site te maken die overeenkomt met de tweede instantie van de virtuele netwerkgateway vpn-gateway. U behoudt dezelfde instellingen, behalve het gebruik van een tweede openbaar IP-adres en een tweede IP-adres met BGP-peer van vpn-gatewayconfiguratie.
6. U hebt nu twee sites met succes ingericht en doorgaan naar de volgende sectie om configuratiebestanden te downloaden.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. Download de VPN-configuratiebestanden

In deze sectie downloadt u het VPN-configuratiebestand voor elk van de sites die u in de vorige sectie hebt gemaakt.

1. Selecteer boven aan de pagina Virtual WAN **VPN-sites** de **site**en selecteer **vervolgens Site-to-site VPN-configuratie downloaden.** Azure maakt een configuratiebestand met de instellingen.

   ![configuratiebestand downloaden](./media/connect-virtual-network-gateway-vwan/download.png "downloaden")
2. Download en open het configuratiebestand.
3. Herhaal deze stappen voor de tweede site. Zodra u beide configuratiebestanden hebt geopend, u doornaar de volgende sectie.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. De lokale netwerkgateways maken

In deze sectie maakt u twee lokale azure VPN Gateway-gateways. De configuratiebestanden van de vorige stap bevatten de configuratie-instellingen voor poorten. Gebruik deze instellingen om de lokale netwerkgateways van Azure VPN Gateway te maken en te configureren.

1. Maak de lokale netwerkgateway met behulp van deze instellingen. Zie het VPN Gateway-artikel [Een lokale netwerkgateway maken](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway)voor informatie over het maken van een lokale VPN Gateway-gateway.

   * **IP-adres** - Gebruik het IP-adres Instance0 dat wordt weergegeven voor *gatewayconfiguratie* uit het configuratiebestand.
   * **BGP** - Als de verbinding ouder is dan BGP, selecteert u **BGP-instellingen configureren** en voert u de ASN '65515' in. Voer het IP-adres van de BGP-peer in. Gebruik 'Instance0 BgpPeeringAddresses' voor *gatewayconfiguratie* vanuit het configuratiebestand.
   * **Abonnement, Resourcegroep en locatie** zijn hetzelfde als voor de Virtuele WAN-hub.
2. Bekijk en maak de lokale netwerkgateway. Uw lokale netwerkgateway moet er hetzelfde uitzien als dit voorbeeld.

   ![configuratiebestand downloaden](./media/connect-virtual-network-gateway-vwan/lng1.png "instantie0")
3. Herhaal deze stappen om een andere lokale netwerkgateway te maken, maar gebruik deze keer de waarden 'Instance1' in plaats van 'Instance0'-waarden uit het configuratiebestand.

   ![configuratiebestand downloaden](./media/connect-virtual-network-gateway-vwan/lng2.png "instance1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. Verbindingen maken

In deze sectie maakt u een verbinding tussen de lokale netwerkgateways van de VPN Gateway en de virtuele netwerkgateway. Zie [Een verbinding configureren](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection)voor stappen voor het maken van een VPN-gatewayverbinding.

1. Navigeer in de portal naar uw virtuele netwerkgateway en klik op **Verbindingen**. Klik bovenaan de pagina Verbindingen op **+ Toevoegen** om de pagina **Verbinding toevoegen** te openen.
2. Configureer op de pagina **Verbinding toevoegen** de volgende waarden voor uw verbinding:

   * **Naam:** de naam van de verbinding.
   * **Verbindingstype:** **Site-to-site(IPSec) selecteren**
   * **Virtuele netwerkgateway:** hiervoor geldt een vaste waarde, omdat u verbinding maakt vanaf deze gateway.
   * **Lokale netwerkgateway:** Deze verbinding verbindt de virtuele netwerkgateway met de lokale netwerkgateway. Kies een van de lokale netwerkgateways die u eerder hebt gemaakt.
   * **Gedeelde sleutel:** Voer een gedeelde sleutel in.
   * **IKE-protocol:** Kies het IKE-protocol.
   * **BGP:** Kies **BGP inschakelen** als de verbinding boven BGP is.
3. Klik op **OK** om uw verbinding te maken.
4. U kunt de verbinding bekijken op de pagina **Verbindingen** van de virtuele netwerkgateway.

   ![Verbinding](./media/connect-virtual-network-gateway-vwan/connect.png "verbinding")
5. Herhaal de voorgaande stappen om een tweede verbinding te maken. Selecteer voor de tweede verbinding de andere lokale netwerkgateway die u hebt gemaakt.

## <a name="6-test-connections"></a><a name="test"></a>6. Testverbindingen

U de connectiviteit testen door twee virtuele machines te maken, een aan de zijkant van de virtuele netwerkgateway van de VPN-gateway en een in een virtueel netwerk voor het Virtuele WAN en vervolgens de twee virtuele machines te pingen.

1. Maak een virtuele machine in het virtuele netwerk (Test1-VNet) voor Azure VPN Gateway (Test1-VNG). Maak de virtuele machine niet in het GatewaySubnet.
2. Maak een ander virtueel netwerk om verbinding te maken met het virtuele WAN. Maak een virtuele machine in een subnet van dit virtuele netwerk. Dit virtuele netwerk mag geen virtuele netwerkgateways bevatten. U snel een virtueel netwerk maken met de PowerShell-stappen in het [site-to-site-verbindingsartikel.](virtual-wan-site-to-site-portal.md#vnet) Zorg ervoor dat u de waarden wijzigt voordat u de cmdlets uitvoert.
3. Sluit het VNet aan op de Virtuele WAN-hub. Selecteer **virtuele netwerkverbindingen**op de pagina voor uw virtuele WAN en **+Verbinding toevoegen**. Vul de volgende velden in op de pagina **Verbinding toevoegen**:

    * **Verbindingsnaam** - voer een naam in voor uw verbinding.
    * **Hubs** - selecteer de hub die u wilt koppelen aan deze verbinding.
    * **Abonnement** - controleer of het abonnement klopt.
    * **Virtueel netwerk** - selecteer het virtuele netwerk dat met deze hub wilt verbinden. Het virtuele netwerk mag geen bestaande virtuele netwerkgateway hebben.
4. Klik op **OK** om de virtuele netwerkverbinding te maken.
5. Connectiviteit is nu ingesteld tussen de VM's. U moet de ene VM van de andere kunnen pingen, tenzij er firewalls of ander beleid zijn dat de communicatie blokkeert.

## <a name="next-steps"></a>Volgende stappen

Zie [Een aangepast IPsec-beleid configureren voor stappen](virtual-wan-custom-ipsec-portal.md)voor het configureren van een aangepast IPsec-beleid voor Virtueel WAN .
Zie Over Azure Virtual [WAN](virtual-wan-about.md) en de [veelgestelde vragen](virtual-wan-faq.md)over Azure Virtual WAN en de veelgestelde vragen over Azure Virtual WAN .