---
title: Actieve S2S Azure VPN-gatewayverbindingen configureren
description: In dit artikel u actief actieve verbindingen configureren met Azure VPN-gateways met Azure Resource Manager en PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/24/2018
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: ec3697208434eb971e47136416f2c2cc541b5cea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244639"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Actieve S2S VPN-verbindingen configureren met Azure VPN-gateways

In dit artikel u de stappen doorlopen om actieve cross-premises en VNet-to-VNet-verbindingen te maken met behulp van het implementatiemodel Resource Manager en PowerShell.



## <a name="about-highly-available-cross-premises-connections"></a>Over zeer beschikbare cross-premises verbindingen
Om een hoge beschikbaarheid voor cross-premises en VNet-to-VNet-connectiviteit te bereiken, moet u meerdere VPN-gateways implementeren en meerdere parallelle verbindingen tot stand brengen tussen uw netwerken en Azure. Zie [zeer beschikbare cross-premises en VNet-to-VNet-connectiviteit](vpn-gateway-highlyavailable.md) voor een overzicht van connectiviteitsopties en topologie.

Dit artikel bevat de instructies voor het opzetten van een actieve cross-premises VPN-verbinding en actieve actieve verbinding tussen twee virtuele netwerken.

* [Deel 1 - Uw Azure VPN-gateway maken en configureren in actief-actieve modus](#aagateway)
* [Deel 2 - Actieve cross-premises verbindingen tot stand brengen](#aacrossprem)
* [Deel 3 - Actieve VNet-to-VNet-verbindingen tot stand brengen](#aav2v)

Als u al een VPN-gateway hebt, u:
* [Een bestaande VPN-gateway bijwerken van active-stand-by naar actief actief, of vice versa](#aaupdate)

U deze combineren om een complexere, zeer beschikbare netwerktopologie op te bouwen die aan uw behoeften voldoet.

> [!IMPORTANT]
> De actief-actieve modus gebruikt alleen de volgende SKU's: 
>   * VpnGw1, VpnGw2, VpnGw3
>   * HighPerformance (voor oude legacy SKU's)

## <a name="part-1---create-and-configure-active-active-vpn-gateways"></a><a name ="aagateway"></a>Deel 1 - Actieve VPN-gateways maken en configureren
De volgende stappen configureren uw Azure VPN-gateway in actief-actieve modi. De belangrijkste verschillen tussen de actief-actieve en actieve stand-by gateways:

* U moet twee GATEWAY IP-configuraties maken met twee openbare IP-adressen
* U moet de vlag Van EnableActiveActiveFeature instellen
* De gateway SKU moet VpnGw1, VpnGw2, VpnGw3 of HighPerformance (legacy SKU) zijn.

De andere eigenschappen zijn hetzelfde als de niet-actieve gateways. 

### <a name="before-you-begin"></a>Voordat u begint
* Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial/).
* U moet de Azure Resource Manager PowerShell-cmdlets installeren. Zie [Overzicht van Azure PowerShell](/powershell/azure/overview) voor meer informatie over het installeren van de PowerShell-cmdlets.

### <a name="step-1---create-and-configure-vnet1"></a>Stap 1 - VNet1 maken en configureren
#### <a name="1-declare-your-variables"></a>1.
Voor deze oefening declareert u eerst de variabelen. In het volgende voorbeeld worden de variabelen gedeclareerd met de waarden voor deze oefening. Zorg dat u de waarden door uw eigen waarden vervangt wanneer u configureert voor productie. U kunt deze variabelen gebruiken als u de stappen wilt doorlopen om vertrouwd te raken met dit type configuratie. Wijzig de variabelen en kopieer en plak ze in uw PowerShell-console.

```powershell
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
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
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Maak verbinding met uw abonnement en maak een nieuwe brongroep
Zorg ervoor dat u overschakelt naar de PowerShell-modus als u de Resource Manager-cmdlets wilt gebruiken. Zie [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Windows PowerShell gebruiken met Resource Manager) voor meer informatie.

Open de PowerShell-console en maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. TestVNet1 maken
In onderstaand voorbeeld wordt een virtueel netwerk gemaakt met de naam TestVNet1. Er worden ook drie subnetten gemaakt, GatewaySubnet, FrontEnd en BackEnd. Wanneer u de waarden vervangt, is het belangrijk dat u de juiste namen voor de gatewaysubnets gebruikt, in het bijzonder GatewaySubnet. Als u een andere naam kiest, mislukt het maken van de gateway.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Stap 2 - De VPN-gateway voor TestVNet1 maken met actief-actieve modus
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. De openbare IP-adressen en gateway-IP-configuraties maken
Vraag twee openbare IP-adressen aan de gateway die u voor uw VNet maakt. U definieert ook de vereiste subnet- en IP-configuraties.

```powershell
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. De VPN-gateway maken met actief actieve configuratie
Maak de gateway van het virtuele netwerk voor TestVNet1. Houd er rekening mee dat er twee GatewayIpConfig-vermeldingen zijn en dat de vlag EnableActiveActiveFeature is ingesteld. Het maken van een gateway kan even duren (45 minuten of langer).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Verkrijg de openbare IP-adressen van de gateway en het IP-adres van BGP-peer
Zodra de gateway is gemaakt, moet u het IP-adres van BGP-peer op de Azure VPN-gateway verkrijgen. Dit adres is nodig om de Azure VPN Gateway te configureren als Een BGP-peer voor uw on-premises VPN-apparaten.

```powershell
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Gebruik de volgende cmdlets om de twee openbare IP-adressen te tonen die zijn toegewezen voor uw VPN-gateway en de bijbehorende IP-adressen van BGP-peer voor elke gateway-instantie:

```powershell
PS D:\> $gw1pip1.IpAddress
40.112.190.5

PS D:\> $gw1pip2.IpAddress
138.91.156.129

PS D:\> $vnet1gw.BgpSettingsText
{
  "Asn": 65010,
  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
  "PeerWeight": 0
}
```

De volgorde van de openbare IP-adressen voor de gateway-exemplaren en de bijbehorende BGP-peeringadressen zijn hetzelfde. In dit voorbeeld gebruikt de gateway-VM met openbaar IP van 40.112.190.5 10.12.255.4 als BGP-peeringadres en de gateway met 138.91.156.129 gebruikt 10.12.255.5. Deze informatie is nodig wanneer u uw on-premises VPN-apparaten instelt die verbinding maken met de actief actieve gateway. De gateway wordt weergegeven in het onderstaande diagram met alle adressen:

![actief actieve gateway](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Zodra de gateway is gemaakt, u deze gateway gebruiken om actief-actieve cross-premises of VNet-naar-VNet-verbinding tot stand te brengen. De volgende secties lopen door de stappen om de oefening te voltooien.

## <a name="part-2---establish-an-active-active-cross-premises-connection"></a><a name ="aacrossprem"></a>Deel 2 - Een actieve cross-premises verbinding tot stand brengen
Als u een cross-premises verbinding wilt maken, moet u een local network gateway maken om uw on-premises VPN-apparaat te vertegenwoordigen en een verbinding om de Azure VPN-gateway te verbinden met de lokale netwerkgateway. In dit voorbeeld bevindt de Azure VPN-gateway zich in de actieve modus. Als gevolg hiervan, hoewel er slechts één on-premises VPN-apparaat (lokale netwerkgateway) en één verbindingsbron is, zullen beide Azure VPN-gateway-exemplaren S2S VPN-tunnels instellen met het on-premises apparaat.

Voordat u verder gaat, moet u ervoor zorgen dat u [deel 1](#aagateway) van deze oefening hebt voltooid.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Stap 1 - De lokale netwerkgateway maken en configureren
#### <a name="1-declare-your-variables"></a>1.
Deze oefening blijft de configuratie in het diagram bouwen. Zorg ervoor dat u de waarden vervangt door de waarden die u voor uw configuratie wilt gebruiken.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Een paar dingen op te merken met betrekking tot de lokale netwerk gateway parameters:

* De lokale netwerkgateway kan zich in dezelfde of andere locatie- en resourcegroep bevinden als de VPN-gateway. In dit voorbeeld worden ze weergegeven in verschillende resourcegroepen, maar op dezelfde Azure-locatie.
* Als er slechts één on-premises VPN-apparaat is zoals hierboven wordt weergegeven, kan de actief-actieve verbinding werken met of zonder BGP-protocol. In dit voorbeeld wordt BGP gebruikt voor de cross-premises verbinding.
* Als BGP is ingeschakeld, is het voorvoegsel dat u moet declareren voor de lokale netwerkgateway het hostadres van uw BGP-peer-IP-adres op uw VPN-apparaat. In dit geval is het een /32 voorvoegsel van "10.52.255.253/32".
* Ter herinnering: u moet verschillende BGP-ASN's gebruiken tussen uw on-premises netwerken en Azure VNet. Als ze hetzelfde zijn, moet u uw VNet ASN wijzigen als uw on-premises VPN-apparaat al het ASN gebruikt om te peeren met andere BGP-buren.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. De lokale netwerkgateway voor Site5 maken
Controleer voordat u verder gaat of u nog bent verbonden met abonnement 1. Maak de resourcegroep als deze nog niet is gemaakt.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Stap 2 - De VNet-gateway en de lokale netwerkgateway verbinden
#### <a name="1-get-the-two-gateways"></a>1. Haal de twee gateways

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. De Verbinding TestVNet1 naar Site5 maken
In deze stap maakt u de verbinding van TestVNet1 naar Site5_1 met 'EnableBGP' ingesteld op $True.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. VPN- en BGP-parameters voor uw on-premises VPN-apparaat
In het onderstaande voorbeeld worden de parameters weergegeven die u invoert in de sectie BGP-configuratie op uw on-premises VPN-apparaat voor deze oefening:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.253
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

De verbinding moet na enkele minuten tot stand worden gebracht en de BGP-peeringsessie start zodra de IPsec-verbinding tot stand is gebracht. Dit voorbeeld tot nu toe heeft slechts één on-premises VPN-apparaat geconfigureerd, wat resulteert in het onderstaande diagram:

![actief-actief-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Stap 3 - Verbind twee on-premises VPN-apparaten met de actief actieve VPN-gateway
Als u twee VPN-apparaten op hetzelfde on-premises netwerk hebt, u dubbele redundantie bereiken door de Azure VPN-gateway te verbinden met het tweede VPN-apparaat.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. De tweede lokale netwerkgateway voor Site5 maken
Het IP-adres van de gateway, het adresvoorvoegsel en het BGP-peeringadres voor de tweede lokale netwerkgateway mogen niet overlappen met de vorige lokale netwerkgateway voor hetzelfde on-premises netwerk.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Sluit de VNet-gateway en de tweede lokale netwerkgateway aan
Maak de verbinding van TestVNet1 naar Site5_2 met 'EnableBGP' ingesteld op $True

```powershell
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. VPN- en BGP-parameters voor uw tweede on-premises VPN-apparaat
Op dezelfde manier worden hieronder de parameters vermeld die u in voert op het tweede VPN-apparaat:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Zodra de verbinding (tunnels) tot stand is gebracht, beschikt u over dubbele redundante VPN-apparaten en tunnels die uw on-premises netwerk en Azure verbinden:

![dual-redundantie-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name="part-3---establish-an-active-active-vnet-to-vnet-connection"></a><a name ="aav2v"></a>Deel 3 - Een actieve VNet-naar-VNet-verbinding tot stand brengen
Deze sectie maakt een actief actieve VNet-naar-VNet-verbinding met BGP. 

De onderstaande instructies volgen op de hierboven beschreven stappen. U moet [deel 1](#aagateway) voltooien om TestVNet1 en de VPN-gateway met BGP te maken en te configureren. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Stap 1 - Maak TestVNet2 en de VPN-gateway
Het is belangrijk om ervoor te zorgen dat de IP-adresruimte van het nieuwe virtuele netwerk, TestVNet2, niet overlapt met een van uw VNet-bereiken.

In dit voorbeeld behoren de virtuele netwerken tot hetzelfde abonnement. U VNet-naar-VNet-verbindingen tussen verschillende abonnementen instellen. raadpleeg [een VNet-to-VNet-verbinding configureren](vpn-gateway-vnet-vnet-rm-ps.md) voor meer informatie. Zorg ervoor dat u de $True inschakelen bij het maken van de verbindingen toevoegt om BGP in te schakelen.

#### <a name="1-declare-your-variables"></a>1.
Zorg ervoor dat u de waarden vervangt door de waarden die u voor uw configuratie wilt gebruiken.

```powershell
$RG2 = "TestAARG2"
$Location2 = "East US"
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
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
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

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Maak de actief actieve VPN-gateway voor TestVNet2
Vraag twee openbare IP-adressen aan de gateway die u voor uw VNet maakt. U definieert ook de vereiste subnet- en IP-configuraties.

```powershell
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Maak de VPN-gateway met het AS-nummer en de vlag 'EnableActiveActiveFeature'. Houd er rekening mee dat u de standaard ASN op uw Azure VPN-gateways moet overschrijven. De ASN's voor de aangesloten VNets moeten verschillend zijn om BGP- en transitroutering in te schakelen.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
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

Na het voltooien van deze stappen wordt de verbinding binnen enkele minuten tot stand gebracht en is de BGP-peeringsessie up zodra de VNet-naar-VNet-verbinding is voltooid met dubbele redundantie:

![actief-actief-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name="update-an-existing-vpn-gateway"></a><a name ="aaupdate"></a>Een bestaande VPN-gateway bijwerken

Met deze sectie u een bestaande Azure VPN-gateway wijzigen van active-stand-by naar actieve actieve modus, of vice versa.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Een active-stand-by gateway wijzigen in een actief actieve gateway

In het volgende voorbeeld wordt een active-stand-by gateway omgezet in een actief-actieve gateway. Wanneer u een active-stand-by gateway wijzigt in actief actief, maakt u een ander openbaar IP-adres en voegt u vervolgens een tweede Gateway IP-configuratie toe.

#### <a name="1-declare-your-variables"></a>1.

Vervang de volgende parameters die voor de voorbeelden worden gebruikt door de instellingen die u nodig hebt voor uw eigen configuratie en declareer deze variabelen.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Nadat u de variabelen hebt aangegeven, u dit voorbeeld kopiëren en plakken op uw PowerShell-console.

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Maak het openbare IP-adres en voeg vervolgens de tweede gateway-IP-configuratie toe

```powershell
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Actieve modus inschakelen en de gateway bijwerken

In deze stap schakelt u de actieve modus in en werkt u de gateway bij. In het voorbeeld gebruikt de VPN-gateway momenteel een verouderde Standard SKU. Actief-actief ondersteunt echter de Standaard SKU niet. Als u het formaat van de oudere SKU wilt wijzigen in een sku die wordt ondersteund (in dit geval HighPerformance), geeft u gewoon de ondersteunde oudere SKU op die u wilt gebruiken.

* U een oudere SKU niet wijzigen in een van de nieuwe SKU's met deze stap. U het formaat van een oudere SKU alleen wijzigen in een andere ondersteunde oudere SKU. U de SKU bijvoorbeeld niet wijzigen van Standard naar VpnGw1 (ook al wordt VpnGw1 ondersteund voor actief actief) omdat Standard een legacy SKU is en VpnGw1 een huidige SKU. Zie Gateway SKU's voor meer informatie over het wijzigen van het formaat en het migreren van [SKU's.](vpn-gateway-about-vpngateways.md#gwsku)

* Als u het formaat van een huidige SKU, bijvoorbeeld VpnGw1 naar VpnGw3, wilt wijzigen, u dit doen met behulp van deze stap omdat de SKU's in dezelfde SKU-familie zijn. Om dit te doen, zou u de waarde gebruiken:```-GatewaySku VpnGw3```

Wanneer u dit in uw omgeving gebruikt, hoeft u de gateway niet op te geven als u de grootte van de gateway niet hoeft te wijzigen. U moet in deze stap het gatewayobject in PowerShell instellen om de daadwerkelijke update te activeren. Deze update kan 30 tot 45 minuten duren, zelfs als u het formaat van uw gateway niet wijzigt.

```powershell
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Een actief actieve gateway wijzigen in een active-stand-by gateway
#### <a name="1-declare-your-variables"></a>1.

Vervang de volgende parameters die voor de voorbeelden worden gebruikt door de instellingen die u nodig hebt voor uw eigen configuratie en declareer deze variabelen.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Nadat u de variabelen hebt aangegeven, krijgt u de naam van de IP-configuratie die u wilt verwijderen.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Verwijder de IP-gatewayconfiguratie en schakel de actieve modus uit

Gebruik dit voorbeeld om de IP-configuratie van de gateway te verwijderen en de actieve modus uit te schakelen. U moet het gatewayobject in PowerShell instellen om de daadwerkelijke update te activeren.

```powershell
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Deze update kan tot 30 tot 45 minuten duren.

## <a name="next-steps"></a>Volgende stappen
Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Een virtuele machine maken](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voor de stappen.
