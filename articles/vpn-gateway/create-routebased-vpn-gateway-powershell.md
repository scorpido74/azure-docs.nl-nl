---
title: 'Een op een route gebaseerde Azure VPN-gateway maken: Power shell | Microsoft Docs'
description: Snel een op route gebaseerd VPN Gateway maken met behulp van Power shell
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/11/2019
ms.author: cherylmc
ms.openlocfilehash: 2a1c34ea25c8ef4bdc3e75e18d6f4a603efc67d5
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953831"
---
# <a name="create-a-route-based-vpn-gateway-using-powershell"></a>Een op een route gebaseerde VPN-gateway maken met behulp van Power shell

Dit artikel helpt u bij het snel maken van een op route gebaseerde Azure VPN-gateway met behulp van Power shell. Een VPN-gateway wordt gebruikt bij het maken van een VPN-verbinding met uw on-premises netwerk. U kunt ook een VPN-gateway gebruiken om verbinding te maken met VNets.

In de stappen in dit artikel wordt een VNet, een subnet, een gateway-subnet en een op route gebaseerde VPN-gateway (virtuele netwerk gateway) gemaakt. Nadat het maken van de gateway is voltooid, kunt u verbindingen maken. Voor deze stappen is een Azure-abonnement vereist. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location EastUS
```

## <a name="vnet"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In het volgende voor beeld wordt een virtueel netwerk met de naam **VNet1** gemaakt op de locatie **eastus** :

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
```

Maak een subnet-configuratie met behulp van de cmdlet [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) .

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Stel de configuratie van het subnet voor het virtuele netwerk in met behulp van de cmdlet [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) .


```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="gwsubnet"></a>Een gateway-subnet toevoegen

Het gateway-subnet bevat de gereserveerde IP-adressen die door de Gateway Services van het virtuele netwerk worden gebruikt. Gebruik de volgende voor beelden om een gateway-subnet toe te voegen:

Stel een variabele in voor uw VNet.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
```

Maak het gateway-subnet met behulp van de cmdlet [add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-azVirtualNetworkSubnetConfig) .

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
```

Stel de configuratie van het subnet voor het virtuele netwerk in met behulp van de cmdlet [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) .

```azurepowershell-interactive
$vnet | Set-AzVirtualNetwork
```

## <a name="PublicIP"></a>Een openbaar IP-adres aanvragen

Een VPN-gateway moet een dynamisch toegewezen openbaar IP-adres hebben. Wanneer u een verbinding met een VPN-gateway maakt, is dit het IP-adres dat u opgeeft. Gebruik het volgende voor beeld om een openbaar IP-adres aan te vragen:

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>De configuratie van het IP-adres van de gateway maken

De gatewayconfiguratie bepaalt welk subnet en openbaar IP-adres moeten worden gebruikt. Gebruik het volgende voorbeeld om de gatewayconfiguratie te maken:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```
## <a name="CreateGateway"></a>De VPN-gateway maken

Het maken van een VPN-gateway kan tot 45 minuten of langer duren. Zodra de gateway is voltooid, kunt u een verbinding maken tussen uw virtuele netwerk en een ander VNet. U kunt ook een verbinding maken tussen uw virtuele netwerk en een on-premises locatie. Maak een VPN-gateway met de cmdlet [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway).

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="viewgw"></a>De VPN-gateway weer geven

U kunt de VPN-gateway weer geven met behulp van de cmdlet [Get-AzVirtualNetworkGateway](/powershell/module/az.network/Get-azVirtualNetworkGateway) .

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -Name Vnet1GW -ResourceGroup TestRG1
```

De uitvoer ziet er ongeveer uit als in dit voor beeld:

```
Name                   : VNet1GW
ResourceGroupName      : TestRG1
Location               : eastus
Id                     : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provide
                         rs/Microsoft.Network/virtualNetworkGateways/VNet1GW
Etag                   : W/"0952d-9da8-4d7d-a8ed-28c8ca0413"
ResourceGuid           : dc6ce1de-2c4494-9d0b-20b03ac595
ProvisioningState      : Succeeded
Tags                   :
IpConfigurations       : [
                           {
                             "PrivateIpAllocationMethod": "Dynamic",
                             "Subnet": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet"
                             },
                             "PublicIpAddress": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP"
                             },
                             "Name": "default",
                             "Etag": "W/\"0952d-9da8-4d7d-a8ed-28c8ca0413\"",
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                         RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/de
                         fault"
                           }
                         ]
GatewayType            : Vpn
VpnType                : RouteBased
EnableBgp              : False
ActiveActive           : False
GatewayDefaultSite     : null
Sku                    : {
                           "Capacity": 2,
                           "Name": "VpnGw1",
                           "Tier": "VpnGw1"
                         }
VpnClientConfiguration : null
BgpSettings            : {
     
```

## <a name="viewgwpip"></a>Het open bare IP-adres weer geven

Als u het open bare IP-adres voor uw VPN-gateway wilt weer geven, gebruikt u de cmdlet [Get-AzPublicIpAddress](/powershell/module/az.network/Get-azPublicIpAddress) .

```azurepowershell-interactive
Get-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1
```

In het antwoord van het voor beeld is de waarde IpAddress het open bare IP-adres.

```
Name                     : VNet1GWIP
ResourceGroupName        : TestRG1
Location                 : eastus
Id                       : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provi
                           ders/Microsoft.Network/publicIPAddresses/VNet1GWIP
Etag                     : W/"5001666a-bc2a-484b-bcf5-ad488dabd8ca"
ResourceGuid             : 3c7c481e-9828-4dae-abdc-f95b383
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : 13.90.153.3
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                           RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/
                           default"
                           }
DnsSettings              : null
Zones                    : {}
Sku                      : {
                             "Name": "Basic"
                           }
IpTags                   : {}
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de gemaakte resources niet meer nodig hebt, gebruikt u de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) om de resourcegroep te verwijderen. Hiermee verwijdert u de resourcegroep en alle resources die deze bevat.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="next-steps"></a>Volgende stappen

Zodra de gateway is gemaakt, kunt u een verbinding maken tussen uw virtuele netwerk en een ander VNet. U kunt ook een verbinding maken tussen uw virtuele netwerk en een on-premises locatie.

> [!div class="nextstepaction"]
> [Een site-naar-site-verbinding maken](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Een punt-naar-site-verbinding maken](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Een verbinding met een ander VNet maken](vpn-gateway-vnet-vnet-rm-ps.md)
