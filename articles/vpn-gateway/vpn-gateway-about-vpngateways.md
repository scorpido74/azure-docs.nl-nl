---
title: Over Azure VPN Gateway
description: Meer informatie over wat een VPN-gateway is en de manieren waarop u een VPN-gateway kunt gebruiken om verbinding te maken met virtuele netwerken in Azure. Inclusief IPsec/IKE-site-naar-site cross-premises- en VNet-naar-VNet-oplossingen, alsmede punt-naar-site-VPN.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 11/13/2019
ms.author: cherylmc
ms.openlocfilehash: 58a92536510d2f434154169cbefff60487a422fa
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075447"
---
# <a name="what-is-vpn-gateway"></a>Wat is VPN Gateway?

Een VPN-gateway is een speciaal soort virtueel-netwerkgateway die wordt gebruikt om versleuteld verkeer te verzenden tussen een virtueel Azure-netwerk en een on-premises locatie via het openbare internet. U kunt een VPN-gateway ook gebruiken om versleuteld verkeer tussen virtuele Azure-netwerken te verzenden via het Microsoft-netwerk. Elk virtueel netwerk kan slechts één VPN-gateway hebben. U kunt echter meerdere verbindingen met dezelfde VPN-gateway maken. Wanneer u meerdere verbindingen naar dezelfde VPN-gateway hebt gemaakt, delen alle VPN-tunnels de bandbreedte die voor de gateway beschikbaar is.

## <a name="whatis"></a>Wat is een virtuele netwerkgateway?

Een virtuele netwerk gateway bestaat uit twee of meer virtuele machines die zijn geïmplementeerd op een specifiek subnet dat u maakt, het *Gateway-subnet*. Virtuele netwerk gateway-Vm's bevatten routerings tabellen en voeren specifieke Gateway Services uit. Deze Vm's worden gemaakt wanneer u de virtuele netwerk gateway maakt. U kunt de Vm's die deel uitmaken van de gateway van het virtuele netwerk niet rechtstreeks configureren.

Een instelling die u configureert voor een virtuele netwerk gateway is het gateway type. Met het gateway type geeft u op hoe de virtuele netwerk gateway wordt gebruikt en welke acties de gateway gaat uitvoeren. Het gateway type ' VPN ' geeft aan dat het type van de virtuele netwerk gateway dat is gemaakt een VPN-gateway is, in plaats van een ExpressRoute-gateway. Een virtueel netwerk kan twee virtuele netwerk gateways hebben. Eén VPN-gateway en één ExpressRoute-gateway, zoals het geval is met [naast elkaar bestaande](#coexisting) verbindings configuraties. Zie [Soorten gateways](vpn-gateway-about-vpn-gateway-settings.md#gwtype) voor meer informatie.

VPN-gateways kunnen in Azure-beschikbaarheidszones worden geïmplementeerd. Dit brengt tolerantie, schaal baarheid en hogere Beschik baarheid voor virtuele netwerk gateways. Het implementeren van gateways in Azure-beschikbaarheidszones fysiek en logisch scheidt gateways binnen een regio, terwijl uw on-premises netwerk connectiviteit met Azure wordt beschermd tegen storingen op zone niveau. Zie [info over zone-redundante virtuele netwerk gateways in azure-beschikbaarheidszones](about-zone-redundant-vnet-gateways.md)

Het maken van een gateway voor een virtueel netwerk kan tot 45 minuten duren. Wanneer u een gateway voor een virtueel netwerk maakt, worden gateway-VM's in het gatewaysubnet geïmplementeerd en geconfigureerd met de instellingen die u opgeeft. Nadat u een VPN-gateway hebt gemaakt, kunt u een IPsec/IKE-VPN-tunnelverbinding maken tussen die VPN-gateway en een andere VPN-gateway (VNet-naar-VNet) of een cross-premises IPsec/IKE-VPN-tunnelverbinding maken tussen de VPN-gateway en een on-premises VPN-apparaat (site-naar-site). U kunt ook een punt-naar-site-VPN-verbinding (VPN via OpenVPN, IKEv2 of SSTP) maken, waarmee u verbinding kunt maken met uw virtuele netwerk vanaf een externe locatie, zoals vanuit een conferentie of thuis.

## <a name="configuring"></a>Een VPN-gateway configureren

Een VPN-gatewayverbinding is afhankelijk van meerdere resources die zijn geconfigureerd met specifieke instellingen. De meeste resources kunnen afzonderlijk worden geconfigureerd, hoewel sommige resources in een bepaalde volgorde moeten worden geconfigureerd.

### <a name="settings"></a>Instellingen

De instellingen die u voor elke resource hebt gekozen, zijn essentieel om een geslaagde verbinding te maken. Zie voor meer informatie over afzonderlijke resources en de instellingen voor VPN Gateway [Over VPN Gateway-instellingen](vpn-gateway-about-vpn-gateway-settings.md). Dit artikel bevat informatie over de gatewaytypen, gateway-SKU's, VPN-typen, typen verbindingen, gatewaysubnetten, lokale netwerkgateways en verschillende andere resource-instellingen die u misschien wilt gebruiken.

### <a name="tools"></a>Implementatiehulpmiddelen

U kunt beginnen met het maken en configureren van resources met een configuratiehulpprogramma, zoals Azure Portal. U kunt later besluiten over te schakelen naar een ander hulpprogramma, zoals PowerShell, om aanvullende resources te configureren, of om desgewenst bestaande resources te wijzigen. Op dit moment is het niet mogelijk om elke resource en resource-instelling in Azure Portal te configureren. De instructies in de artikelen voor elke verbindingstopologie geven aan of een specifiek confihuratiehulpprogramma nodig is. 

### <a name="models"></a>Implementatiemodel

Er zijn momenteel twee implementatiemodellen voor Azure. Wanneer u een VPN-gateway configureert, zijn de stappen die u moet volgen, afhankelijk van het implementatiemodel waarmee u het virtuele netwerk hebt gemaakt. Als u bijvoorbeeld uw VNet hebt gemaakt met het klassieke implementatiemodel, gebruikt u de richtlijnen en instructies voor het klassieke implementatiemodel om de VPN-gateway te maken en de instellingen te configureren. Zie [Het Resource Manager-implementatiemodel en het klassieke implementatiemodel begrijpen](../azure-resource-manager/resource-manager-deployment-model.md) voor meer informatie over de implementatiemodellen.

### <a name="planningtable"></a>Tabel plannen

De volgende tabel kan u helpen bij het kiezen van de beste connectiviteitsoptie voor uw oplossing.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

## <a name="gwsku"></a>Gateway-SKU's

Wanneer u een virtuele netwerkgateway maakt, geeft u de gewenste gateway-SKU op. Selecteer de SKU die aan uw vereisten voldoet op basis van de typen werkbelasting, doorvoer, functies en SLA's.

* Zie het artikel [VPN gateway-Gateway-sku's](vpn-gateway-about-vpn-gateway-settings.md#gwsku) voor meer informatie over Gateway-sku's, waaronder ondersteunde functies, productie en dev-test en configuratie stappen.
* Zie [werken met oudere sku's](vpn-gateway-about-skus-legacy.md)voor meer informatie over oudere sku's.

### <a name="benchmark"></a>Gateway-SKU's per tunnel, verbinding en doorvoer

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="diagrams"></a>Diagrammen over de verbindingstopologie

Het is belangrijk te weten dat er verschillende configuraties beschikbaar zijn voor VPN-gatewayverbindingen. U moet bepalen welke configuratie het beste aansluit bij uw behoeften. In de gedeelten hieronder kunt u informatie en topologiediagrammen over de volgende VPN-gatewayverbindingen bekijken. In de tabellen worden de volgende zaken weergegeven:

* Beschikbaar implementatiemodel
* Beschikbare configuratiehulpprogramma's
* Rechtstreekse koppelingen naar een artikel, indien beschikbaar

Gebruik de diagrammen en beschrijvingen als hulp bij het selecteren van de juiste verbindingstopologie voor uw vereisten. De diagrammen tonen de belangrijkste basistopologieën, maar het is mogelijk om met de diagrammen als richtlijn complexere configuraties te bouwen.

## <a name="s2smulti"></a>Site-naar-site en multi-site (IPsec-/IKE VPN-tunnel)

### <a name="S2S"></a>Site-naar-site

Een site-naar-site-VPN-gatewayverbinding (S2S) is een verbinding via een VPN-tunnel met IPsec/IKE (IKEv1 of IKEv2). S2S-verbindingen kunnen worden gebruikt voor cross-premises en hybride configuraties. Voor een S2S-verbinding moet een VPN-apparaat on-premises aanwezig zijn waaraan een openbaar IP-adres is toegewezen en dat zich niet achter een NAT bevindt. Zie [Veelgestelde vragen over VPN Gateways - VPN-apparaten](vpn-gateway-vpn-faq.md#s2s) voor meer informatie over het selecteren van een VPN-apparaat.

![Voorbeeld van een site-naar-site-verbinding met Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="Multi"></a>Multi-site

Dit type verbinding is een variatie op de site-naar-site-verbinding. U maakt meer dan één VPN-verbinding vanaf uw virtuele netwerkgateway, meestal met verschillende on-premises sites. Als u met meerdere verbindingen werkt, moet u een op een route gebaseerd VPN-type (ook bekend als een dynamische gateway voor klassieke VNets) gebruiken. Omdat elk virtueel netwerk maar één VPN-gateway kan hebben, delen alle verbindingen via de gateway de beschikbare bandbreedte. Dit type verbinding wordt vaak een multi-site-verbinding genoemd.

![Voorbeeld van een verbinding tussen meerdere locaties met Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Implementatiemodellen en -methoden voor site-naar-site en multi-site

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="P2S"></a>Punt-naar-site-VPN

Met een point-to-site-VPN-gatewayverbinding (P2S) kunt u vanaf een afzonderlijke clientcomputer een beveiligde verbinding maken met uw virtuele netwerk. Een P2S-verbinding wordt tot stand gebracht door deze te starten vanaf de clientcomputer. Deze oplossing is handig voor telewerkers die verbinding willen maken met een Azure VNet vanaf een externe locatie, zoals vanaf thuis of een conferentie. P2S-VPN is ook een uitstekende oplossing in plaats van een S2S-VPN wanneer u maar een paar clients hebt die verbinding moeten maken met een VNet.

Bij P2S-verbindingen is er, in tegenstelling tot bij S2S-verbindingen, geen on-premises openbaar IP-adres en geen VPN-apparaat nodig. P2S-verbindingen kunnen worden gebruikt met S2S-verbindingen via dezelfde VPN-gateway, mits alle configuratievereisten voor beide verbindingen compatibel zijn. Voor meer informatie over point-to-site-verbindingen leest u [About Point-to-Site VPN](point-to-site-about.md) (Over point-to-site-VPN).

![Voorbeeld van een punt-naar-site-verbinding met Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>Implementatiemodellen en -methoden voor P2S

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="V2V"></a>VNet-naar-VNet-verbindingen (IPsec-/IKE VPN-tunnel)

Het verbinden van een virtueel netwerk met een ander virtueel netwerk (VNet-naar-VNet) lijkt op het verbinden van een VNet met een on-premises locatie. Voor beide connectiviteitstypen wordt een VPN-gateway gebruikt om een beveiligde tunnel met IPsec/IKE te bieden. U kunt zelfs VNet-naar-VNet-communicatie met multi-site-verbindingsconfiguraties combineren. Zo kunt u netwerktopologieën maken waarin cross-premises connectiviteit is gecombineerd met connectiviteit tussen virtuele netwerken.

U kunt de volgende VNets verbinden:

* in dezelfde of verschillende regio's
* in dezelfde of verschillende abonnementen 
* in dezelfde of verschillende implementatiemodellen

![Voorbeeld van een VNet-naar-VNet-verbinding met Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Verbindingen tussen implementatiemodellen

Azure heeft momenteel twee implementatiemodellen: klassiek en Resource Manager. Als u al langer met Azure hebt gewerkt, hebt u waarschijnlijk Azure-VM's en -rolinstanties in een klassiek VNet. De nieuwere VM's en rolinstanties werken mogelijk in een VNet dat is gemaakt in Resource Manager. U kunt de VNets verbinden zodat de resources in het ene VNet direct met de resources in het andere kunnen communiceren.

### <a name="vnet-peering"></a>VNet-peering

Zolang het virtuele netwerk voldoet aan bepaalde vereisten, kunt u VNet-peering gebruiken om uw verbinding te maken. Bij VNet-peering wordt geen virtuele netwerkgateway gebruikt. Zie het artikel [VNet-peering](../virtual-network/virtual-network-peering-overview.md) voor meer informatie.

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Implementatiemodellen en -methoden voor VNet-naar-VNet

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="ExpressRoute"></a>ExpressRoute (privéverbinding)

Met ExpressRoute kunt u uw on-premises netwerken in de Microsoft Cloud uitbreiden via een persoonlijke verbinding die wordt gefaciliteerd door een connectiviteitsprovider. Met ExpressRoute kunt u verbindingen tot stand brengen met Microsoft Cloud-services, zoals Microsoft Azure, Office 365 en CRM Online. Via een connectiviteitsprovider in een co-locatiefaciliteit is connectiviteit mogelijk vanuit een any-to-any (IP VPN) netwerk, een point-to-point Ethernet-netwerk of een virtuele overlappende verbinding.

ExpressRoute-verbindingen gaan niet via het openbare internet. Daardoor zijn ExpressRoute-verbindingen betrouwbaarder en sneller en hebben ze lagere latenties en betere beveiliging dan gewone verbindingen via internet.

Een ExpressRoute-verbinding gebruikt een virtuele netwerkgateway als onderdeel van de vereiste configuratie. In een ExpressRoute-verbinding wordt de virtuele netwerkgateway geconfigureerd met het gatewaytype 'ExpressRoute' in plaats van 'VPN'. Hoewel gegevensoverdracht via een ExpressRoute-circuit standaard niet wordt versleuteld, is het mogelijk een oplossing te maken waarmee u versleuteld verkeer kunt verzenden via een ExpressRoute-circuit. Zie [Technical Overview](../expressroute/expressroute-introduction.md) (Technisch overzicht) voor meer informatie over ExpressRoute.

## <a name="coexisting"></a>Site-naar-site- en ExpressRoute-verbindingen naast elkaar

ExpressRoute is een rechtstreekse privéverbinding van uw WAN (niet via het openbare internet) met Microsoft-services, waaronder Azure. Site-naar-site-VPN-verkeer verplaatst zich versleuteld via het openbare internet. De mogelijkheid om site-naar-site-VPN- en ExpressRoute-verbindingen voor hetzelfde virtuele netwerk te configureren, heeft verschillende voordelen.

U kunt een site-naar-site-VPN configureren als een beveiligd failoverpad voor ExpressRoute of site-naar-site-VPN’s gebruiken om verbinding te maken met sites die geen deel uitmaken van uw netwerk, maar zijn verbonden via ExpressRoute. Houd er rekening mee dat deze configuratie twee gateways vereist voor hetzelfde virtuele netwerk, één met gatewaytype 'VPN' en de andere met gatewaytype 'ExpressRoute'.

![Voorbeeld van ExpressRoute en VPN Gateway in eenzelfde implementatie](./media/vpn-gateway-about-vpngateways/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>Implementatiemodellen en -methoden voor S2S en ExpressRoute komen naast elkaar voor

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="pricing"></a>Prijzen

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

Zie [Gateway-SKU's](vpn-gateway-about-vpn-gateway-settings.md#gwsku) voor meer informatie over gateway-SKU's voor VPN Gateway.

## <a name="faq"></a>Veelgestelde vragen

Zie [Veelgestelde vragen VPN Gateway](vpn-gateway-vpn-faq.md) voor veelgestelde vragen over VPN Gateway.

## <a name="next-steps"></a>Volgende stappen

- Zie de [Veelgestelde vragen over VPN Gateway](vpn-gateway-vpn-faq.md) voor meer informatie.
- Zie de [Abonnements- en servicebeperkingen](../azure-subscription-service-limits.md#networking-limits).
- Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](../networking/networking-overview.md) van Azure.
