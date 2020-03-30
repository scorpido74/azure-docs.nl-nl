---
title: Configuratie van Partner VPN-apparaten voor verbinding met Azure VPN-gateways
description: In dit artikel vindt u een overzicht van de configuratie van vpn-apparaten van partners voor verbinding maken met Azure VPN-gateways.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: b914afaa6725920078da309981bcda5bb765e155
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279401"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Overzicht van de configuratie van partner-VPN-apparaten
Dit artikel biedt een overzicht van het configureren van on-premises VPN-apparaten voor verbinding maken met Azure VPN-gateways. Een voorbeeld van het virtuele netwerk en de VPN-gateway wordt gebruikt om u te laten zien hoe u verbinding maken met verschillende on-premises VPN-apparaatconfiguraties met behulp van dezelfde parameters.



## <a name="device-requirements"></a>Vereisten voor apparaten
Azure VPN-gateways gebruiken standaard IPsec/IKE-protocolsuites voor site-to-site (S2S) VPN-tunnels. Zie [Over VPN-apparaten](vpn-gateway-about-vpn-devices.md)voor een lijst met IPsec/IKE-parameters en cryptografische algoritmen voor Azure VPN-gateways. U ook de exacte algoritmen en belangrijke sterktes opgeven voor een specifieke verbinding zoals beschreven in [Over cryptografische vereisten.](vpn-gateway-about-compliance-crypto.md)

## <a name="single-vpn-tunnel"></a><a name ="singletunnel"></a>Eén VPN-tunnel
De eerste configuratie in het voorbeeld bestaat uit één S2S VPN-tunnel tussen een Azure VPN-gateway en een on-premises VPN-apparaat. U het [Border Gateway Protocol (BGP)](#bgp)optioneel configureren in de VPN-tunnel.

![Diagram van één S2S VPN-tunnel](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Zie [Een site-to-site-verbinding configureren](vpn-gateway-howto-site-to-site-resource-manager-portal.md)voor stapsgewijze instructies voor het instellen van één VPN-tunnel. In de volgende secties worden de verbindingsparameters voor de voorbeeldconfiguratie opgegeven en wordt een PowerShell-script weergegeven waarmee u aan de slag.

### <a name="connection-parameters"></a>Verbindingsparameters
In deze sectie worden de parameters weergegeven voor de voorbeelden die in de vorige secties worden beschreven.

| **Parameter**                | **Waarde**                    |
| ---                          | ---                          |
| Voorvoegsels voor virtueel netwerkadres        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP-gateway azure VPN-gateway         | Azure VPN-gateway-IP         |
| On-premises adresvoorvoegsels | 10.51.0.0/16<br>10.52.0.0/16 |
| On-premises IP-apparaat VPN-apparaat    | On-premises IP-apparaat VPN-apparaat    |
| * Virtueel netwerk BGP ASN                | 65010                        |
| * Azure BGP-peer-IP           | 10.12.255.30                 |
| * On-premises BGP ASN         | 65050                        |
| * On-premises BGP peer IP     | 10.52.255.254                |

\*Optionele parameter alleen voor BGP.

### <a name="sample-powershell-script"></a>Voorbeeld van PowerShell-script
In deze sectie vindt u een voorbeeldscript om u op weg te helpen. Zie [Een S2S VPN-verbinding maken met PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)voor gedetailleerde instructies.

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subscription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name="optional-use-custom-ipsecike-policy-with-usepolicybasedtrafficselectors"></a><a name ="policybased"></a>(Optioneel) Gebruik aangepast IPsec/IKE-beleid met UsePolicyBasedTrafficSelectors
Als uw VPN-apparaten geen verkeersselectors ondersteunen, zoals routegebaseerde of VTI-configuraties, maakt u een aangepast IPsec/IKE-beleid met de optie [UsePolicyBasedTrafficSelectors.](vpn-gateway-connect-multiple-policybased-rm-ps.md)

> [!IMPORTANT]
> U moet een IPsec/IKE-beleid maken om de optie **UsePolicyBasedTrafficSelectors** op de verbinding in te schakelen.


Het voorbeeldscript maakt een IPsec/IKE-beleid met de volgende algoritmen en parameters:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, SA Lifetime 7.200 seconden en 20.480.000 KB (20 GB)

Het script past het IPsec/IKE-beleid toe en maakt de optie **UsePolicyBasedTrafficSelectors** op de verbinding mogelijk.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name="optional-use-bgp-on-s2s-vpn-connection"></a><a name ="bgp"></a>(Optioneel) BGP gebruiken op S2S VPN-verbinding
Wanneer u de S2S VPN-verbinding maakt, u optioneel BGP gebruiken [voor de VPN-gateway.](vpn-gateway-bgp-resource-manager-ps.md) Deze aanpak kent twee verschillen:

* De on-premises adresvoorvoegsels kunnen één hostadres zijn. Het on-premises IP-adres met BGP-peer wordt als volgt opgegeven:

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* Wanneer u de verbinding maakt, moet u de optie **-EnableBGP** instellen op $True:

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Volgende stappen
Zie [Active-active VPN-gateways configureren voor stapsgewijze instructies](vpn-gateway-activeactive-rm-powershell.md)voor het instellen van actieve VPN-gateways.

