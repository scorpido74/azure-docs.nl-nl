---
title: Over Azure Point-to-site-route ring | Microsoft Docs
description: Dit artikel helpt u inzicht te krijgen in de werking van punt-naar-site VPN-route ring.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/08/2019
ms.author: anzaman
ms.openlocfilehash: ced1bc647f93beec73b8101a952944f31e497658
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693225"
---
# <a name="about-point-to-site-vpn-routing"></a>Over point-to-site-VPN-routering

Dit artikel helpt u inzicht te krijgen in de werking van Azure Point-to-site VPN-route ring. Het gedrag van P2S VPN-route ring is afhankelijk van het client besturingssysteem, het protocol dat wordt gebruikt voor de VPN-verbinding en hoe de virtuele netwerken (VNets) met elkaar zijn verbonden.

Azure ondersteunt momenteel twee protocollen voor externe toegang, IKEv2 en SSTP. IKEv2 wordt ondersteund op veel client besturingssystemen, waaronder Windows, Linux, MacOS, Android en iOS. SSTP wordt alleen ondersteund in Windows. Als u een wijziging aanbrengt in de topologie van uw netwerk en Windows VPN-clients hebt, moet het VPN-client pakket voor Windows-clients opnieuw worden gedownload en geïnstalleerd om de wijzigingen toe te passen op de client.

> [!NOTE]
> Dit artikel is alleen van toepassing op IKEv2.
>

## <a name="diagrams"></a>Over de diagrammen

Dit artikel bevat een aantal verschillende diagrammen. In elke sectie wordt een andere topologie of configuratie weer gegeven. Voor de doel einden van dit artikel functioneren site-naar-site (S2S) en VNet-naar-VNet-verbindingen op dezelfde manier, zoals IPsec-tunnels. Alle VPN-gateways in dit artikel zijn op route gebaseerd.

## <a name="isolatedvnet"></a>Eén geïsoleerd VNet

De punt-naar-site-VPN-gateway verbinding in dit voor beeld is voor een VNet dat niet is verbonden of die is gekoppeld aan een ander virtueel netwerk (VNet1). In dit voor beeld hebben clients toegang tot VNet1.

![geïsoleerd VNet-route ring](./media/vpn-gateway-about-point-to-site-routing/1.jpg "geïsoleerd VNet-route ring")

### <a name="address-space"></a>Adresruimte

* VNet1:10.1.0.0/16

### <a name="routes-added"></a>Routes toegevoegd

* Routes die zijn toegevoegd aan Windows-clients: 10.1.0.0/16, 192.168.0.0/24

* Routes die zijn toegevoegd aan niet-Windows-clients: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Toegang

* Windows-clients hebben toegang tot VNet1

* Niet-Windows-clients hebben toegang tot de VNet1

## <a name="multipeered"></a>Meerdere peered VNets

In dit voor beeld is de punt-naar-site-VPN-gateway verbinding voor VNet1. VNet1 is gekoppeld aan VNet2. VNet 2 is gekoppeld aan VNet3. VNet1 is gekoppeld aan en vnet4. Er is geen rechtstreekse peering tussen VNet1 en VNet3. VNet1 heeft ' door gateway door geven toestaan ' en VNet2 ' externe gateways gebruiken ' ingeschakeld.

Clients die Windows gebruiken, hebben toegang tot rechtstreeks peered VNets, maar de VPN-client moet opnieuw worden gedownload als er wijzigingen zijn aangebracht in VNet-peering of de netwerk topologie. Niet-Windows-clients hebben toegang tot rechtstreeks peered VNets. Toegang is niet transitief en is beperkt tot alleen rechtstreeks peered VNets.

![meerdere peered VNets](./media/vpn-gateway-about-point-to-site-routing/2.jpg "meerdere peered VNets")

### <a name="address-space"></a>Adres ruimte:

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* En vnet4:10.4.0.0/16

### <a name="routes-added"></a>Routes toegevoegd

* Routes die zijn toegevoegd aan Windows-clients: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Routes die zijn toegevoegd aan niet-Windows-clients: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Toegang

* Windows-clients hebben toegang tot VNet1, VNet2 en en vnet4, maar de VPN-client moet opnieuw worden gedownload om de topologie wijzigingen van kracht te laten worden.

* Niet-Windows-clients hebben toegang tot VNet1, VNet2 en en vnet4

## <a name="multis2s"></a>Meerdere VNets zijn verbonden via een S2S-VPN

In dit voor beeld is de punt-naar-site-VPN-gateway verbinding voor VNet1. VNet1 is verbonden met VNet2 met behulp van een site-naar-site-VPN-verbinding. VNet2 is verbonden met VNet3 met behulp van een site-naar-site-VPN-verbinding. Er is geen rechtstreekse peering of site-naar-site-VPN-verbinding tussen VNet1 en VNet3. Voor alle site-naar-site-verbindingen wordt BGP niet uitgevoerd voor route ring.

Clients die gebruikmaken van Windows of een ander ondersteund besturings systeem, hebben alleen toegang tot VNet1. Om toegang te krijgen tot extra VNets moet BGP worden gebruikt.

![meerdere VNets en S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "meerdere VNets en S2S")

### <a name="address-space"></a>Adresruimte

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

### <a name="routes-added"></a>Routes toegevoegd

* Routes die zijn toegevoegd aan Windows-clients: 10.1.0.0/16, 192.168.0.0/24

* Routes die zijn toegevoegd aan niet-Windows-clients: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Toegang

* Windows-clients hebben alleen toegang tot VNet1

* Niet-Windows-clients hebben alleen toegang tot VNet1

## <a name="multis2sbgp"></a>Meerdere VNets-verbindingen met behulp van een S2S VPN (BGP)

In dit voor beeld is de punt-naar-site-VPN-gateway verbinding voor VNet1. VNet1 is verbonden met VNet2 met behulp van een site-naar-site-VPN-verbinding. VNet2 is verbonden met VNet3 met behulp van een site-naar-site-VPN-verbinding. Er is geen rechtstreekse peering of site-naar-site-VPN-verbinding tussen VNet1 en VNet3. Voor alle site-naar-site-verbindingen wordt BGP voor route ring uitgevoerd.

Clients die gebruikmaken van Windows of een ander ondersteund besturings systeem, hebben toegang tot alle VNets die zijn verbonden met een site-naar-site-VPN-verbinding, maar routes naar verbonden VNets moeten hand matig worden toegevoegd aan de Windows-clients.

![meerdere VNets en S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "meerdere VNets en S2S BGP")

### <a name="address-space"></a>Adresruimte

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

### <a name="routes-added"></a>Routes toegevoegd

* Routes die zijn toegevoegd aan Windows-clients: 10.1.0.0/16

* Routes die zijn toegevoegd aan niet-Windows-clients: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Toegang

* Windows-clients hebben toegang tot VNet1, VNet2 en VNet3, maar routes naar VNet2 en VNet3 moeten hand matig worden toegevoegd.

* Niet-Windows-clients hebben toegang tot VNet1, VNet2 en VNet3

## <a name="vnetbranch"></a>Eén VNet en een filiaal

In dit voor beeld is de punt-naar-site-VPN-gateway verbinding voor VNet1. VNet1 is niet verbonden met een ander virtueel netwerk, maar is verbonden met een on-premises site via een site-naar-site-VPN-verbinding waarop BGP niet wordt uitgevoerd.

Windows-en niet-Windows-clients hebben alleen toegang tot VNet1.

![route ring met een VNet en een filiaal](./media/vpn-gateway-about-point-to-site-routing/5.jpg "route ring met een VNet en een filiaal")

### <a name="address-space"></a>Adresruimte

* VNet1:10.1.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Routes toegevoegd

* Routes die zijn toegevoegd aan Windows-clients: 10.1.0.0/16, 192.168.0.0/24

* Routes die zijn toegevoegd aan niet-Windows-clients: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Toegang

* Windows-clients hebben alleen toegang tot VNet1

* Niet-Windows-clients hebben alleen toegang tot VNet1

## <a name="vnetbranchbgp"></a>Eén VNet en een filiaal (BGP)

In dit voor beeld is de punt-naar-site-VPN-gateway verbinding voor VNet1. VNet1 is niet verbonden met of gekoppeld aan een ander virtueel netwerk, maar is verbonden met een on-premises site (site1) via een site-naar-site-VPN-verbinding die BGP uitvoert.

Windows-clients hebben toegang tot het VNet en het filiaal (site1), maar de routes naar site1 moeten hand matig worden toegevoegd aan de client. Niet-Windows-clients hebben toegang tot het VNet en de on-premises filialen.

![Eén VNet en een filiaal (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "Eén VNet en een filiaal")

### <a name="address-space"></a>Adresruimte

* VNet1:10.1.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Routes toegevoegd

* Routes die zijn toegevoegd aan Windows-clients: 10.1.0.0/16, 192.168.0.0/24

* Routes die zijn toegevoegd aan niet-Windows-clients: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Toegang

* Windows-clients hebben toegang tot VNet1 en site1, maar routes naar site1 moeten hand matig worden toegevoegd.

* Niet-Windows-clients hebben toegang tot VNet1 en site1.


## <a name="multivnets2sbranch"></a>Meerdere VNets verbonden met behulp van S2S en een filiaal

In dit voor beeld is de punt-naar-site-VPN-gateway verbinding voor VNet1. VNet1 is verbonden met VNet2 met behulp van een site-naar-site-VPN-verbinding. VNet2 is verbonden met VNet3 met behulp van een site-naar-site-VPN-verbinding. Er is geen directe peering of site-naar-site-VPN-tunnel tussen de VNet1-en VNet3-netwerken. VNet3 is verbonden met een filiaal (site1) met behulp van een site-naar-site-VPN-verbinding. Op alle VPN-verbindingen wordt BGP niet uitgevoerd.

Alle clients hebben alleen toegang tot VNet1.

![Multi-VNet S2S en filialen](./media/vpn-gateway-about-point-to-site-routing/7.jpg "Multi-VNet S2S en filialen")

### <a name="address-space"></a>Adresruimte

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Routes toegevoegd

* Routes die zijn toegevoegd aan Windows-clients: 10.1.0.0/16, 192.168.0.0/24

* Routes die zijn toegevoegd aan niet-Windows-clients: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Toegang

* De Windows-clients hebben alleen toegang tot VNet1

* Niet-Windows-clients hebben alleen toegang tot VNet1

## <a name="multivnets2sbranchbgp"></a>Meerdere VNets verbonden met behulp van S2S en een filiaal (BGP)

In dit voor beeld is de punt-naar-site-VPN-gateway verbinding voor VNet1. VNet1 is verbonden met VNet2 met behulp van een site-naar-site-VPN-verbinding. VNet2 is verbonden met VNet3 met behulp van een site-naar-site-VPN-verbinding. Er is geen directe peering of site-naar-site-VPN-tunnel tussen de VNet1-en VNet3-netwerken. VNet3 is verbonden met een filiaal (site1) met behulp van een site-naar-site-VPN-verbinding. Alle VPN-verbindingen met BGP worden uitgevoerd.

Clients die Windows gebruiken, hebben toegang tot VNets en sites die zijn verbonden met een site-naar-site-VPN-verbinding, maar de routes naar VNet2, VNet3 en site1 moeten hand matig worden toegevoegd aan de client. Niet-Windows-clients hebben toegang tot VNets en sites die zijn verbonden met een site-naar-site-VPN-verbinding zonder hand matige tussen komst. De toegang is transitief en clients hebben toegang tot bronnen in alle verbonden VNets en sites (on-premises).

![Multi-VNet S2S en filialen](./media/vpn-gateway-about-point-to-site-routing/8.jpg "Multi-VNet S2S en filialen")

### <a name="address-space"></a>Adresruimte

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Routes toegevoegd

* Routes die zijn toegevoegd aan Windows-clients: 10.1.0.0/16, 192.168.0.0/24

* Routes die zijn toegevoegd aan niet-Windows-clients: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Toegang

* De Windows-clients hebben toegang tot VNet1, VNet2, VNet3 en site1, maar routes naar VNet2, VNet3 en site1 moeten hand matig worden toegevoegd aan de client.

* Niet-Windows-clients hebben toegang tot VNet1, Vnet2, VNet3 en site1.

## <a name="next-steps"></a>Volgende stappen

Zie [een P2S-VPN maken met behulp van de Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md) om uw P2S-VPN te gaan maken.
