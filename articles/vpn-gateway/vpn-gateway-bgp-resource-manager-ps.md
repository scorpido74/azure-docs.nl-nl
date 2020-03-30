---
title: 'Azure VPN-gateway: BGP configureren: PowerShell'
description: In dit artikel u BGP configureren met Azure VPN-gateways met Azure Resource Manager en PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 04/12/2017
ms.author: yushwang
ms.openlocfilehash: 78147a96d6d9e92c2602b6a83cbed743cf2abf37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152037"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>BGP configureren op Azure VPN-gateways met PowerShell
In dit artikel vindt u de stappen om BGP in te schakelen op een cross-premises Site-to-Site (S2S) VPN-verbinding en een VNet-naar-VNet-verbinding met behulp van het Implementatiemodel resourcebeheer en PowerShell.



## <a name="about-bgp"></a>Over BGP
BGP is het standaardprotocol voor routering dat doorgaans op internet wordt gebruikt voor het uitwisselen van routerings- en bereikbaarheidsgegevens tussen twee of meer netwerken. BGP maakt het mogelijk dat Azure VPN Gateways en uw on-premises VPN-apparaten, zogenaamde BGP-peers of neighbors, 'routes' kunnen uitwisselen die beide gateways informeren over de beschikbaarheid en bereikbaarheid voor deze voorvoegsels zodat ze via de juiste gateways of routers communiceren. Met BGP kan ook transitroutering tussen meerdere netwerken worden ingeschakeld door routes die een BGP-gateway leert van één BGP te propageren naar alle andere BGP-peers.

Zie [Overzicht van BGP met Azure VPN Gateways](vpn-gateway-bgp-overview.md) voor meer discussie over de voordelen van BGP en om inzicht te krijgen in de technische vereisten en overwegingen van het gebruik van BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Aan de slag met BGP op Azure VPN-gateways

In dit artikel vindt u de stappen om de volgende taken uit te voeren:

* [Deel 1 - BGP inschakelen op uw Azure VPN-gateway](#enablebgp)
* Deel 2 - Een cross-premises verbinding met BGP tot stand brengen
* [Deel 3 - Een VNet-naar-VNet-verbinding met BGP tot stand brengen](#v2vbgp)

Elk deel van de instructies vormt een basisbouwsteen voor het inschakelen van BGP in uw netwerkverbinding. Als u alle drie de delen voltooit, bouwt u de topologie zoals weergegeven in het volgende diagram:

![BGP-topologie](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

U onderdelen combineren om een complexer, multi-hop, transitnetwerk op te bouwen dat aan uw behoeften voldoet.

## <a name="part-1---configure-bgp-on-the-azure-vpn-gateway"></a><a name ="enablebgp"></a>Deel 1 - BGP configureren op de Azure VPN-gateway
De configuratiestappen stellen de BGP-parameters van de Azure VPN-gateway in zoals weergegeven in het volgende diagram:

![BGP-gateway](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Voordat u begint
* Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial/).
* Installeer de PowerShell-cmdlets van Azure Resource Manager. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor meer informatie over het installeren van de PowerShell-cmdlets. 

### <a name="step-1---create-and-configure-vnet1"></a>Stap 1 - VNet1 maken en configureren
#### <a name="1-declare-your-variables"></a>1.
Voor deze oefening beginnen we met het declareren van onze variabelen. In het volgende voorbeeld worden de variabelen aangeeft met behulp van de waarden voor deze oefening. Zorg dat u de waarden door uw eigen waarden vervangt wanneer u configureert voor productie. U kunt deze variabelen gebruiken als u de stappen wilt doorlopen om vertrouwd te raken met dit type configuratie. Wijzig de variabelen en kopieer en plak ze in uw PowerShell-console.

```powershell
$Sub1 = "Replace_With_Your_Subscription_Name"
$RG1 = "TestBGPRG1"
$Location1 = "East US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GWIPName1 = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection12 = "VNet1toVNet2"
$Connection15 = "VNet1toSite5"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Maak verbinding met uw abonnement en maak een nieuwe brongroep
Als u de cmdlets Resource Beheer wilt gebruiken, moet u overschakelen naar de PowerShell-modus. Zie [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Windows PowerShell gebruiken met Resource Manager) voor meer informatie.

Open de PowerShell-console en maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. TestVNet1 maken
In het volgende voorbeeld wordt een virtueel netwerk gemaakt met de naam TestVNet1 en drie subnetten, een zogenaamde GatewaySubnet, een genaamd FrontEnd en een backend. Wanneer u de waarden vervangt, is het belangrijk dat u de juiste namen voor de gatewaysubnets gebruikt, in het bijzonder GatewaySubnet. Als u een andere naam kiest, mislukt het maken van de gateway.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Stap 2 - De VPN-gateway voor TestVNet1 maken met BGP-parameters
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. De IP- en subnetconfiguraties maken
Vraag om een openbaar IP-adres toe te wijzen aan de gateway die u voor uw VNet gaat maken. U definieert ook de vereiste subnet- en IP-configuraties.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Maak de VPN-gateway met het AS-nummer
Maak de gateway van het virtuele netwerk voor TestVNet1. BGP vereist een Route-Based VPN-gateway, en ook de toevoegingsparameter-Asn om de ASN (AS-nummer) in te stellen voor TestVNet1. Als u de parameter ASN niet instelt, wordt ASN 65515 toegewezen. Het maken van een gateway kan even duren (30 minuten of langer).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Het IP-adres van Azure BGP-peer verkrijgen
Zodra de gateway is gemaakt, moet u het IP-adres van BGP-peer op de Azure VPN-gateway verkrijgen. Dit adres is nodig om de Azure VPN Gateway te configureren als Een BGP-peer voor uw on-premises VPN-apparaten.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

De laatste opdracht toont de bijbehorende BGP-configuraties op de Azure VPN-gateway; bijvoorbeeld:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Zodra de gateway is gemaakt, u deze gateway gebruiken om een cross-premises verbinding of VNet-naar-VNet-verbinding met BGP tot stand te brengen. De volgende secties lopen door de stappen om de oefening te voltooien.

## <a name="part-2---establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembbgp"></a>Deel 2 - Een cross-premises verbinding met BGP tot stand brengen

Als u een cross-premises verbinding wilt maken, moet u een Local Network Gateway maken om uw on-premises VPN-apparaat te vertegenwoordigen en een verbinding om de VPN-gateway te verbinden met de lokale netwerkgateway. Hoewel er artikelen zijn die u door deze stappen laten lopen, bevat dit artikel de extra eigenschappen die nodig zijn om de BGP-configuratieparameters op te geven.

![BGP voor cross-premises](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Voordat u verder gaat, moet u ervoor zorgen dat u [deel 1](#enablebgp) van deze oefening hebt voltooid.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Stap 1 - De lokale netwerkgateway maken en configureren

#### <a name="1-declare-your-variables"></a>1.

Deze oefening blijft de configuratie in het diagram bouwen. Zorg ervoor dat u de waarden vervangt door de waarden die u voor uw configuratie wilt gebruiken.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Een paar dingen op te merken met betrekking tot de lokale netwerk gateway parameters:

* De lokale netwerkgateway kan zich in dezelfde of andere locatie- en resourcegroep bevinden als de VPN-gateway. In dit voorbeeld worden ze weergegeven in verschillende resourcegroepen op verschillende locaties.
* Het voorvoegsel dat u moet declareren voor de lokale netwerkgateway is het hostadres van uw BGP-peer IP-adres op uw VPN-apparaat. In dit geval is het een /32 voorvoegsel van "10.52.255.254/32".
* Ter herinnering: u moet verschillende BGP-ASN's gebruiken tussen uw on-premises netwerken en Azure VNet. Als ze hetzelfde zijn, moet u uw VNet ASN wijzigen als uw on-premises VPN-apparaat al het ASN gebruikt om te peeren met andere BGP-buren.

Controleer voordat u verdergaat of u nog bent verbonden met Abonnement 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. De lokale netwerkgateway voor Site5 maken

Zorg ervoor dat u de brongroep maakt als deze niet is gemaakt, voordat u de lokale netwerkgateway maakt. Let op de twee extra parameters voor de lokale netwerkgateway: Asn en BgpPeerAddress.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Stap 2 - De VNet-gateway en de lokale netwerkgateway verbinden

#### <a name="1-get-the-two-gateways"></a>1. Haal de twee gateways

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. De Verbinding TestVNet1 naar Site5 maken

In deze stap maakt u de verbinding van TestVNet1 naar Site5. U moet "-EnableBGP-$True" opgeven om BGP voor deze verbinding in te schakelen. Zoals eerder besproken, is het mogelijk om zowel BGP- als niet-BGP-verbindingen te hebben voor dezelfde Azure VPN-gateway. Tenzij BGP is ingeschakeld in de verbindingseigenschap, schakelt Azure Geen BGP in voor deze verbinding, ook al zijn BGP-parameters al geconfigureerd op beide gateways.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

In het volgende voorbeeld worden de parameters weergegeven die u invoert in de sectie BGP-configuratie op uw on-premises VPN-apparaat voor deze oefening:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

De verbinding wordt na een paar minuten tot stand gebracht en de BGP-peeringsessie begint zodra de IPsec-verbinding tot stand is gebracht.

## <a name="part-3---establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Deel 3 - Een VNet-naar-VNet-verbinding met BGP tot stand brengen

In deze sectie wordt een VNet-naar-VNet-verbinding met BGP toegevoegd, zoals in het volgende diagram wordt weergegeven:

![BGP voor VNet-naar-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

De volgende instructies gaan verder uit de vorige stappen. U moet [deel I](#enablebgp) voltooien om TestVNet1 en de VPN Gateway met BGP te maken en te configureren. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Stap 1 - Maak TestVNet2 en de VPN-gateway

Het is belangrijk om ervoor te zorgen dat de IP-adresruimte van het nieuwe virtuele netwerk, TestVNet2, niet overlapt met een van uw VNet-bereiken.

In dit voorbeeld behoren de virtuele netwerken tot hetzelfde abonnement. U VNet-naar-VNet-verbindingen tussen verschillende abonnementen instellen. Zie [Een VNet-naar-VNet-verbinding configureren](vpn-gateway-vnet-vnet-rm-ps.md)voor meer informatie. Zorg ervoor dat u de $True inschakelen bij het maken van de verbindingen toevoegt om BGP in te schakelen.

#### <a name="1-declare-your-variables"></a>1.

Zorg ervoor dat u de waarden vervangt door de waarden die u voor uw configuratie wilt gebruiken.

```powershell
$RG2 = "TestBGPRG2"
$Location2 = "West US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GWIPName2 = "VNet2GWIP"
$GWIPconfName2 = "gwipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. TestVNet2 maken in de nieuwe resourcegroep

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. Maak de VPN-gateway voor TestVNet2 met BGP-parameters

Vraag een openbaar IP-adres aan de gateway die u voor uw VNet maakt en definieer de vereiste subnet- en IP-configuraties.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Maak de VPN-gateway met het AS-nummer. U moet de standaard ASN op uw Azure VPN-gateways overschrijven. De ASN's voor de aangesloten VNets moeten verschillend zijn om BGP- en transitroutering in te schakelen.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Stap 2 - Sluit de TestVNet1- en TestVNet2-gateways aan

In dit voorbeeld bevinden beide gateways zich in hetzelfde abonnement. U deze stap voltooien in dezelfde PowerShell-sessie.

#### <a name="1-get-both-gateways"></a>1. Krijg beide gateways

Zorg dat u zich aanmeldt bij en verbinding maakt met Abonnement 1.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Beide verbindingen maken

In deze stap maakt u de verbinding van TestVNet1 naar TestVNet2 en de verbinding van TestVNet2 naar TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Zorg ervoor dat u BGP inschakelt voor beide verbindingen.
> 
> 

Na het voltooien van deze stappen wordt de verbinding na enkele minuten tot stand gebracht. De BGP-peeringsessie is up zodra de VNet-naar-VNet-verbinding is voltooid.

Als u alle drie de onderdelen van deze oefening hebt voltooid, hebt u de volgende netwerktopologie ingesteld:

![BGP voor VNet-naar-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Volgende stappen

Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Een virtuele machine maken](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voor de stappen.
