---
title: 'Azure VPN-gateway: configuratie-instellingen'
description: Meer informatie over VPN-gateway-instellingen voor virtuele Azure-netwerkgateways.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: d7a2040748d170b4e536df59947ea811f149d931
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244860"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Informatie over VPN-gateway-configuratie-instellingen

Een VPN-gateway is een type virtuele netwerkgateway dat versleuteld verkeer verzendt tussen uw virtuele netwerk en uw on-premises locatie via een openbare verbinding. U ook een VPN-gateway gebruiken om verkeer tussen virtuele netwerken via de Azure-backbone te verzenden.

Een VPN-gatewayverbinding is afhankelijk van de configuratie van meerdere bronnen, die elk configureerbare instellingen bevatten. De secties in dit artikel bespreken de bronnen en instellingen die betrekking hebben op een VPN-gateway voor een virtueel netwerk dat is gemaakt in het implementatiemodel van Resource Manager. In het artikel [Over VPN Gateway](vpn-gateway-about-vpngateways.md) vindt u beschrijvingen en topologiediagrammen voor elke verbindingsoplossing.

De waarden in dit artikel passen VPN-gateways toe (virtuele netwerkgateways die de -GatewayType Vpn) gebruiken. Dit artikel heeft geen betrekking op alle gatewaytypen of zoneredundante gateways.

* Zie [Virtual Network Gateways for ExpressRoute voor](../expressroute/expressroute-about-virtual-network-gateways.md)waarden die van toepassing zijn op -GatewayType 'ExpressRoute'.

* Zie [Over zoneredundante gateways](about-zone-redundant-vnet-gateways.md)voor zoneredundante gateways .

* Zie [Over virtueel WAN](../virtual-wan/virtual-wan-about.md)voor Virtueel WAN .



## <a name="gateway-types"></a><a name="gwtype"></a>Gatewaytypen

Elk virtueel netwerk kan slechts één virtuele netwerkgateway van elk type hebben. Wanneer u een virtuele netwerkgateway maakt, moet u ervoor zorgen dat het gatewaytype correct is voor uw configuratie.

De beschikbare waarden voor -GatewayType zijn:

* VPN
* ExpressRoute

Een VPN-gateway `-GatewayType` vereist de *Vpn.*

Voorbeeld:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gateway-skus"></a><a name="gwsku"></a>Gateway-SKU's

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Een gateway-SKU configureren

#### <a name="azure-portal"></a>Azure Portal

Als u de Azure-portal gebruikt om een virtuele netwerkgateway voor ResourceBeheer te maken, u de gateway-SKU selecteren met behulp van de vervolgkeuzelijst. De opties die u wordt gepresenteerd, komen overeen met het gatewaytype en het VPN-type dat u selecteert.

#### <a name="powershell"></a>PowerShell

In het volgende PowerShell-voorbeeld wordt de `-GatewaySku` vpnGw1 opgegeven. Wanneer u PowerShell gebruikt om een gateway te maken, moet u eerst de IP-configuratie maken en vervolgens een variabele gebruiken om ernaar te verwijzen. In dit voorbeeld wordt de configuratievariabele $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>Azure-CLI

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizing-or-changing-a-sku"></a><a name="resizechange"></a>Het formaat van een SKU wijzigen of wijzigen

Als u een VPN-gateway hebt en u een andere Gateway SKU wilt gebruiken, zijn uw opties om het formaat van uw gateway-SKU te wijzigen of om over te schakelen naar een andere SKU. Wanneer u overstapt op een andere gateway-SKU, verwijdert u de bestaande gateway volledig en bouwt u een nieuwe gateway. Een gateway kan tot 45 minuten duren om te bouwen. Ter vergelijking: wanneer u het formaat van een gateway-SKU wijzigt, is er niet veel downtime omdat u de gateway niet hoeft te verwijderen en opnieuw op te bouwen. Als u de optie hebt om het formaat van uw gateway-SKU te wijzigen, in plaats van deze te wijzigen, wilt u dat doen. Er zijn echter regels met betrekking tot het wijzigen van het formaat:

1. Met uitzondering van de Basic SKU u het formaat van een VPN-gateway SKU wijzigen naar een andere VPN-gateway SKU binnen dezelfde generatie (Generation1 of Generation2). VpnGw1 van Generation1 kan bijvoorbeeld worden aangepast aan VpnGw2 van Generation1, maar niet naar VpnGw2 van Generation2.
2. Als u met de oude gateway-SKU's werkt, kunt u wisselen tussen Basic-, Standard- en HighPerformance-SKU's.
3. U **kunt het** formaat niet wijzigen van Basic/Standard/HighPerformance SKU's naar VpnGw SKU's. U moet in plaats [daarvan, overschakelen](#change) naar de nieuwe SKU's.

#### <a name="to-resize-a-gateway"></a><a name="resizegwsku"></a>Het formaat van een gateway wijzigen

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="to-change-from-an-old-legacy-sku-to-a-new-sku"></a><a name="change"></a>Om over te schakelen van een oude (legacy) SKU naar een nieuwe SKU

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connection-types"></a><a name="connectiontype"></a>Verbindingstypen

In het implementatiemodel Resource Manager vereist elke configuratie een specifiek type verbinding met de virtuele netwerkgateway. De beschikbare Resource Manager PowerShell-waarden voor `-ConnectionType` zijn:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

In het volgende PowerShell-voorbeeld maken we een S2S-verbinding waarvoor het verbindingstype *IPsec*vereist is.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpn-types"></a><a name="vpntype"></a>VPN-typen

Wanneer u de virtuele netwerkgateway voor een VPN-gatewayconfiguratie maakt, moet u een VPN-type opgeven. Het VPN-type dat u kiest, is afhankelijk van de verbindingstopologie die u wilt maken. Een P2S-verbinding vereist bijvoorbeeld een RouteBased VPN-type. Een VPN-type kan ook afhangen van de hardware die u gebruikt. S2S-configuraties vereisen een VPN-apparaat. Sommige VPN-apparaten ondersteunen alleen een bepaald VPN-type.

Het VPN-type dat u selecteert, moet voldoen aan alle verbindingsvereisten voor de oplossing die u wilt maken. Als u bijvoorbeeld een S2S VPN-gatewayverbinding en een P2S VPN-gatewayverbinding voor hetzelfde virtuele netwerk wilt maken, gebruikt u VPN-type *RouteBased* omdat P2S een RouteBased VPN-type vereist. U moet ook controleren of uw VPN-apparaat een RouteBased VPN-verbinding ondersteunde. 

Zodra een virtuele netwerkgateway is gemaakt, u het VPN-type niet meer wijzigen. U moet de virtuele netwerkgateway verwijderen en een nieuwe gateway maken. Er zijn twee VPN-typen:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

In het volgende PowerShell-voorbeeld wordt het `-VpnType` voorbeeld *routegebaseerd opgegeven*. Wanneer u een gateway maakt, moet u het juiste VPN-type voor uw configuratie kiezen.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="gateway-requirements"></a><a name="requirements"></a>Gatewayvereisten

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gateway-subnet"></a><a name="gwsub"></a>Gatewaysubnet

Voordat u een VPN-gateway maakt, moet u een gateway-subnet maken. Het gatewaysubnet bevat de IP-adressen die de virtuele netwerkgateway VM's en -services gebruiken. Wanneer u uw virtuele netwerkgateway maakt, worden gateway-VM's geïmplementeerd op het gatewaysubnet en geconfigureerd met de vereiste VPN-gateway-instellingen. Implementeer nooit iets anders (bijvoorbeeld extra VM's) naar het gatewaysubnet. Het gatewaysubnet moet 'GatewaySubnet' heten om goed te kunnen werken. Als u het gateway-subnet 'GatewaySubnet' noemt, laat Azure weten dat dit het subnet is om de virtuele netwerkgateway VM's en -services te implementeren.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Wanneer u het gatewaysubnet maakt, geeft u op hoeveel IP-adressen het subnet bevat. De IP-adressen in het gatewaysubnet worden toegewezen aan de gateway-VM's en gatewayservices. Sommige configuraties vereisen meer IP-adressen dan andere. 

Wanneer u uw subnetgrootte van de gateway plant, raadpleegt u de documentatie voor de configuratie die u wilt maken. De ExpressRoute/VPN Gateway-coëxistentieconfiguratie vereist bijvoorbeeld een groter gatewaysubnet dan de meeste andere configuraties. Bovendien u ervoor zorgen dat uw gatewaysubnet voldoende IP-adressen bevat om eventuele toekomstige aanvullende configuraties mogelijk te kunnen aanpassen. Hoewel u een gateway-subnet zo klein als /29 maken, raden we u aan een gateway-subnet van /27 of groter (/27, /26 enz.) te maken als u de beschikbare adresruimte hebt om dit te doen. Dit is geschikt voor de meeste configuraties.

In het volgende voorbeeld van Resource Manager PowerShell wordt een gateway-subnet met de naam GatewaySubnet weergegeven. U de CIDR-notatie een /27 zien, waardoor er voldoende IP-adressen zijn voor de meeste configuraties die momenteel bestaan.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="local-network-gateways"></a><a name="lng"></a>Lokale netwerkgateways

 Een lokale netwerkgateway is anders dan een virtuele netwerkgateway. Bij het maken van een VPN-gatewayconfiguratie vertegenwoordigt de lokale netwerkgateway meestal uw on-premises locatie. In het klassieke implementatiemodel werd de lokale gateway een lokale site genoemd.

U geeft de lokale netwerkgateway een naam, het openbare IP-adres van het on-premises VPN-apparaat en geeft de adresvoorvoegsels op die zich op de on-premises locatie bevinden. Azure bekijkt de voorvoegsels voor het doeladres voor netwerkverkeer, raadpleegt de configuratie die u hebt opgegeven voor uw lokale netwerkgateway en stuurt pakketten dienovereenkomstig. U geeft ook lokale netwerkgateways op voor VNet-to-VNet-configuraties die een VPN-gatewayverbinding gebruiken.

In het volgende PowerShell-voorbeeld wordt een nieuwe lokale netwerkgateway gemaakt:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Soms moet u de lokale netwerkgateway-instellingen wijzigen. Bijvoorbeeld wanneer u het adresbereik toevoegt of wijzigt, of als het IP-adres van het VPN-apparaat verandert. Zie [Lokale netwerkgateway-instellingen wijzigen met PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="rest-apis-powershell-cmdlets-and-cli"></a><a name="resources"></a>REST API's, PowerShell-cmdlets en CLI

Zie de volgende pagina's voor aanvullende technische bronnen en specifieke syntaxisvereisten bij het gebruik van REST API's, PowerShell-cmdlets of Azure CLI voor VPN-gatewayconfiguraties:

| **Klassiek** | **Resource Manager** |
| --- | --- |
| [Powershell](/powershell/module/az.network/#networking) |[Powershell](/powershell/module/az.network#vpn) |
| [REST-API](https://msdn.microsoft.com/library/jj154113) |[REST-API](/rest/api/network/virtualnetworkgateways) |
| Niet ondersteund | [Azure-CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Volgende stappen

Zie [Over VPN Gateway](vpn-gateway-about-vpngateways.md)voor meer informatie over beschikbare verbindingsconfiguraties.
