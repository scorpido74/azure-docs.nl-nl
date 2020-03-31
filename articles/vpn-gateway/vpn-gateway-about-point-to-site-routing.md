---
title: 'Azure VPN-gateway: over P2S-routering'
description: Dit artikel helpt u te begrijpen hoe Point-to-Site VPN-routering zich gedraagt.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: anzaman
ms.openlocfilehash: 4821f2eb694a36cf0570008b3e62ce39999c58d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239755"
---
# <a name="about-point-to-site-vpn-routing"></a>Over point-to-site-VPN-routering

In dit artikel u begrijpen hoe Azure Point-to-Site VPN-routering zich gedraagt. P2S VPN-routeringsgedrag is afhankelijk van het client-besturingssysteem, het protocol dat wordt gebruikt voor de VPN-verbinding en hoe de virtuele netwerken (VNets) met elkaar zijn verbonden.

Azure ondersteunt momenteel twee protocollen voor externe toegang, IKEv2 en SSTP. IKEv2 wordt ondersteund op veel besturingssystemen voor klanten, waaronder Windows, Linux, MacOS, Android en iOS. SSTP wordt alleen ondersteund op Windows. Als u een wijziging aanbrengt in de topologie van uw netwerk en Windows VPN-clients hebt, moet het VPN-clientpakket voor Windows-clients opnieuw worden gedownload en geïnstalleerd om de wijzigingen op de client toe te passen.

> [!NOTE]
> Dit artikel is alleen van toepassing op IKEv2.
>

## <a name="about-the-diagrams"></a><a name="diagrams"></a>Over de diagrammen

Er zijn een aantal verschillende diagrammen in dit artikel. Elke sectie toont een andere topologie of configuratie. Voor de toepassing van dit artikel werken Site-to-Site (S2S) en VNet-to-VNet-verbindingen op dezelfde manier, omdat beide IPsec-tunnels zijn. Alle VPN-gateways in dit artikel zijn routegebaseerd.

## <a name="one-isolated-vnet"></a><a name="isolatedvnet"></a>Een geïsoleerde VNet

De Point-to-Site VPN-gatewayverbinding in dit voorbeeld is voor een VNet dat niet is verbonden of peered met een ander virtueel netwerk (VNet1). In dit voorbeeld hebben clients toegang tot VNet1.

![geïsoleerde VNet-routering](./media/vpn-gateway-about-point-to-site-routing/1.jpg "geïsoleerde VNet-routering")

### <a name="address-space"></a>Adresruimte

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>Routes toegevoegd

* Routes toegevoegd aan Windows-clients: 10.1.0.0/16, 192.168.0.0/24

* Routes toegevoegd aan niet-Windows-clients: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Toegang

* Windows-clients hebben toegang tot VNet1

* Niet-Windows-clients hebben toegang tot VNet1

## <a name="multiple-peered-vnets"></a><a name="multipeered"></a>Meerdere peered VNets

In dit voorbeeld is de Point-to-Site VPN-gatewayverbinding voor VNet1. VNet1 is peered met VNet2. VNet 2 is peered met VNet3. VNet1 is peered met VNet4. Er is geen direct peering tussen VNet1 en VNet3. VNet1 heeft "Allow gateway transit" en VNet2 en VNet4 hebben "Use remote gateways" ingeschakeld.

Clients die Windows gebruiken, hebben toegang tot direct peered VNets, maar de VPN-client moet opnieuw worden gedownload als er wijzigingen worden aangebracht in VNet-peering of de netwerktopologie. Niet-Windows-clients hebben toegang tot direct peered VNets. Toegang is niet transitief en is beperkt tot alleen direct gepeerde VNets.

![meerdere peered VNets](./media/vpn-gateway-about-point-to-site-routing/2.jpg "meerdere peered VNets")

### <a name="address-space"></a>Adresruimte:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>Routes toegevoegd

* Routes toegevoegd aan Windows-clients: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Routes toegevoegd aan niet-Windows-clients: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Toegang

* Windows-clients hebben toegang tot VNet1, VNet2 en VNet4, maar de VPN-client moet opnieuw worden gedownload om wijzigingen in de topologie van kracht te kunnen maken.

* Niet-Windows-clients hebben toegang tot VNet1, VNet2 en VNet4

## <a name="multiple-vnets-connected-using-an-s2s-vpn"></a><a name="multis2s"></a>Meerdere VNets verbonden via een S2S VPN

In dit voorbeeld is de Point-to-Site VPN-gatewayverbinding voor VNet1. VNet1 is verbonden met VNet2 via een Site-to-Site VPN-verbinding. VNet2 is verbonden met VNet3 via een Site-to-Site VPN-verbinding. Er is geen directe peering of Site-to-Site VPN-verbinding tussen VNet1 en VNet3. Alle site-to-site-verbindingen worden niet uitgevoerd met BGP voor routering.

Clients die Windows gebruiken, of een ander ondersteund besturingssysteem, hebben alleen toegang tot VNet1. Om toegang te krijgen tot extra VNets, moet BGP worden gebruikt.

![meerdere VNets en S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "meerdere VNets en S2S")

### <a name="address-space"></a>Adresruimte

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Routes toegevoegd

* Routes toegevoegd aan Windows-clients: 10.1.0.0/16, 192.168.0.0/24

* Routes toegevoegd aan niet-Windows-clients: 10.1.0.0/16, 10.2.0.0/16, 192.168.0/24

### <a name="access"></a>Toegang

* Windows-clients hebben alleen toegang tot VNet1

* Niet-Windows-clients hebben alleen toegang tot VNet1

## <a name="multiple-vnets-connected-using-an-s2s-vpn-bgp"></a><a name="multis2sbgp"></a>Meerdere VNets verbonden via een S2S VPN (BGP)

In dit voorbeeld is de Point-to-Site VPN-gatewayverbinding voor VNet1. VNet1 is verbonden met VNet2 via een Site-to-Site VPN-verbinding. VNet2 is verbonden met VNet3 via een Site-to-Site VPN-verbinding. Er is geen directe peering of Site-to-Site VPN-verbinding tussen VNet1 en VNet3. Op alle site-to-site-verbindingen wordt BGP uitgevoerd voor routering.

Clients die Windows gebruiken, of een ander ondersteund besturingssysteem, hebben toegang tot alle VNets die zijn verbonden via een Site-to-Site VPN-verbinding, maar routes naar verbonden VNets moeten handmatig worden toegevoegd aan de Windows-clients.

![meerdere VNets en S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "meerdere VNets en S2S BGP")

### <a name="address-space"></a>Adresruimte

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Routes toegevoegd

* Routes toegevoegd aan Windows-clients: 10.1.0.0/16

* Routes toegevoegd aan niet-Windows-clients: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Toegang

* Windows-clients hebben toegang tot VNet1, VNet2 en VNet3, maar routes naar VNet2 en VNet3 moeten handmatig worden toegevoegd.

* Niet-Windows-clients hebben toegang tot VNet1, VNet2 en VNet3

## <a name="one-vnet-and-a-branch-office"></a><a name="vnetbranch"></a>Eén VNet en een bijkantoor

In dit voorbeeld is de Point-to-Site VPN-gatewayverbinding voor VNet1. VNet1 is niet verbonden/ peered met een ander virtueel netwerk, maar is verbonden met een on-premises site via een Site-to-Site VPN-verbinding die geen BGP uitvoert.

Windows- en niet-Windows-clients hebben alleen toegang tot VNet1.

![routering met een VNet en een bijkantoor](./media/vpn-gateway-about-point-to-site-routing/5.jpg "routering met een VNet en een bijkantoor")

### <a name="address-space"></a>Adresruimte

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Routes toegevoegd

* Routes toegevoegd aan Windows-clients: 10.1.0.0/16, 192.168.0.0/24

* Routes toegevoegd aan niet-Windows-clients: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Toegang

* Windows-clients hebben alleen toegang tot VNet1

* Niet-Windows-clients hebben alleen toegang tot VNet1

## <a name="one-vnet-and-a-branch-office-bgp"></a><a name="vnetbranchbgp"></a>Eén VNet en een bijkantoor (BGP)

In dit voorbeeld is de Point-to-Site VPN-gatewayverbinding voor VNet1. VNet1 is niet verbonden of peered met een ander virtueel netwerk, maar is verbonden met een on-premises site (Site1) via een Site-to-Site VPN-verbinding met BGP.

Windows-clients hebben toegang tot het VNet en het filiaal (Site1), maar de routes naar Site1 moeten handmatig aan de client worden toegevoegd. Niet-Windows-clients hebben toegang tot het VNet en het on-premises filiaal.

![één VNet en een bijkantoor (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "één VNet en een bijkantoor")

### <a name="address-space"></a>Adresruimte

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Routes toegevoegd

* Routes toegevoegd aan Windows-clients: 10.1.0.0/16, 192.168.0.0/24

* Routes toegevoegd aan niet-Windows-clients: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Toegang

* Windows-clients hebben toegang tot VNet1 en Site1, maar routes naar Site1 moeten handmatig worden toegevoegd.

* Niet-Windows-clients hebben toegang tot VNet1 en Site1.


## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office"></a><a name="multivnets2sbranch"></a>Meerdere VNets verbonden via S2S en een bijkantoor

In dit voorbeeld is de Point-to-Site VPN-gatewayverbinding voor VNet1. VNet1 is verbonden met VNet2 via een Site-to-Site VPN-verbinding. VNet2 is verbonden met VNet3 via een Site-to-Site VPN-verbinding. Er is geen directe peering of Site-to-Site VPN-tunnel tussen de VNet1- en VNet3-netwerken. VNet3 is verbonden met een filiaal (Site1) via een Site-to-Site VPN-verbinding. Alle VPN-verbindingen draaien niet op BGP.

Alle clients hebben alleen toegang tot VNet1.

![multi-VNet S2S en bijkantoor](./media/vpn-gateway-about-point-to-site-routing/7.jpg "multi-VNet S2S en bijkantoor")

### <a name="address-space"></a>Adresruimte

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Routes toegevoegd

* Routes toegevoegd aan Windows-clients: 10.1.0.0/16, 192.168.0.0/24

* Routes toegevoegd aan niet-Windows-clients: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Toegang

* De Windows-clients hebben alleen toegang tot VNet1

* Niet-Windows-clients hebben alleen toegang tot VNet1

## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office-bgp"></a><a name="multivnets2sbranchbgp"></a>Meerdere VNets verbonden met S2S en een bijkantoor (BGP)

In dit voorbeeld is de Point-to-Site VPN-gatewayverbinding voor VNet1. VNet1 is verbonden met VNet2 via een Site-to-Site VPN-verbinding. VNet2 is verbonden met VNet3 via een Site-to-Site VPN-verbinding. Er is geen directe peering of Site-to-Site VPN-tunnel tussen de VNet1- en VNet3-netwerken. VNet3 is verbonden met een filiaal (Site1) via een Site-to-Site VPN-verbinding. Alle VPN-verbindingen draaien BGP.

Clients die Windows gebruiken, hebben toegang tot VNets en sites die zijn verbonden via een Site-to-Site VPN-verbinding, maar de routes naar VNet2, VNet3 en Site1 moeten handmatig aan de client worden toegevoegd. Niet-Windows-clients hebben toegang tot VNets en sites die zijn verbonden via een Site-to-Site VPN-verbinding zonder handmatige tussenkomst. De toegang is transitief en clients hebben toegang tot bronnen in alle verbonden VNets en sites (on-premises).

![multi-VNet S2S en bijkantoor](./media/vpn-gateway-about-point-to-site-routing/8.jpg "multi-VNet S2S en bijkantoor")

### <a name="address-space"></a>Adresruimte

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Routes toegevoegd

* Routes toegevoegd aan Windows-clients: 10.1.0.0/16, 192.168.0.0/24

* Routes toegevoegd aan niet-Windows-clients: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Toegang

* De Windows-clients hebben toegang tot VNet1, VNet2, VNet3 en Site1, maar routes naar VNet2, VNet3 en Site1 moeten handmatig aan de client worden toegevoegd.

* Niet-Windows-clients hebben toegang tot VNet1, Vnet2, VNet3 en Site1.

## <a name="next-steps"></a>Volgende stappen

Zie [Een P2S VPN maken met behulp van de Azure-portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md) om te beginnen met het maken van uw P2S VPN.
