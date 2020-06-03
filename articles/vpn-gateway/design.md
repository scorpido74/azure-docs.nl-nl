---
title: Over het ontwerp van Azure VPN Gateway
description: Meer informatie over de manieren waarop u een VPN-gateway topologie kunt ontwerpen om verbinding te maken met virtuele Azure-netwerken.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/01/2020
ms.author: cherylmc
ms.openlocfilehash: d7b9077af50115e912415d784dc98ace081c0c88
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300321"
---
# <a name="vpn-gateway-design"></a>VPN Gateway ontwerp

Het is belangrijk te weten dat er verschillende configuraties beschikbaar zijn voor VPN-gatewayverbindingen. U moet bepalen welke configuratie het beste aansluit bij uw behoeften. In de volgende secties kunt u ontwerp informatie en topologie diagrammen bekijken over de volgende VPN-gateway verbindingen. Gebruik de diagrammen en beschrijvingen als hulp bij het selecteren van de juiste verbindingstopologie voor uw vereisten. In de diagrammen worden de belangrijkste topologieën van de basis lijn weer gegeven, maar het is mogelijk om complexere configuraties te bouwen met behulp van de diagrammen als richt lijnen.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a><a name="s2smulti"></a>Site-naar-site en multi-site (IPsec-/IKE VPN-tunnel)

### <a name="site-to-site"></a><a name="S2S"></a>Site-naar-site

Een site-naar-site-VPN-gatewayverbinding (S2S) is een verbinding via een VPN-tunnel met IPsec/IKE (IKEv1 of IKEv2). S2S-verbindingen kunnen worden gebruikt voor cross-premises en hybride configuraties. Voor een S2S-verbinding is een VPN-apparaat op locatie vereist waaraan een openbaar IP-adres is toegewezen. Zie [Veelgestelde vragen over VPN Gateways - VPN-apparaten](vpn-gateway-vpn-faq.md#s2s) voor meer informatie over het selecteren van een VPN-apparaat.

![Voorbeeld van een site-naar-site-verbinding met Azure VPN Gateway](./media/design/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a><a name="Multi"></a>Meerdere locaties

Dit type verbinding is een variatie op de site-naar-site-verbinding. U maakt meer dan één VPN-verbinding vanaf uw virtuele netwerkgateway, meestal met verschillende on-premises sites. Als u met meerdere verbindingen werkt, moet u een op een route gebaseerd VPN-type (ook bekend als een dynamische gateway voor klassieke VNets) gebruiken. Omdat elk virtueel netwerk maar één VPN-gateway kan hebben, delen alle verbindingen via de gateway de beschikbare bandbreedte. Dit type verbinding wordt vaak een multi-site-verbinding genoemd.

![Voorbeeld van een verbinding tussen meerdere locaties met Azure VPN Gateway](./media/design/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Implementatiemodellen en -methoden voor site-naar-site en multi-site

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="point-to-site-vpn"></a><a name="P2S"></a>Punt-naar-site-VPN

Met een point-to-site-VPN-gatewayverbinding (P2S) kunt u vanaf een afzonderlijke clientcomputer een beveiligde verbinding maken met uw virtuele netwerk. Een P2S-verbinding wordt tot stand gebracht door deze te starten vanaf de clientcomputer. Deze oplossing is handig voor telewerkers die verbinding willen maken met een Azure VNet vanaf een externe locatie, zoals vanaf thuis of een conferentie. P2S-VPN is ook een uitstekende oplossing in plaats van een S2S-VPN wanneer u maar een paar clients hebt die verbinding moeten maken met een VNet.

Bij P2S-verbindingen is er, in tegenstelling tot bij S2S-verbindingen, geen on-premises openbaar IP-adres en geen VPN-apparaat nodig. P2S-verbindingen kunnen worden gebruikt met S2S-verbindingen via dezelfde VPN-gateway, mits alle configuratievereisten voor beide verbindingen compatibel zijn. Voor meer informatie over point-to-site-verbindingen leest u [About Point-to-Site VPN](point-to-site-about.md) (Over point-to-site-VPN).

![Voorbeeld van een punt-naar-site-verbinding met Azure VPN Gateway](./media/design/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>Implementatiemodellen en -methoden voor P2S

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="vnet-to-vnet-connections-ipsecike-vpn-tunnel"></a><a name="V2V"></a>VNet-naar-VNet-verbindingen (IPsec-/IKE VPN-tunnel)

Het verbinden van een virtueel netwerk met een ander virtueel netwerk (VNet-naar-VNet) lijkt op het verbinden van een VNet met een on-premises locatie. Voor beide connectiviteitstypen wordt een VPN-gateway gebruikt om een beveiligde tunnel met IPsec/IKE te bieden. U kunt zelfs VNet-naar-VNet-communicatie met multi-site-verbindingsconfiguraties combineren. Zo kunt u netwerktopologieën maken waarin cross-premises connectiviteit is gecombineerd met connectiviteit tussen virtuele netwerken.

U kunt de volgende VNets verbinden:

* in dezelfde of verschillende regio's
* in dezelfde of verschillende abonnementen 
* in dezelfde of verschillende implementatiemodellen

![Voorbeeld van een VNet-naar-VNet-verbinding met Azure VPN Gateway](./media/design/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Verbindingen tussen implementatiemodellen

Azure heeft momenteel twee implementatiemodellen: klassiek en Resource Manager. Als u al langer met Azure hebt gewerkt, hebt u waarschijnlijk Azure-VM's en -rolinstanties in een klassiek VNet. De nieuwere VM's en rolinstanties werken mogelijk in een VNet dat is gemaakt in Resource Manager. U kunt de VNets verbinden zodat de resources in het ene VNet direct met de resources in het andere kunnen communiceren.

### <a name="vnet-peering"></a>VNet-peering

Zolang het virtuele netwerk voldoet aan bepaalde vereisten, kunt u VNet-peering gebruiken om uw verbinding te maken. Bij VNet-peering wordt geen virtuele netwerkgateway gebruikt. Zie het artikel [VNet-peering](../virtual-network/virtual-network-peering-overview.md) voor meer informatie.

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Implementatiemodellen en -methoden voor VNet-naar-VNet

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="expressroute-private-connection"></a><a name="ExpressRoute"></a>ExpressRoute (privéverbinding)

Met ExpressRoute kunt u uw on-premises netwerken in de Microsoft Cloud uitbreiden via een persoonlijke verbinding die wordt gefaciliteerd door een connectiviteitsprovider. Met ExpressRoute kunt u verbindingen tot stand brengen met Microsoft Cloud-services, zoals Microsoft Azure, Office 365 en CRM Online. De connectiviteit kan van een wille keurig netwerk (IP VPN), een Point-to-Point Ethernet-netwerk of een virtuele Kruis verbinding via een connectiviteits provider op een functie voor samen locatie zijn.

ExpressRoute-verbindingen gaan niet via het openbare internet. Daardoor zijn ExpressRoute-verbindingen betrouwbaarder en sneller en hebben ze lagere latenties en betere beveiliging dan gewone verbindingen via internet.

Een ExpressRoute-verbinding gebruikt een virtuele netwerkgateway als onderdeel van de vereiste configuratie. In een ExpressRoute-verbinding wordt de virtuele netwerkgateway geconfigureerd met het gatewaytype 'ExpressRoute' in plaats van 'VPN'. Hoewel gegevensoverdracht via een ExpressRoute-circuit standaard niet wordt versleuteld, is het mogelijk een oplossing te maken waarmee u versleuteld verkeer kunt verzenden via een ExpressRoute-circuit. Zie het [technisch overzicht van ExpressRoute](../expressroute/expressroute-introduction.md)voor meer informatie over ExpressRoute.

## <a name="site-to-site-and-expressroute-coexisting-connections"></a><a name="coexisting"></a>Site-naar-site- en ExpressRoute-verbindingen naast elkaar

ExpressRoute is een rechtstreekse privéverbinding van uw WAN (niet via het openbare internet) met Microsoft-services, waaronder Azure. Site-naar-site-VPN-verkeer verplaatst zich versleuteld via het openbare internet. De mogelijkheid om site-naar-site-VPN- en ExpressRoute-verbindingen voor hetzelfde virtuele netwerk te configureren, heeft verschillende voordelen.

U kunt een site-naar-site-VPN configureren als een beveiligd failoverpad voor ExpressRoute of site-naar-site-VPN’s gebruiken om verbinding te maken met sites die geen deel uitmaken van uw netwerk, maar zijn verbonden via ExpressRoute. Houd er rekening mee dat deze configuratie twee gateways vereist voor hetzelfde virtuele netwerk, één met gatewaytype 'VPN' en de andere met gatewaytype 'ExpressRoute'.

![Voorbeeld van ExpressRoute en VPN Gateway in eenzelfde implementatie](./media/design/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>Implementatiemodellen en -methoden voor S2S en ExpressRoute komen naast elkaar voor

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="highly-available-connections"></a><a name="highly-available"></a>Maxi maal beschik bare verbindingen

Zie [Maxi maal beschik bare verbindingen](vpn-gateway-highlyavailable.md)voor het plannen en ontwerpen voor Maxi maal beschik bare verbindingen.

## <a name="next-steps"></a>Volgende stappen

* Zie de [Veelgestelde vragen over VPN Gateway](vpn-gateway-vpn-faq.md) voor meer informatie.

* Meer informatie over [VPN gateway configuratie-instellingen](vpn-gateway-about-vpn-gateway-settings.md).

* Zie [over BGP](vpn-gateway-bgp-overview.md)voor VPN gateway BGP-overwegingen.

* Zie de [Abonnements- en servicebeperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

* Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](../networking/networking-overview.md) van Azure.
