---
title: VNet- of VPN-verbindingen configureren en valideren
description: Stapsgewijze instructies voor het configureren en valideren van verschillende Azure VPN-en VNet-implementaties
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: fc4b649ce8d082d8d854c4c19b617c088ff3141c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901891"
---
# <a name="configure-and-validate-vnet-or-vpn-connections"></a>VNet- of VPN-verbindingen configureren en valideren

Deze begeleide walkthrough bevat stapsgewijze instructies voor het configureren en valideren van verschillende Azure VPN-en VNet-implementaties in scenario's zoals Transit routering, VNet-naar-VNet, BGP, multi-site, punt-naar-site, enzovoort.

Azure VPN-gateways bieden u de mogelijkheid om bijna elke soort topologie met verbonden Virtual Network (VNet) in azure te rangschikken: u kunt VNets verbinden tussen verschillende regio's, tussen VNet-typen (Azure Resource Manager versus Klassiek), in azure of met een on-premises hybride omgeving, in verschillende abonnementen, enzovoort. 

## <a name="scenario-1-vnet-to-vnet-vpn-connection"></a>Scenario 1: VNet-naar-VNet-VPN-verbinding

Het verbinden van een virtueel netwerk met een ander virtueel netwerk (VNet-naar-VNet) via VPN is vergelijkbaar met het verbinden van een VNet met een on-premises site locatie. Beide connectiviteits typen gebruiken een VPN-gateway om een beveiligde tunnel met **IPSec/IKE**te bieden. De virtuele netwerken kunnen zich in dezelfde of verschillende regio's bevinden en tot dezelfde of verschillende abonnementen behoren.

![BITMAPAFBEELDING](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Afbeelding 1: VNet-naar-VNet met IPsec

Als uw VNets zich in dezelfde regio bevinden, kunt u overwegen om deze te verbinden met VNet-peering, dat geen VPN-gateway gebruikt, de door Voer verhoogt en de latentie vermindert, selecteert u **vnet-peering configureren en valideren** voor het configureren van een vnet-peering Combi.

Als uw VNets beide zijn gemaakt met behulp van Azure Resource Manager-implementatie model, selecteert u **een resource manager-Vnet configureren en valideren voor een resource manager vnet-verbinding** om een VPN-verbinding te configureren.

Als een van de VNets is gemaakt met behulp van het klassieke Azure-implementatie model, wordt het andere gemaakt door Resource Manager, selecteert u **een klassiek VNet configureren en valideren voor een resource manager VNet-verbinding** om een VPN-verbinding te configureren.

### <a name="configuration-1-configure-vnet-peering-for-two-vnets-in-the-same-region"></a>Configuratie 1: VNet-peering configureren voor twee VNets in dezelfde regio

Voordat u de implementatie van Azure VNet-peering start, moet u ervoor zorgen dat u voldoet aan de volgende vereisten om VNet-peering te configureren:

* De virtuele netwerken die worden gekoppeld, moeten zich in dezelfde Azure-regio bevinden.
* De gekoppelde virtuele netwerken moeten niet-overlappende IP-adres ruimten hebben.
* Peering in virtuele netwerken vindt plaats tussen twee virtuele netwerken. Er is geen afgeleide transitieve relatie tussen peerings. Als VNetA bijvoorbeeld is gekoppeld aan VNetB, en als VNetB is gekoppeld aan VNetC, betekent dit *niet* automatisch dat VNetA is gekoppeld aan VNetC.

Wanneer u aan de vereisten voldoet, kunt u [een zelf studie voor het maken van een virtuele netwerk](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) voor het maken en configureren van de VNet-peering volgen.

Als u de configuratie van de VNet-peering wilt controleren, gebruikt u de volgende methoden:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een account met de benodigde [rollen en machtigingen](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions).
2. In het vak met de tekst *zoeken resources* boven aan de Azure Portal, typt u *virtuele netwerken*. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, klikt u erop.
3. Klik in de Blade **virtuele netwerken** die wordt weer gegeven op het virtuele netwerk waarvoor u een peering wilt maken.
4. In het deel venster dat wordt weer gegeven voor het virtuele netwerk dat u hebt geselecteerd, klikt u op **peerings** in het gedeelte **instellingen** .
5. Klik op de peering waarvan u de configuratie wilt controleren.

![BITMAPAFBEELDING](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Voer met behulp van Azure Power shell de opdracht [Get-AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) uit om de peering van het virtuele netwerk op te halen, bijvoorbeeld:

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connection-type-1-connect-a-resource-manager-vnet-to-anther-resource-manager-vnet-azure-resource-manager-to-azure-resource-manager"></a>Verbindings type 1: Een resource manager-VNet verbinden met Anther Resource Manager VNet (Azure Resource Manager naar Azure Resource Manager)

U kunt een verbinding van één resource manager VNet rechtstreeks naar een ander Resource Manager VNet configureren of de verbinding met IPsec configureren.

### <a name="configuration-2-configure-vpn-connection-between-resource-manager-vnets"></a>Configuratie 2: VPN-verbinding tussen Resource Manager-VNets configureren

Zie [een vnet-naar-vnet VPN-gateway verbinding configureren met behulp van de Azure portal voor het](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)configureren van een verbinding tussen VNets zonder IPSec.

Als u een verbinding met IPsec tussen twee Resource Manager-VNets wilt configureren, volgt u de stappen 1-5 in [een site-naar-site-verbinding maken in de Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) voor elk VNet.

> [!Note]
> Deze stappen werken alleen voor VNets in hetzelfde abonnement. Als uw VNets onder verschillende abonnementen vallen, moet u PowerShell gebruiken om de verbinding te maken. Zie het artikel voor [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps).

### <a name="validate-vpn-connection-between-resource-manager-vnets"></a>VPN-verbinding tussen Resource Manager-VNets valideren

![BITMAPAFBEELDING](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Afbeelding 4-klassieke VNet-verbinding met Azure Resource Manager VNet

Als u wilt controleren of uw VPN-verbinding juist is geconfigureerd, volgt u de instructies:

> [!Note]
> Het nummer na de onderdelen van het virtuele netwerk, zoals ' Vnet 1 ' in de onderstaande stappen, komt overeen met de getallen in afbeelding 4.

1. Controleren op overlappende adres ruimten in de verbonden VNets.

   > [!Note]
   > Er kunnen geen overlappende adres ruimten in Vnet 1 en Vnet 6 zijn. 

2. Controleer of het adres bereik van Azure Resource Manager Vnet 1 nauw keurig is gedefinieerd in **verbindings object** 4.
3. Controleer of Azure Resource Manager Vnet 6-adres bereik nauw keurig is gedefinieerd in **verbindings object** 3.
4. Controleer of de vooraf gedeelde sleutels overeenkomen met beide verbindings objecten 3 en 4.
5. Controleer of de Azure Resource Manager Vnet-gateway 2 VIP nauw keurig is gedefinieerd in het **verbindings object** 4.
6. Controleer of de Azure Resource Manager Vnet-gateway 5 VIP nauw keurig is gedefinieerd in het **verbindings object** 3.

### <a name="connection-type-2-connect-a-classic-vnet-to-a-resource-manager-vnet"></a>Verbindings type 2: Een klassieke VNet verbinden met een Resource Manager VNet

U kunt een verbinding maken tussen VNets die zich in verschillende abonnementen en in verschillende regio's bevinden. U kunt ook verbinding maken met VNets die al verbindingen met on-premises netwerken hebben, zolang u het gateway type hebt geconfigureerd als op route gebaseerd.

Zie voor meer informatie [verbinding maken met virtuele netwerken van verschillende implementatie modellen met behulp van de Azure portal voor het](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal) configureren van een verbinding tussen een klassiek vnet en een resource manager-vnet.

![BITMAPAFBEELDING](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Afbeelding 5-klassieke VNet-verbinding met Azure Resource Manager VNet

Volg de instructies om de configuratie te controleren wanneer u een klassiek VNet verbindt met een Azure Resource Manager VNet:

> [!Note]
> Het nummer na de onderdelen van het virtuele netwerk zoals ' Vnet 1 ' in de onderstaande stappen komen overeen met de getallen in afbeelding 5.

1. Controleren op overlappende adres ruimten in de verbonden VNets.

   > [!Note]
   > Er zijn geen overlappende adres ruimten in Vnet 1 en Vnet 6

2. Controleer of Azure Resource Manager VNet 6-adres bereik nauw keurig is gedefinieerd in de klassieke lokale netwerk definitie 3.
3. Controleer of het adres bereik van het klassieke VNet 1 nauw keurig is gedefinieerd in het Azure Resource Manager **verbindings object** 4.
4. Controleer of de klassieke VNet-gateway 2 VIP nauw keurig is gedefinieerd in het Azure Resource Manager- **verbindings object** 4.
5. Controleer of de Azure Resource Manager VNet-gateway 5 VIP nauw keurig is gedefinieerd in de klassieke **lokale netwerk definitie** 3.
6. Controleer of de vooraf gedeelde sleutels overeenkomen met de verbonden VNets:
   1. Klassiek Vnet-lokale netwerk definitie 3
   2. Azure Resource Manager Vnet-verbindings object 4

## <a name="scenario-2-point-to-site-vpn-connection"></a>Scenario 2: Punt-naar-site-VPN-verbinding

Met een punt-naar-site-configuratie (P2S) kunt u een beveiligde verbinding maken tussen een afzonderlijke clientcomputer en een virtueel netwerk. Punt-naar-site-verbindingen zijn handig als u verbinding wilt maken met uw VNet vanaf een externe locatie, zoals vanaf thuis of een conferentie, of wanneer u slechts enkele clients hebt die verbinding moeten maken met een virtueel netwerk. De P2S-VPN-verbinding wordt door de clientcomputer met de systeemeigen Windows VPN-client gestart. Clienten verbinden met certificaten om te verifiëren.

![BITMAPAFBEELDING](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Afbeelding 2: punt-naar-site-verbinding

Voor Point-to-site-verbindingen is geen VPN-apparaat vereist. P2S maakt de VPN-verbinding via SSTP (Secure Socket Tunneling Protocol). U kunt een punt-naar-site-verbinding met een VNet verbinden door gebruik te maken van een andere implementatie hulpprogramma's en implementatie modellen:

* [Een punt-naar-site-verbinding met een VNet configureren met Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Een punt-naar-site-verbinding met een VNet configureren met behulp van de Azure Portal (klassiek)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Een punt-naar-site-verbinding met een VNet configureren met behulp van PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connections"></a>Uw punt-naar-site-verbindingen valideren

Het artikel [problemen oplossen: Problemen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) met een punt-naar-site-verbinding worden door gang met veelvoorkomende problemen met Point-to-site-verbindingen.

## <a name="scenario-3-multi-site-vpn-connection"></a>Scenario 3: Multi-site VPN-verbinding

U kunt een S2S-verbinding (site-naar-site) toevoegen aan een VNet dat al een S2S-verbinding, een punt-naar-site-verbinding of een VNet-naar-VNet-verbinding heeft. dit type verbinding wordt vaak ook wel een configuratie met **meerdere locaties** genoemd. 

![BITMAPAFBEELDING](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Afbeelding 3: verbinding met meerdere locaties

Azure werkt momenteel met twee implementatie modellen: Resource Manager en klassiek. De twee modellen zijn niet volledig compatibel met elkaar. Als u verbinding tussen **meerdere locaties** en verschillende modellen wilt configureren, raadpleegt u de volgende artikelen:

* [Een site-naar-site-verbinding met een VNet toevoegen met een bestaande VPN-gateway verbinding](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Een site-naar-site-verbinding met een VNet toevoegen met een bestaande VPN-gateway verbinding (klassiek)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> Deze stappen zijn niet van toepassing op ExpressRoute-en site-to-site naast elkaar bestaande verbindings configuraties. Zie voor meer informatie over naast elkaar bestaande verbindingen [ExpressRoute/S2S-verbindingen](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

## <a name="scenario-4-configure-transit-routing"></a>Scenario 4: Transit routering configureren

Transitieve route ring is een specifiek routerings scenario waarin u meerdere netwerken in een BIND keten-topologie verbindt. Met deze route ring kunnen resources in Vnets aan beide uiteinden van de ' keten ' communiceren met elkaar via VNets. Zonder transitieve route ring kunnen netwerken of apparaten die via een hub worden gekoppeld, elkaar niet bereiken.

### <a name="configuration-1-configure-transit-routing-in-a-point-to-site-connection"></a>Configuratie 1: Transit routering configureren in een punt-naar-site-verbinding

In dit scenario configureert u een site-naar-site-VPN-verbinding tussen VNetA en VNetB, configureert u ook een Point-to-site VPN voor client om verbinding te maken met de gateway van VNetA. Vervolgens wilt u Transit routering inschakelen voor de punt-naar-site-clients om verbinding te maken met VNetB, die door VNetA wordt door gegeven. Dit scenario wordt ondersteund wanneer BGP is ingeschakeld op de site-VPN tussen VNetA en VNetB. Zie [about Point-to-site VPN-route ring](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)voor meer informatie.

### <a name="configuration-2-configure-transit-routing-in-an-expressroute-connection"></a>Configuratie 2: Transit routering configureren in een ExpressRoute-verbinding

Met Microsoft Azure ExpressRoute kunt u uw on-premises netwerken in de Microsoft Cloud uitbreiden via een speciale persoonlijke verbinding die wordt gefaciliteerd door een connectiviteitsprovider. Met ExpressRoute kunt u verbindingen tot stand brengen met Microsoft Cloud-services, zoals Microsoft Azure, Office 365 en Dynamics 365.  Zie [ExpressRoute Overview](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)(Engelstalig) voor meer informatie.

![BITMAPAFBEELDING](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

Afbeelding 6-ExpressRoute ' persoonlijke peering ' verbinden met Azure VNets

> [!Note]
> Als VNetA en VNetB zich in dezelfde geopolitieke regio bevinden, is het raadzaam [om beide VNets te koppelen aan het ExpressRoute-circuit](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) in plaats van transitieve route ring te configureren. Als uw VNets zich in verschillende geopolitieke regio's bevinden, kunt u ze ook rechtstreeks aan uw circuit koppelen als u [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium)hebt. 

Als u ExpressRoute en site-naar-site-samen werking hebt, wordt Transit routering niet ondersteund. Zie voor meer informatie [ExpressRoute en site-naar-site naast elkaar bestaande verbindingen configureren voor meer informatie](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

Als u ExpressRoute hebt ingeschakeld om uw lokale netwerken te verbinden met een virtueel Azure-netwerk, kunt u VNet-peering inschakelen tussen de VNets waarvoor transitieve route ring moet gelden. Als u uw lokale netwerken verbinding wilt laten maken met het externe VNet, moet u [peering voor het virtuele netwerk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity)configureren. 

> [!Note]
> VNet-peering is alleen beschikbaar voor VNets in dezelfde regio.

Volg de instructies om te controleren of u Transit route voor VNet-peering hebt geconfigureerd:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een account met de benodigde [rollen en machtigingen](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions).
2. [Maak een peering tussen het VNet a en B](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) zoals in het diagram eerder (afbeelding 6). 
3. In het deel venster dat wordt weer gegeven voor het virtuele netwerk dat u hebt geselecteerd, klikt u op **peerings** in het gedeelte **instellingen** .
4. Klik op de peering die u wilt weer geven en **configureren** om te controleren of u **Allow gateway-door Voer** hebt ingeschakeld op de VNetA die is verbonden met het ExpressRoute-circuit en **Gebruik externe gateway** op de externe VNetB die niet is verbonden met de ExpressRoute schakelaar.

### <a name="configuration-3-configure-transit-routing-in-a-vnet-peering-connection"></a>Configuratie 3: Transit routering configureren in een VNet-peering-verbinding

Wanneer virtuele netwerken gelijkwaardig zijn gemaakt, kunt u ook de gateway in het gelijkwaardige virtuele netwerk configureren als een doorvoerpunt naar een on-premises netwerk. Als u Transit route in VNet-peering wilt configureren, controleert u [virtuele netwerk-naar-virtuele netwerk verbindingen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json).

> [!Note]
> Gateway-door Voer wordt niet ondersteund in de peering-relatie tussen virtuele netwerken die via verschillende implementatie modellen zijn gemaakt. Beide virtuele netwerken in de peering-relatie moeten zijn gemaakt via Resource Manager voor de doorvoer van een werkende gateway-doorvoer.

Volg de instructies om te controleren of u Transit route voor VNet-peering hebt geconfigureerd:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een account met de benodigde [rollen en machtigingen](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions).
2. In het vak met de tekst zoeken resources boven aan de Azure Portal, typt u *virtuele netwerken*. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, klikt u erop.
3. Klik in de Blade **virtuele netwerken** die wordt weer gegeven op het virtuele netwerk waarvoor u de instelling voor peering wilt controleren.
4. In het deel venster dat wordt weer gegeven voor het virtuele netwerk dat u hebt geselecteerd, klikt u op **peerings** in het gedeelte **instellingen** .
5. Klik op de peering die u wilt weer geven en controleer of u de functie **gateway door Voer toestaan** hebt ingeschakeld en **externe gateway** onder **configuratie**wilt gebruiken.

![BITMAPAFBEELDING](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configuration-4-configure-transit-routing-in-a-vnet-to-vnet-connection"></a>Configuratie 4: Transit routering configureren in een VNet-naar-VNet-verbinding

Als u Transit routering tussen VNets wilt configureren, moet u BGP inschakelen voor alle tussenliggende VNet-naar-VNet-verbindingen met behulp van het Resource Manager-implementatie model en Power shell. Zie [BGP op Azure VPN-gateways configureren met behulp van Power shell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)voor instructies.

Transit verkeer via Azure VPN-gateway is mogelijk met behulp van het klassieke implementatie model, maar is afhankelijk van statisch gedefinieerde adres ruimten in het netwerk configuratie bestand. BGP wordt nog niet ondersteund met Azure Virtual Networks en VPN-gateways met behulp van het klassieke implementatie model. Zonder BGP, het hand matig definiëren van transit adres ruimten is fout gevoelig en wordt niet aanbevolen.

> [!Note]
> Klassieke VNet-naar-VNet-verbindingen worden geconfigureerd met behulp van de Azure Portal (klassiek) of door gebruik te maken van een netwerk configuratie bestand in de klassieke Portal. U kunt een klassiek virtueel netwerk niet maken of wijzigen via het Azure Resource Manager-implementatie model of Azure Portal. Zie [Hub & spoke, ringnet werk keten en volledige-mesh VNET-topologieën in azure arm met behulp van VPN (v1)](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)voor meer informatie over transit routering voor klassieke VNets.

### <a name="configuration-5-configure-transit-routing-in-a-site-to-site-connection"></a>Configuratie 5: Transit routering configureren in een site-naar-site-verbinding

Als u de transit routering tussen uw on-premises netwerk en een VNet met een site-naar-site-verbinding wilt configureren, moet u BGP inschakelen op alle tussenliggende site-naar-site-verbindingen met behulp van het Resource Manager-implementatie model en Power shell. Zie [How to configure BGP op Azure VPN-gateways met behulp van Power shell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) voor instructies.

Transit verkeer via Azure VPN-gateway is mogelijk met behulp van het klassieke implementatie model, maar is afhankelijk van statisch gedefinieerde adres ruimten in het netwerk configuratie bestand. BGP wordt nog niet ondersteund met Azure Virtual Networks en VPN-gateways met behulp van het klassieke implementatie model. Zonder BGP, het hand matig definiëren van transit adres ruimten is fout gevoelig en wordt niet aanbevolen.

> [!Note]
> Klassieke site-naar-site-verbindingen worden geconfigureerd met behulp van de Azure Portal (klassiek) of met behulp van een netwerk configuratie bestand in de klassieke Portal. U kunt een klassiek virtueel netwerk niet maken of wijzigen via het Azure Resource Manager-implementatie model of Azure Portal. Zie [Hub & spoke, ringnet werk keten en volledige-mesh VNET-topologieën in azure arm met behulp van VPN (v1)](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)voor meer informatie over transit routering voor klassieke VNets.

## <a name="scenario-5-configure-bgp-for-a-vpn-gateway"></a>Scenario 5: Een BGP configureren voor een VPN-gateway

BGP is het standaard routerings protocol dat op Internet wordt gebruikt om routerings-en bereik baarheids gegevens tussen twee of meer netwerken te uitwisselen. Wanneer BGP wordt gebruikt in de context van virtuele Azure-netwerken, maakt BGP de Azure VPN-gateways en uw on-premises VPN-apparaten, ook wel BGP-peers of neighbors. Ze wisselen ' routes ' in, waarbij beide gateways worden geïnformeerd over de beschik baarheid en het bereiken van deze voor voegsels om door de betrokken gateways of routers te gaan. Met BGP kan ook transitroutering tussen meerdere netwerken worden ingeschakeld door routes die een BGP-gateway leert van één BGP te propageren naar alle andere BGP-peers. Zie [overzicht van BGP met Azure VPN-gateways](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)voor meer informatie.

### <a name="configure-bgp-for-a-vpn-connection"></a>BGP configureren voor een VPN-verbinding

Zie [BGP op Azure VPN-gateways configureren met behulp van Power shell voor meer informatie over](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)het configureren van een VPN-verbinding die gebruikmaakt van BGP.

> [!Note]
> 1. Schakel BGP in op de Virtual Network-gateway door een AS-nummer te maken. Basis gateways bieden geen ondersteuning voor BGP. Als u de SKU van de gateway wilt controleren, gaat u naar de sectie Overzicht van de Blade VPN Gateway in het Azure Portal. Als uw SKU **Basic**is, moet u de SKU ([de grootte van de gateway](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)wijzigen) aanpassen naar de **VpnGw1** -SKU. Het controleren van de SKU resulteert in een downtime van 20-30 minuten. Zodra de gateway de juiste SKU heeft, kan de AS worden toegevoegd via [set-azurermvirtualnetworkgateway bijgewerkt](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) Power shell-opdracht toestaan. Nadat u het AS-nummer hebt geconfigureerd, wordt er automatisch een BGP-peer-IP voor de gateway gegeven.
> 2. De LocalNetworkGateway moet **hand matig** worden voorzien van een as-nummer en een BGP-peer adres. U kunt de **ASN** -en **-BgpPeeringAddress-** waarden instellen met behulp van de opdracht [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) of [set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) Power shell. Sommige AS-nummers zijn gereserveerd voor Azure en u kunt ze niet gebruiken zoals wordt beschreven in het gedeelte [over BGP met azure VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview#bgp-faq).
> 3. Ten slotte moet BGP zijn ingeschakeld voor het verbindings object. U kunt de waarde **-EnableBGP** instellen op `$True` via [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) of [set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0).

### <a name="validate-the-bgp-configuration"></a>De BGP-configuratie valideren

Als u wilt controleren of BGP juist is geconfigureerd, kunt u de `get-AzureRmVirtualNetworkGateway` cmdlet `get-AzureRmLocalNetworkGateway`uitvoeren en vervolgens de BGP-gerelateerde uitvoer weer gegeven in het BgpSettingsText-onderdeel. Bijvoorbeeld: BgpSettingsText:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="scenario-6-highly-available-active-active-vpn-connection"></a>Scenario 6: Maxi maal beschik bare actief-actief VPN-verbinding

De belangrijkste verschillen tussen de gateways actief en actief en stand-by:

* U moet twee IP-configuraties met gateways met twee open bare IP-adressen maken
* U moet de vlag *EnableActiveActiveFeature* instellen
* De gateway-SKU moet VpnGw1, VpnGw2, VpnGw3

Voor een hoge Beschik baarheid van cross-premises en VNet-naar-VNet-connectiviteit moet u meerdere VPN-gateways implementeren en meerdere parallelle verbindingen tot stand brengen tussen uw netwerken en Azure. Zie voor een overzicht van connectiviteits opties en-topologie [Maxi maal beschik bare cross-premises en vnet-naar-vnet-connectiviteit](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable).

Als u Active-Active-cross-premises en VNet-naar-VNet-verbindingen wilt maken, volgt u de instructies in [Configureer actieve en actieve S2S VPN-verbindingen met Azure VPN-gateways](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) voor het configureren van Azure VPN-gateway in de modus actief/actief.

> [!Note]  
> 1. Wanneer u adressen toevoegt aan de lokale netwerk gateway voor BGP ingeschakeld, worden in de modus actief naar actief *alleen de/32-adressen van de BGP-peers toegevoegd*. Als u meer adressen toevoegt, worden deze beschouwd als statische routes en hebben ze voor rang op BGP-routes.
> 2. U moet andere BGP-Asn's gebruiken voor uw on-premises netwerken die verbinding maken met Azure. (Als deze hetzelfde zijn, moet u uw VNet-ASN wijzigen als uw on-premises VPN-apparaat het ASN al gebruikt voor een peer met andere BGP-neighbors.)

## <a name="scenario-7-change-an-azure-vpn-gateway-type-after-deployment"></a>Scenario 7: Een Azure VPN-gateway type wijzigen na de implementatie

U kunt een Azure VNet-gateway type niet wijzigen van beleid op basis van route ring of op een andere manier rechtstreeks. U moet de gateway verwijderen nadat het IP-adres en de won'tbe van de vooraf gedeelde sleutel zijn behouden. Vervolgens kunt u een nieuwe gateway maken van het gewenste type. Volg de stappen om een gateway te verwijderen en te maken:

1. Verwijder alle verbindingen die zijn gekoppeld aan de oorspronkelijke gateway.
2. De gateway verwijderen met behulp van Azure Portal, Power shell of klassieke Power shell: 
   * [Een virtuele netwerk gateway verwijderen met behulp van de Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Een virtuele netwerk gateway verwijderen met Power shell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Een virtuele netwerk gateway verwijderen met behulp van Power shell (klassiek)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Volg de stappen in [de VPN-gateway maken](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#a-namevnetgatewaya4-create-the-vpn-gateway) om de nieuwe gateway van het gewenste type te maken en de VPN-installatie te volt ooien.

> [!Note]
> Dit proces duurt ongeveer 60 minuten.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met de connectiviteit tussen virtuele Azure-machines oplossen](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

