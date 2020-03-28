---
title: Zelfstudie - Een gateway maken en beheren met Azure VPN-gateway
description: 'Zelfstudie: Een Azure VPN-gateway maken en beheren met behulp van Microsoft Azure PowerShell'
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: cherylmc
ms.openlocfilehash: 66efa0f2922e70908616c7c447d782efee8f6b1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79137174"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>Zelfstudie: Een VPN-gateway maken en beheren met PowerShell

Azure VPN-gateways bieden veilige, cross-premises connectiviteit tussen de klanten-premises en Azure. Deze zelfstudie bevat informatie over implementatie-basiselementen voor Azure VPN-gateways, zoals het maken en beheren van een VPN-gateway. Procedures voor:

> [!div class="checklist"]
> * Een VPN-gateway maken
> * Het openbare IP-adres weergeven
> * Het formaat van een VPN-gateway wijzigen
> * Een VPN-gateway opnieuw instellen

Het volgende diagram toont het virtuele netwerk en de VPN-gateway die zijn gemaakt als onderdeel van deze zelfstudie.

![VNet en VPN-gateway](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="working-with-azure-cloud-shell-and-azure-powershell"></a>Werken met Azure Cloud Shell en Azure PowerShell

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>Algemene parameterwaarden van het netwerk

Hieronder vindt u de parameterwaarden die voor deze zelfstudie worden gebruikt. In de voorbeelden vertalen de variabelen zich naar het volgende:

```
#$RG1         = The name of the resource group
#$VNet1       = The name of the virtual network
#$Location1   = The location region
#$FESubnet1   = The name of the first subnet
#$BESubnet1   = The name of the second subnet
#$VNet1Prefix = The address range for the virtual network
#$FEPrefix1   = Addresses for the first subnet
#$BEPrefix1   = Addresses for the second subnet
#$GwPrefix1   = Addresses for the GatewaySubnet
#$VNet1ASN    = ASN for the virtual network
#$DNS1        = The IP address of the DNS server you want to use for name resolution
#$Gw1         = The name of the virtual network gateway
#$GwIP1       = The public IP address for the virtual network gateway
#$GwIPConf1   = The name of the IP configuration
```

Wijzig de onderstaande waarden op basis van uw omgeving en netwerkinstellingen en kopieer en plak deze waarden vervolgens om de variabelen voor deze zelfstudie in te stellen. Als er een time-out optreedt in uw Cloud Shell-sessie, of als u een ander PowerShell-venster moet gebruiken, kopieert en plakt u de variabelen naar uw nieuwe sessie en vervolgt u de zelfstudie.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Eerst moet u een resourcegroep maken. In het volgende voorbeeld wordt een resourcegroep met de naam *TestRG1* gemaakt in de regio *VS - oost*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Azure VPN-gateway biedt cross-premises connectiviteit en P2S-VPN-server-functionaliteit voor uw virtuele netwerk. Voeg de VPN-gateway toe aan een bestaand virtueel netwerk of maak een nieuw virtueel netwerk en de gateway. Merk op dat het voorbeeld specifiek de naam van het gateway-subnet opgeeft. U moet altijd de naam van het gatewaysubnet opgeven als "GatewaySubnet" om het goed te laten functioneren. In dit voorbeeld wordt een nieuw virtueel netwerk gemaakt met drie subnetten: Frontend, Backend en GatewaySubnet met [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) en [New-AzVirtualNetwork:](/powershell/module/az.network/new-azvirtualnetwork)

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -AddressPrefix $GwPrefix1
$vnet   = New-AzVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Een openbaar IP-adres voor de VPN-gateway aanvragen

Azure VPN-gateways communiceren met uw on-premises VPN-apparaten via Internet om IKE (Internet Key Exchange)-onderhandeling uit te voeren en IPsec-tunnels tot stand te brengen. Maak een openbaar IP-adres en wijs dit toe aan uw VPN-gateway, zoals wordt weergegeven in het onderstaande voorbeeld met [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) en [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig):

> [!IMPORTANT]
> Op dit moment kunt u alleen een dynamisch openbaar IP-adres gebruiken voor de gateway. Statische IP-adressen worden niet ondersteund op Azure VPN-gateways.

```azurepowershell-interactive
$gwpip    = New-AzPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-a-vpn-gateway"></a>Een VPN-gateway maken

Het maken van een VPN-gateway kan tot 45 minuten of langer duren. Nadat het maken van de gateway is voltooid, kunt u een verbinding tussen uw virtuele netwerk en een ander VNet maken. Of maak een verbinding tussen uw virtuele netwerk en een on-premises locatie. Maak een VPN-gateway met de cmdlet [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway).

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Sleutelparameterwaarden:
* GatewayType: Gebruik **Vpn** voor site-naar-site- en VNet-naar-VNet-verbindingen
* VpnType: Gebruik **RouteBased** om te communiceren met meer verschillende VPN-apparaten en meer routeringsfuncties
* GatewaySku: **VpnGw1** is de standaard; wijzig het naar een andere VpnGw SKU als u hogere doorvoerof meer verbindingen nodig hebt. Zie [Gateway-SKU's](vpn-gateway-about-vpn-gateway-settings.md#gwsku) voor meer informatie.

Als u de TryIt gebruikt, kan uw sessie een time-out krijgen. Is goed zo. De gateway wordt alsnog gemaakt.

Nadat het maken van de gateway is voltooid, kunt u een verbinding maken tussen uw virtuele netwerk en een ander VNet of een verbinding maken tussen uw virtuele netwerk en een on-premises locatie. U kunt ook een P2S-verbinding configureren voor uw VNet vanaf een clientcomputer.

## <a name="view-the-gateway-public-ip-address"></a>Het openbare IP-adres van de gateway weergeven

Als u de naam van het openbare IP-adres weet, gebruikt u [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) om het openbare IP-adres weer te geven dat is toegewezen aan de gateway.

Als er een time-out optreedt voor uw sessie, kopieert u de netwerkparameters uit het begin van deze zelfstudie naar de nieuwe sessie en gaat u verder.

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>Het formaat van een gateway wijzigen

U kunt de VPN-gateway-SKU wijzigen nadat de gateway is gemaakt. Verschillende gateway SKU's ondersteunen verschillende specificaties, zoals doorvoer, aantal verbindingen, enz. In het volgende voorbeeld wordt [het formaat van AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) gebruikt om het formaat van uw gateway te wijzigen van VpnGw1 naar VpnGw2. Zie [Gateway-SKU's](vpn-gateway-about-vpn-gateway-settings.md#gwsku) voor meer informatie.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Vergroten of verkleinen van een VPN-gateway duurt ook ongeveer 30 tot 45 minuten, hoewel deze bewerking bestaande verbindingen en configuraties **niet** onderbreekt of verwijdert.

## <a name="reset-a-gateway"></a>Een gateway opnieuw instellen

Als onderdeel van de stappen voor probleemoplossing, kunt u uw Azure VPN-gateway opnieuw instellen om de VPN-gateway te dwingen de IPsec-/IKE-tunnelconfiguraties opnieuw op te starten. Gebruik [Reset-AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway) als u een gateway opnieuw wilt instellen.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Zie [Reset a VPN gateway](vpn-gateway-resetgw-classic.md) (Een VPN-gateway opnieuw instellen) voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u doorgaat naar de [volgende zelfstudie,](vpn-gateway-tutorial-vpnconnection-powershell.md)wilt u deze resources behouden omdat ze de vereisten zijn.

Als de gateway echter deel uitmaakt van een prototype, test of 'proof of concept'-implementatie, kunt u de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de VPN-gateway en alle bijbehorende resources te verwijderen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u informatie gehad over basistaken voor het maken en beheren van een virtuele machine, zoals:

> [!div class="checklist"]
> * Een VPN-gateway maken
> * Het openbare IP-adres weergeven
> * Het formaat van een VPN-gateway wijzigen
> * Een VPN-gateway opnieuw instellen

Ga naar de volgende zelfstudies voor meer informatie over S2S-, VNet-naar-VNet- en P2S-verbindingen.

> [!div class="nextstepaction"]
> * [S2S-verbindingen maken](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [VNet-verbindingen maken](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [P2S-verbindingen maken](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
