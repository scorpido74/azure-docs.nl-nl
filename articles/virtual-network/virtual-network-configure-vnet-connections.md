---
title: Virtuele netwerk- of VPN-verbindingen configureren en valideren
description: Stapsgewijze richtlijnen voor het configureren en valideren van verschillende Azure VPN- en virtuele netwerkimplementaties
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
ms.openlocfilehash: dddf402455292e19bf0fcda3c50d9ce10d5888d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099064"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Virtuele netwerk- of VPN-verbindingen configureren en valideren

Deze walkthrough biedt stapsgewijze richtlijnen voor het configureren en valideren van verschillende Azure VPN- en virtuele netwerkimplementaties. Scenario's zijn transitrouting, network-to-network-verbindingen, Border Gateway Protocol (BGP), multisiteverbindingen en point-to-site verbindingen.

Azure VPN-gateways maken flexibiliteit mogelijk bij het regelen van bijna elke vorm van verbonden virtuele netwerktopologie in Azure. U bijvoorbeeld virtuele netwerken verbinden:

- In verschillende regio's.
- Tussen virtuele netwerktypen (Azure Resource Manager versus classic).
- Binnen Azure of binnen een on-premises hybride omgeving.
- In verschillende abonnementen. 

## <a name="network-to-network-vpn-connection"></a>VPN-verbinding tussen netwerk en netwerk

Het verbinden van een virtueel netwerk met een ander virtueel netwerk (network-to-network) via VPN is vergelijkbaar met het verbinden van een virtueel netwerk met een on-premises locatie. Beide connectiviteitstypen maken gebruik van een VPN-gateway om een veilige tunnel te bieden via IPsec en IKE. De virtuele netwerken kunnen zich in dezelfde of verschillende regio's bevinden en tot dezelfde of verschillende abonnementen behoren.

![Netwerk-naar-netwerkverbinding met IPsec](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Als uw virtuele netwerken zich in dezelfde regio bevinden, u overwegen ze met elkaar te verbinden met behulp van virtuele netwerkpeering. Virtuele netwerkpeering maakt geen gebruik van een VPN-gateway. Het verhoogt de doorvoer en vermindert de latentie. Als u een verbinding voor virtuele netwerkpeering wilt configureren, selecteert u **VNet-peering configureren en valideren.**

Als uw virtuele netwerken zijn gemaakt via het Azure Resource Manger-implementatiemodel, selecteert u **Een VNet resourcebeheer configureren en valideren naar een VNet-verbinding voor ResourceBeheer** om een VPN-verbinding te configureren.

Als een van de virtuele netwerken is gemaakt via het klassieke Azure-implementatiemodel en de andere is gemaakt via Resource Beheer, selecteert u **Een klassieke VNet configureren en valideren naar een VNet-verbinding** voor Resource Beheer om een VPN-verbinding te configureren.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Virtueel netwerkpeeren configureren voor twee virtuele netwerken in dezelfde regio

Voordat u begint met het implementeren en configureren van Azure virtual network peering, moet u ervoor zorgen dat u aan de volgende vereisten voldoet:

* De virtuele netwerken die worden gekoppeld, moeten zich in dezelfde Azure-regio bevinden.
* De peered virtuele netwerken moeten IP-adresruimten hebben die elkaar niet overlappen.
* Peering in virtuele netwerken vindt plaats tussen twee virtuele netwerken. Er is geen afgeleide transitieve relatie tussen peerings. Als VNetA bijvoorbeeld is gekoppeld aan VNetB en VNetB is gekoppeld aan VNetC, wordt VNetA *niet* met VNetC gekeken.

Wanneer u aan de vereisten voldoet, u [Zelfstudie: Virtuele netwerken verbinden met virtueel netwerkpeeren volgen met behulp van de Azure-portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) om peering te maken en te configureren.

Als u de peeringconfiguratie wilt controleren, gebruikt u de volgende methode:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met behulp van een account met de benodigde [rollen en machtigingen.](virtual-network-manage-peering.md#permissions)
2. Typ **virtuele netwerken**in het vak met de tekst **Zoekbronnen** boven aan de portal. Wanneer **virtuele netwerken** worden weergegeven in de zoekresultaten, selecteert u deze.
3. Selecteer in het blad **Virtuele netwerken** dat wordt weergegeven het virtuele netwerk waarvoor u een peering wilt maken.
4. Selecteer **Peerings** in het deelvenster dat voor het virtuele netwerk wordt weergegeven in de sectie **Instellingen.**
5. Selecteer een peering en bekijk de configuratieresultaten.

![Selecties voor het controleren van de virtuele netwerkpeeringconfiguratie](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Voer voor Azure PowerShell de opdracht [Get-AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) uit om het virtuele netwerkpeering te krijgen. Hier volgt een voorbeeld:

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

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Een virtueel netwerk van Resource Manager verbinden met een ander virtueel netwerk van Resource Manager

U een verbinding rechtstreeks configureren van het ene virtuele netwerk van Resource Manager naar een ander virtueel netwerk van Resource Manager. U de verbinding ook configureren met Behulp van IPsec.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Een VPN-verbinding configureren tussen virtuele netwerken van Resource Manager

Zie [Een VPN-gatewayverbinding](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)tussen Netwerk naar netwerk configureren met behulp van de Azure-portal als u een verbinding wilt configureren tussen virtuele netwerken van Resource Manager zonder IPsec.

Als u een verbinding met IPsec wilt configureren tussen twee virtuele netwerken van Resource Manager, voert u de stappen 1 tot en met 5 uit in [Een site-to-site-verbinding maken in de Azure-portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) voor elk virtueel netwerk.

> [!Note]
> Deze stappen werken alleen voor virtuele netwerken in hetzelfde abonnement. Als uw virtuele netwerken zich in verschillende abonnementen bevinden, moet u PowerShell gebruiken om de verbinding te maken. Zie het artikel voor [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps).

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>De VPN-verbinding tussen virtuele netwerken van Resource Manager valideren

![Klassieke virtuele netwerkverbinding met een virtueel Azure Resource Manager-netwerk](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Volg deze instructies om te controleren of uw VPN-verbinding correct is geconfigureerd.

> [!Note] 
> De getallen na virtuele netwerkcomponenten in deze stappen komen overeen met de getallen in het voorgaande diagram.

1. Zorg ervoor dat er geen overlappende adresruimten zijn in de verbonden virtuele netwerken.
2. Controleer of het adresbereik voor het virtuele Azure Resource Manager-netwerk (1) nauwkeurig is gedefinieerd in de instantie **Verbindingsobject** (4).
3. Controleer of het adresbereik voor het virtuele Azure Resource Manager-netwerk (6) nauwkeurig is gedefinieerd in de instantie **Verbindingsobject** (3).
4. Controleer of de vooraf gedeelde sleutels overeenkomen met de verbindingsobjecten.
5. Controleer of de VIP (2) van Azure Resource Manager virtual network gateway nauwkeurig is gedefinieerd in de instantie **Van het doel Verbinding** (4).
6. Controleer of de VIP (5) van Azure Resource Manager virtual network gateway nauwkeurig is gedefinieerd in de instantie **Verbindingobject** (3).

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Een klassiek virtueel netwerk verbinden met een virtueel netwerk van Resource Manager

U een verbinding maken tussen virtuele netwerken die zich in verschillende abonnementen en in verschillende regio's bevinden. U ook virtuele netwerken met verbindingen met on-premises netwerken verbinden, zolang u het gatewaytype hebt geconfigureerd als routegebaseerd.

Zie Virtuele netwerken van verschillende implementatiemodellen verbinden met [behulp van de Azure-portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal)als u een verbinding wilt configureren tussen een klassiek virtueel netwerk en een virtueel netwerk van Resource Manager.

![Klassieke virtuele netwerkverbinding met een virtueel Azure Resource Manager-netwerk](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Volg deze instructies om de configuratie te controleren wanneer u een klassiek virtueel netwerk aansluit op een virtueel Azure Resource Manager-netwerk.

> [!Note] 
> De getallen na virtuele netwerkcomponenten in deze stappen komen overeen met de getallen in het voorgaande diagram. 

1. Zorg ervoor dat er geen overlappende adresruimten zijn in de verbonden virtuele netwerken.
2. Controleer of het adresbereik voor het virtuele Azure Resource Manager-netwerk (6) nauwkeurig is gedefinieerd in de klassieke lokale netwerkdefinitie (3).
3. Controleer of het adresbereik voor het klassieke virtuele netwerk (1) nauwkeurig is gedefinieerd in de instantie Azure Resource Manager **Connection** (4).
4. Controleer of de klassieke virtuele netwerkgateway VIP (2) nauwkeurig is gedefinieerd in de instantie Azure Resource Manager **Connection** (4).
5. Controleer of de virtuele netwerkgateway azure resource manager (5) nauwkeurig is gedefinieerd in de klassieke instantie **Local Network Definition** (3).
6. Controleer of de vooraf gedeelde sleutels overeenkomen op beide verbonden virtuele netwerken:
   - Klassiek virtueel netwerk: **Local Network Definition** (3)
   - Azure Resource Manager virtueel netwerk: **verbindingsobject** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>Een point-to-site VPN-verbinding maken

Met een point-to-site *(P2S* in het volgende diagram) u een beveiligde verbinding maken van een individuele clientcomputer naar een virtueel netwerk. Point-to-site-verbindingen zijn handig wanneer u verbinding wilt maken met uw virtuele netwerk vanaf een externe locatie, zoals thuis of een conferentie. Ze zijn ook handig als u slechts een paar clients hebt die verbinding moeten maken met een virtueel netwerk. 

De point-to-site VPN-verbinding wordt gestart vanaf de clientcomputer via de native Windows VPN-client. Clienten verbinden met certificaten om te verifiëren.

![Punt-naar-site-verbinding](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Point-to-site-verbindingen vereisen geen VPN-apparaat. Ze maken de VPN-verbinding via Secure Socket Tunneling Protocol (SSTP). U een point-to-site verbinding met een virtueel netwerk verbinden met behulp van verschillende implementatiehulpprogramma's en implementatiemodellen:

* [Een point-to-site-verbinding met een virtueel netwerk configureren met behulp van de Azure-portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Een point-to-site-verbinding met een virtueel netwerk configureren met behulp van de Azure-portal (klassiek)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Een point-to-site verbinding met een virtueel netwerk configureren met PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>Uw point-to-site-verbinding valideren

Het artikel [Probleemoplossing: azure point-to-site-verbindingsproblemen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) lopen door veelvoorkomende problemen met point-to-site-verbindingen.

## <a name="create-a-multisite-vpn-connection"></a>Een VPN-verbinding met meerdere plaatsen maken

U een site-to-site *(S2S* in het volgende diagram) toevoegen aan een virtueel netwerk dat al een site-to-site-verbinding, point-to-site-verbinding of netwerk-naar-netwerkverbinding heeft. Dit soort verbinding wordt vaak een *multisite-configuratie* genoemd. 

![Verbinding met meerdere plaatsen](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Azure werkt momenteel in combinatie met twee implementatiemodellen: Resource Manager en klassiek. De twee modellen zijn niet volledig compatibel met elkaar. Zie de volgende artikelen om een verbinding met meerdere locaties met verschillende modellen te configureren:

* [Een site-to-site-verbinding toevoegen aan een virtueel netwerk met een bestaande VPN-gatewayverbinding](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Een site-to-site-verbinding toevoegen aan een virtueel netwerk met een bestaande VPN-gatewayverbinding (klassiek)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> De stappen in die artikelen zijn niet van toepassing op Azure ExpressRoute en site-to-site coëxistentieconfiguraties. Zie [ExpressRoute en site-to-site naast elkaar bestaande verbindingen](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)voor meer informatie.

## <a name="configure-transit-routing"></a>Transitroutering configureren

Transitrouting is een specifiek routeringsscenario waarbij u meerdere netwerken in een daisy-chain topologie met elkaar verbindt. Deze routing stelt resources in virtuele netwerken aan weerszijden van de keten in staat om met elkaar te communiceren via virtuele netwerken tussendoor. Zonder transitroutering kunnen netwerken of apparaten die door een hub worden gekeken elkaar niet bereiken.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Transitroutering configureren in een point-to-site-verbinding

Stelt u zich een scenario voor waarin u een site-to-site VPN-verbinding tussen VNetA en VNetB wilt configureren. U wilt ook een point-to-site VPN configureren voor de client om verbinding te maken met de gateway van VNetA. Vervolgens wilt u transitroutering inschakelen voor de point-to-site clients om verbinding te maken met VNetB, dat via VNetA loopt. 

Dit scenario wordt ondersteund wanneer BGP is ingeschakeld op de site-to-site VPN tussen VNetA en VNetB. Zie [Informatie over point-to-site VPN-routering](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)voor meer informatie.

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Transitroutering configureren in een ExpressRoute-verbinding

Met Azure ExpressRoute kunt u uw on-premises netwerken in de Microsoft-cloud uitbreiden via een speciale persoonlijke verbinding die wordt gefaciliteerd door een connectiviteitsprovider. Met ExpressRoute kunt u verbindingen tot stand brengen met Microsoft Cloud-services, zoals Microsoft Azure, Office 365 en Dynamics 365. Raadpleeg [Overzicht van ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) voor meer informatie.

![ExpressRoute private peering-verbinding met virtuele Azure-netwerken](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> We raden u aan om als VNetA en VNetB zich in dezelfde geopolitieke regio bevinden, [beide virtuele netwerken te koppelen aan het ExpressRoute-circuit](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) in plaats van transitroutering te configureren. Als uw virtuele netwerken zich in verschillende geopolitieke regio's bevinden, u ze ook rechtstreeks aan uw circuit koppelen als u [ExpressRoute Premium hebt.](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) 

Als u ExpressRoute en site-to-site coëxistentie hebt, wordt transitroutering niet ondersteund. Zie [ExpressRoute en site-to-site configureren voor](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)meer informatie met PowerShell.

Als u ExpressRoute hebt ingeschakeld om uw lokale netwerken te verbinden met een virtueel Azure-netwerk, u peering inschakelen tussen de virtuele netwerken waar u transitroutering wilt hebben. Als u wilt dat uw lokale netwerken verbinding maken met het externe virtuele netwerk, moet u [virtuele netwerkpeering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity)configureren. 

> [!Note]
> Virtuele netwerkpeering is alleen beschikbaar voor virtuele netwerken in dezelfde regio.

Volg de volgende instructies om te controleren of u transitroutering hebt geconfigureerd voor virtueel netwerkpeeren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met behulp van een account met de benodigde [rollen en machtigingen.](virtual-network-manage-peering.md#permissions)
2. [Maak een peering tussen VNetA en VNetB](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) zoals weergegeven in het eerdere diagram. 
3. Selecteer **Peerings** in het deelvenster dat voor het virtuele netwerk wordt weergegeven in de sectie **Instellingen.**
4. Selecteer het peering dat u wilt weergeven. Selecteer vervolgens **Configuratie** om te valideren dat u **Gateway Transit toestaan** hebt ingeschakeld op het VNetA-netwerk dat is aangesloten op het ExpressRoute-circuit en Remote Gateway **gebruiken** op het externe VNetB-netwerk dat niet is aangesloten op het ExpressRoute-circuit.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Transitroutering configureren in een verbinding met virtuele netwerkpeering

Wanneer virtuele netwerken gelijkwaardig zijn gemaakt, kunt u ook de gateway in het gelijkwaardige virtuele netwerk configureren als een doorvoerpunt naar een on-premises netwerk. Zie [Netwerk-naar-netwerkverbindingen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json)voor het configureren van een doorvoerroute in virtueel netwerkpeeren.

> [!Note]
> Gateway transit wordt niet ondersteund in de peering relatie tussen virtuele netwerken gemaakt via verschillende implementatiemodellen. Beide virtuele netwerken in de peeringrelatie moeten zijn gemaakt via Resource Manager voor gateway transit naar werk.

Volg de volgende instructies om te controleren of u een transitroute hebt geconfigureerd voor virtueel netwerkpeeren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met behulp van een account met de benodigde [rollen en machtigingen.](virtual-network-manage-peering.md#permissions)
2. Typ **virtuele netwerken**in het vak met de tekst **Zoekbronnen** boven aan de portal. Wanneer **virtuele netwerken** worden weergegeven in de zoekresultaten, selecteert u deze.
3. Selecteer in het blad **Virtuele netwerken** dat wordt weergegeven het virtuele netwerk waarvoor u de peering-instelling wilt controleren.
4. Selecteer **Peerings** in het deelvenster dat wordt weergegeven voor het virtuele netwerk dat u hebt geselecteerd in de sectie **Instellingen.**
5. Selecteer de peering die u wilt weergeven. Controleer of u **Gateway transit toestaan** hebt ingeschakeld en **externe gateways gebruiken** onder **Configuratie**.

![Selecties om te controleren of u een transitroute hebt geconfigureerd voor virtueel netwerkpeeren](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Transitroutering configureren in een netwerk-naar-netwerkverbinding

Als u de transitroutering tussen virtuele netwerken wilt configureren, moet u BGP inschakelen op alle tussentijdse netwerk-naar-netwerkverbindingen met behulp van het implementatiemodel ResourceBeheer en PowerShell. Zie [BGP configureren op Azure VPN-gateways met PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)voor instructies.

Transitverkeer via Azure VPN-gateways is mogelijk via het klassieke implementatiemodel, maar dat is afhankelijk van statisch gedefinieerde adresruimten in het netwerkconfiguratiebestand. BGP wordt nog niet ondersteund met virtuele Azure-netwerken en VPN-gateways via het klassieke implementatiemodel. Zonder BGP is het handmatig definiëren van transitadresruimten foutgevoelig en we raden het niet aan.

> [!Note]
> U configureert klassieke netwerk-naar-netwerkverbindingen met behulp van de klassieke Azure-portal of met behulp van een netwerkconfiguratiebestand in de klassieke portal. U geen klassiek virtueel netwerk maken of wijzigen via het Azure Resource Manager-implementatiemodel of de Azure-portal. Zie de [Microsoft Developer-blog](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)voor meer informatie over transitroutering voor klassieke virtuele netwerken.

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Transitroutering configureren in een site-to-site-verbinding

Als u de transitroutering wilt configureren tussen uw on-premises netwerk en een virtueel netwerk met een site-to-site-verbinding, moet u BGP inschakelen op alle tussenliggende site-to-site-verbindingen met behulp van het implementatiemodel Resource Manager en PowerShell. Zie [Hoe u BGP op Azure VPN-gateways configureert met PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) voor instructies.

Transitverkeer via Azure VPN-gateways is mogelijk via het klassieke implementatiemodel, maar dat is afhankelijk van statisch gedefinieerde adresruimten in het netwerkconfiguratiebestand. BGP wordt nog niet ondersteund met virtuele Azure-netwerken en VPN-gateways via het klassieke implementatiemodel. Zonder BGP is het handmatig definiëren van transitadresruimten foutgevoelig en we raden het niet aan.

> [!Note]
> U configureert klassieke site-to-site-verbindingen met behulp van de klassieke Azure-portal of met behulp van een netwerkconfiguratiebestand in de klassieke portal. U geen klassiek virtueel netwerk maken of wijzigen via het Azure Resource Manager-implementatiemodel of de Azure-portal. Zie de [Microsoft Developer-blog](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)voor meer informatie over transitroutering voor klassieke virtuele netwerken.

## <a name="configure-bgp-for-a-vpn-gateway"></a>Een BGP configureren voor een VPN-gateway

BGP is het standaard routeringsprotocol dat op het internet wordt gebruikt om routerings- en bereikbaarheidsinformatie uit te wisselen tussen twee of meer netwerken. Wanneer BGP wordt gebruikt in de context van virtuele Azure-netwerken, worden de Azure VPN-gateways en uw on-premises VPN-apparaten, bekend als BGP-peers of buren, ingeschakeld. Ze wisselen "routes" uit die beide gateways informeren over de beschikbaarheid en bereikbaarheid voor die voorvoegsels om door de betrokken gateways of routers te gaan. 

BGP kan ook transitroutering tussen meerdere netwerken mogelijk maken door routes te verspreiden die een BGP-gateway leert van één BGP-peer ten opzichte van alle andere BGP-peers. Zie [Overzicht van BGP met Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)voor meer informatie.

### <a name="configure-bgp-for-a-vpn-connection"></a>BGP configureren voor een VPN-verbinding

Zie [BGP configureren op Azure VPN-gateways met PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)als u een VPN-verbinding wilt configureren die BGP gebruikt.

Schakel BGP in op de virtuele netwerkgateway door er een as-nummer voor autonoom systeem (AS) voor te maken. Basisgateways ondersteunen geen BGP. Als u de SKU van de gateway wilt controleren, gaat u naar het gedeelte **Overzicht** van het **VPN-gatewayblad** in de Azure-portal. Als uw SKU **Basic**is, moet u de SKU (zie [Het formaat van de gateway wijzigen)](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)naar **VpnGw1**. 

Het controleren van de SKU zal leiden tot 20 tot 30 minuten downtime. Zodra de gateway de juiste SKU heeft, u het AS-nummer toevoegen met behulp van de [set-AzureRmVirtualNetworkNetworkPowerShell-opdracht.](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) Nadat u het AS-nummer hebt geconfigureerd, wordt automatisch een IP-peer van Een BGP-peer voor de gateway verstrekt.

U moet handmatig `LocalNetworkGateway` een AS-nummer en een BGP-peeradres opgeven. U `ASN` de `-BgpPeeringAddress` waarden en waarden instellen met de opdracht [Nieuw-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) of de PowerShell-commandlet [Set-AzureRmLocalNetworkNetwork.](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) Sommige AS-nummers zijn gereserveerd voor Azure en u ze niet gebruiken zoals beschreven in [Over BGP met Azure VPN Gateway.](../vpn-gateway/vpn-gateway-bgp-overview.md#faq)

Het verbindingsobject moet BGP hebben ingeschakeld. U `-EnableBGP` de `$True` waarde instellen via [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) of [Set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0).

### <a name="validate-the-bgp-configuration"></a>De BGP-configuratie valideren

Als u wilt controleren of BGP correct `get-AzureRmVirtualNetworkGateway` is `get-AzureRmLocalNetworkGateway` geconfigureerd, u de opdrachten en opdrachten uitvoeren. Dan zult u merken BGP-gerelateerde `BgpSettingsText` output in het onderdeel. Bijvoorbeeld:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Een zeer beschikbare actieve/actieve VPN-verbinding maken

De belangrijkste verschillen tussen de actieve/actieve en actieve/stand-by gateways zijn:

* U moet twee IP-gatewayconfiguraties maken met twee openbare IP-adressen.
* U moet de vlag **Van EnableActiveActiveFeature** instellen.
* De gateway SKU moet **VpnGw1,** **VpnGw2**of **VpnGw3**zijn.

Om een hoge beschikbaarheid voor cross-premises en network-to-network-connectiviteit te bereiken, moet u meerdere VPN-gateways implementeren en meerdere parallelle verbindingen tot stand brengen tussen uw netwerken en Azure. Zie [Zeer beschikbare cross-premises en network-to-network-connectiviteit](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)voor een overzicht van connectiviteitsopties en topologie.

Als u actieve/actieve cross-premises en netwerk-naar-netwerkverbindingen wilt maken, volgt u de instructies in [Actieve/actieve S2S VPN-verbindingen configureren met Azure VPN-gateways](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) om een Azure VPN-gateway in actieve/actieve modus te configureren.

> [!Note]  
> * Wanneer u adressen toevoegt aan de lokale netwerkgateway voor de actieve/actieve modus die bgp is ingeschakeld, *voegt u alleen de /32-adressen van de BGP-peers toe.* Als u meer adressen toevoegt, worden deze beschouwd als statische routes en hebben ze voorrang op BGP-routes.
> * U moet verschillende BGP AS-nummers gebruiken voor uw on-premises netwerken die verbinding maken met Azure. (Als ze hetzelfde zijn, moet u uw virtuele netwerk AS-nummer wijzigen als uw on-premises VPN-apparaat het ASN al gebruikt om te peeren met andere BGP-buren.)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Een Azure VPN-gatewaytype wijzigen na implementatie

U een type Azure-virtuele netwerkgateway niet rechtstreeks wijzigen van beleid gebaseerd naar routegebaseerd of op de andere manier. U moet eerst de gateway verwijderen. Daarna worden het IP-adres en de vooraf gedeelde sleutel niet meer bewaard. Vervolgens u een nieuwe gateway van het gewenste type maken. 

Voer de volgende stappen uit om een gateway te verwijderen en te maken:

1. Verwijder alle verbindingen die zijn gekoppeld aan de oorspronkelijke gateway.
2. Verwijder de gateway met de Azure-portal, PowerShell of klassieke PowerShell: 
   * [Een virtuele netwerkgateway verwijderen met behulp van de Azure-portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Een virtuele netwerkgateway verwijderen met PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Een virtuele netwerkgateway verwijderen met PowerShell (klassiek)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Volg de stappen in [De VPN-gateway maken](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway) om de nieuwe gateway van het gewenste type te maken en de VPN-installatie te voltooien.

> [!Note]
> Dit proces duurt ongeveer 60 minuten.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met de connectiviteit tussen virtuele Azure-machines oplossen](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

