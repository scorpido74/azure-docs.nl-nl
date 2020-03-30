---
title: Een zoneredundante virtuele netwerkgateway maken in Azure Availability Zones
description: VPN-gateway- en ExpressRoute-gateways implementeren in beschikbaarheidszones
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: d8c6b68a38d4b60cf7a3194e6a5ded8804cc416f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77150167"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Een zoneredundante virtuele netwerkgateway maken in Azure Availability Zones

U VPN- en ExpressRoute-gateways implementeren in Azure Availability Zones. Dit zorgt in een virtuele netwerkgateway voor tolerantie, schaalbaarheid en hoge beschikbaarheid. Gateways fysiek en logisch implementeren in Azure-beschikbaarheidszones scheidt gateways binnen een regio, terwijl uw on-premises netwerkconnectiviteit met Azure wordt beschermd tegen fouten op zoneniveau. Zie Informatie [over redundante virtuele netwerkgateways](about-zone-redundant-vnet-gateways.md) en [over azure-beschikbaarheidszones](../availability-zones/az-overview.md)voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-declare-your-variables"></a><a name="variables"></a>1.

Declareer de waarden die u wilt gebruiken. Gebruik het volgende voorbeeld, en vervang zo nodig de waarden door uw eigen waarden. Als u uw PowerShell/Cloud Shell-sessie op enig moment tijdens de oefening sluit, kopieert en plakt u de waarden opnieuw om de variabelen opnieuw te declareren. Controleer bij het opgeven van de locatie of het door u opgegeven gebied wordt ondersteund. Zie voor meer informatie de [FAQ](#faq).

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="2-create-the-virtual-network"></a><a name="configure"></a>2. Maak het virtuele netwerk

Maak een resourcegroep.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Maak een virtueel netwerk.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="3-add-the-gateway-subnet"></a><a name="gwsub"></a>3. Het subnet van de gateway toevoegen

Het gatewaysubnet bevat de gereserveerde IP-adressen die de virtuele netwerkgatewayservices gebruiken. Gebruik de volgende voorbeelden om een gateway-subnet toe te voegen en in te stellen:

Voeg het gatewaysubnet toe.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Stel de subnetconfiguratie gateway voor het virtuele netwerk in.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="4-request-a-public-ip-address"></a><a name="publicip"></a>4. Vraag een openbaar IP-adres aan
 
Kies in deze stap de instructies die van toepassing zijn op de gateway die u wilt maken. De selectie van zones voor het implementeren van de gateways is afhankelijk van de zones die zijn opgegeven voor het openbare IP-adres.

### <a name="for-zone-redundant-gateways"></a><a name="ipzoneredundant"></a>Voor zoneredundante gateways

Vraag een openbaar IP-adres aan met een **Standaard** PublicIpaddress SKU en geef geen zone op. In dit geval is het standaard openbare IP-adres dat is gemaakt een zoneredundant openbaar IP-adres.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="for-zonal-gateways"></a><a name="ipzonalgw"></a>Voor zonale gateways

Vraag een openbaar IP-adres aan met een **Standaard** PublicIpaddress SKU. Geef de zone op (1, 2 of 3). Alle gateway-exemplaren worden in deze zone geïmplementeerd.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="for-regional-gateways"></a><a name="ipregionalgw"></a>Voor regionale gateways

Vraag een openbaar IP-adres aan met een **Basic** PublicIpaddress SKU. In dit geval wordt de gateway geïmplementeerd als een regionale gateway en is er geen zoneredundantie ingebouwd in de gateway. De gateway-exemplaren worden gemaakt in alle zones, respectievelijk.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="5-create-the-ip-configuration"></a><a name="gwipconfig"></a>5. De IP-configuratie maken

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="6-create-the-gateway"></a><a name="gwconfig"></a>6. De gateway maken

Maak de virtuele netwerkgateway.

### <a name="for-expressroute"></a>Voor ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>Voor VPN-gateway

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a><a name="faq"></a>Veelgestelde vragen

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Wat verandert er als ik deze nieuwe SKU's implementeer?

Vanuit uw perspectief u uw gateways implementeren met zoneredundantie. Dit betekent dat alle exemplaren van de gateways worden geïmplementeerd in Azure Availability Zones en dat elke beschikbaarheidszone een ander fout- en updatedomein is. Dit maakt uw gateways betrouwbaarder, beschikbaar en bestandtegen zonefouten.

### <a name="can-i-use-the-azure-portal"></a>Kan ik de Azure-portal gebruiken?

Ja, u de Azure-portal gebruiken om de nieuwe SKU's te implementeren. U ziet deze nieuwe SKU's echter alleen in azure-regio's met Azure-beschikbaarheidszones.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Welke regio's zijn beschikbaar voor mij om de nieuwe SKU's te gebruiken?

Zie [Beschikbaarheidszones](../availability-zones/az-overview.md#services-support-by-region) voor de meest recente lijst met beschikbare regio's.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Kan ik mijn bestaande virtuele netwerkgateways wijzigen/migreren/upgraden naar zoneredundante of zonale gateways?

Het migreren van uw bestaande virtuele netwerkgateways naar zoneredundante of zonale gateways wordt momenteel niet ondersteund. U echter uw bestaande gateway verwijderen en een zoneredundante of zonale gateway opnieuw maken.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Kan ik zowel VPN- als Express Route-gateways implementeren in hetzelfde virtuele netwerk?

Coëxistentie van zowel VPN- als Express Route-gateways in hetzelfde virtuele netwerk wordt ondersteund. U moet echter een IP-adresbereik van / 27 reserveren voor het gateway-subnet.
