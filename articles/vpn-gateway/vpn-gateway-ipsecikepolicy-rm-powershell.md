---
title: IPsec/IKE-beleid voor S2S VPN-& VNet-to-VNet-verbindingen
titleSuffix: Azure VPN Gateway
description: IPsec/IKE-beleid configureren voor S2S- of VNet-verbindingen met Azure VPN-gateways met Azure Resource Manager en PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: eaca48fc354f1cf37635e9729b04eaaaa882ba1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161899"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>IPsec/IKE-beleid configureren voor S2S VPN-verbindingen of VNet-naar-VNet-verbindingen

In dit artikel vindt u de stappen om het IPsec/IKE-beleid voor site-to-site VPN- of VNet-naar-VNet-verbindingen te configureren met behulp van het implementatiemodel voor Resource Manager en PowerShell.



## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a><a name="about"></a>Over iPsec- en IKE-beleidsparameters voor Azure VPN-gateways
IPsec en IKE protocol standaard ondersteunt een breed scala van cryptografische algoritmen in verschillende combinaties. Raadpleeg [over cryptografische vereisten en Azure VPN-gateways](vpn-gateway-about-compliance-crypto.md) om te zien hoe dit kan helpen om ervoor te zorgen dat cross-premises en VNet-to-VNet-connectiviteit voldoen aan uw nalevings- of beveiligingsvereisten.

In dit artikel vindt u instructies voor het maken en configureren van een IPsec/IKE-beleid en is dit van toepassing op een nieuwe of bestaande verbinding:

* [Deel 1 - Werkstroom voor het maken en instellen van IPsec/IKE-beleid](#workflow)
* [Deel 2 - Ondersteunde cryptografische algoritmen en belangrijke sterke punten](#params)
* [Deel 3 - Een nieuwe S2S VPN-verbinding maken met het IPsec/IKE-beleid](#crossprem)
* [Deel 4 - Een nieuwe VNet-naar-VNet-verbinding maken met het IPsec/IKE-beleid](#vnet2vnet)
* [Deel 5 - IPsec/IKE-beleid beheren (maken, toevoegen, verwijderen) voor een verbinding](#managepolicy)

> [!IMPORTANT]
> 1. Houd er rekening mee dat het IPsec/IKE-beleid alleen werkt op de volgende gateway-SKU's:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (route-based)
>    * ***Standaard*** en ***HighPerformance*** (routegebaseerd)
> 2. U kunt maar ***één*** beleidscombinatie opgeven voor een bepaalde verbinding.
> 3. U moet alle algoritmen en parameters opgeven voor zowel IKE (Main Mode) als IPsec (Quick Mode). Gedeeltelijke beleidsspecificatie is niet toegestaan.
> 4. Raadpleeg de specificaties van de leverancier van uw VPN-apparaat om ervoor te zorgen dat het beleid wordt ondersteund op uw on-premises VPN-apparaten. S2S- of VNet-naar-VNet-verbindingen kunnen niet vaststellen of het beleid onverenigbaar is.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a><a name ="workflow"></a>Deel 1 - Werkstroom voor het maken en instellen van IPsec/IKE-beleid
In deze sectie wordt de workflow beschreven om ipsec/IKE-beleid te maken en bij te werken op een S2S VPN- of VNet-verbinding:
1. Een virtueel netwerk en een VPN-gateway maken
2. Een lokale netwerkgateway maken voor cross premises-verbinding of een andere virtuele netwerk- en gateway voor VNet-verbinding
3. Een IPsec/IKE-beleid maken met geselecteerde algoritmen en parameters
4. Een verbinding maken (IPsec of VNet2VNet) met het IPsec/IKE-beleid
5. Een IPsec/IKE-beleid voor een bestaande verbinding toevoegen/bijwerken/verwijderen

Met de instructies in dit artikel u ipsec/IKE-beleid instellen en configureren zoals in het diagram wordt weergegeven:

![ipsec-ike-beleid](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name="part-2---supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Deel 2 - Ondersteunde cryptografische algoritmen & belangrijke sterke punten

In de volgende tabel worden de ondersteunde cryptografische algoritmen en sleutelsterktes weergegeven die door de klanten kunnen worden geconfigureerd:

| **IPsec/IKEv2**  | **Opties**    |
| ---  | --- 
| IKEv2-versleuteling | AES256, AES192, AES128, DES3, DES  
| IKEv2-integriteit  | SHA384, SHA256, SHA1, MD5  |
| DH-groep         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, Geen |
| IPsec-versleuteling | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, geen    |
| IPsec-integriteit  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS-groep        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, geen 
| QM SA-levensduur   | (**Optioneel**: standaardwaarden worden gebruikt als deze niet zijn opgegeven)<br>Seconden (geheel getal; **min. 300 **/standaard 27000 seconden)<br>KB (geheel getal; **min. 1024**/standaard 102400000 KB)   |
| Verkeersselector | UsePolicyBasedTrafficSelectors** ($True/$False; **Optioneel**, standaard $False als deze niet is opgegeven)    |
|  |  |

> [!IMPORTANT]
> 1. **De configuratie van uw on-premises VPN-apparaat moet overeenkomen met of de volgende algoritmen en parameters bevatten die u in het Azure IPsec/IKE-beleid opgeeft:**
>    * IKE-versleutelingsalgoritme (hoofdmodus / fase 1)
>    * IKE integriteitsalgoritme (Hoofdmodus / Fase 1)
>    * DH-groep (hoofdmodus / fase 1)
>    * IPsec encryptie-algoritme (Quick Mode / Fase 2)
>    * IPsec-integriteitsalgoritme (Quick Mode / Fase 2)
>    * PFS-groep (snelle modus / fase 2)
>    * Traffic Selector (if UsePolicyBasedTrafficSelectors is used)
>    * De SA-levensduren zijn alleen lokale specificaties en hoeven niet overeen te komen.
>
> 2. **Als GCMAES wordt gebruikt zoals voor iPsec Encryption-algoritme, moet u hetzelfde GCMAES-algoritme en de sleutellengte voor IPsec-integriteit selecteren; GCMAES128 bijvoorbeeld gebruiken voor beide**
> 3. In de bovenstaande tabel:
>    * IKEv2 komt overeen met hoofdmodus of fase 1
>    * IPsec komt overeen met de snelle modus of fase 2
>    * DH Group geeft de Diffie-Hellmen-groep op die wordt gebruikt in de hoofdmodus of fase 1
>    * PFS Group specificeerde de Diffie-Hellmen Group die wordt gebruikt in quick mode of fase 2
> 4. SA-levensduur voor IKEv2 Main Mode staat vastgesteld op 28.800 seconden op de Azure VPN-gateways
> 5. Als u 'UsePolicyBasedTrafficSelectors' instelt op $True op een verbinding, configureert u de Azure VPN-gateway om verbinding te maken met de vpn-firewall op basis van beleid. Als u PolicyBasedTrafficSelectors inschakelt, moet u ervoor zorgen dat uw VPN-apparaat de overeenkomende verkeersselectors heeft gedefinieerd met alle combinaties van uw on-premises netwerkvoorvoegsels (local network gateway) van/naar de voorvoegsels van het Azure-virtuele netwerk, in plaats van alle-to-any.If you enable PolicyBasedTrafficSelectselectors, you need to ensure your VPN device has the matching traffic selectors defined with all combinations of your on-premises network (local network gateway) prefixes to/from the Azure virtual network prefixes, instead of any-to-any. Als uw lokale netwerkvoorvoegsels bijvoorbeeld 10.1.0.0/16 en 10.2.0.0/16 zijn, en de voorvoegsels van uw virtuele netwerk 192.168.0.0/16 en 172.16.0.0/16, moet u de volgende verkeersselectoren opgeven:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Zie [Meerdere on-premises beleidsgebaseerde VPN-apparaten](vpn-gateway-connect-multiple-policybased-rm-ps.md)verbinden voor meer informatie over beleidsgebaseerde verkeersselectors.

In de volgende tabel worden de bijbehorende Diffie-Hellman-groepen weergegeven die worden ondersteund door het aangepaste beleid:

| **Diffie-Hellman-groep**  | **DHGroup**              | **PFSGroup** | **Sleutellengte** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768-bits MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024-bits MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048-bits MODP  |
| 19                        | ECP256                   | ECP256       | 256-bits ECP    |
| 20                        | ECP384                   | ECP384       | 384-bits ECP    |
| 24                        | DHGroup24                | PFS24        | 2048-bits MODP  |

Raadpleeg [RFC3526](https://tools.ietf.org/html/rfc3526) en [RFC5114](https://tools.ietf.org/html/rfc5114) voor meer informatie.

## <a name="part-3---create-a-new-s2s-vpn-connection-with-ipsecike-policy"></a><a name ="crossprem"></a>Deel 3 - Een nieuwe S2S VPN-verbinding maken met het IPsec/IKE-beleid

In deze sectie loopt u door de stappen van het maken van een S2S VPN-verbinding met een IPsec/IKE-beleid. Met de volgende stappen wordt de verbinding gemaakt zoals in het diagram wordt weergegeven:

![s2s-beleid](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Zie [Een S2S VPN-verbinding maken](vpn-gateway-create-site-to-site-rm-powershell.md) voor meer gedetailleerde stapsgewijze instructies voor het maken van een S2S VPN-verbinding.

### <a name="before-you-begin"></a><a name="before"></a>Voordat u begint

* Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial/).
* Installeer de PowerShell-cmdlets van Azure Resource Manager. Zie [Overzicht van Azure PowerShell](/powershell/azure/overview) voor meer informatie over het installeren van de PowerShell-cmdlets.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>Stap 1 - De virtuele netwerk-, VPN-gateway en lokale netwerkgateway maken

#### <a name="1-declare-your-variables"></a>1.

Voor deze oefening beginnen we met het declareren van onze variabelen. Zorg dat u de waarden door uw eigen waarden vervangt wanneer u configureert voor productie.

```powershell
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
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
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Maak verbinding met uw abonnement en maak een nieuwe brongroep

Zorg ervoor dat u overschakelt naar de PowerShell-modus als u de Resource Manager-cmdlets wilt gebruiken. Zie [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Windows PowerShell gebruiken met Resource Manager) voor meer informatie.

Open de PowerShell-console en maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Maak het virtuele netwerk, vpn-gateway en de lokale netwerkgateway

In het volgende voorbeeld wordt het virtuele netwerk TestVNet1 gemaakt met drie subnetten en de VPN-gateway. Wanneer u de waarden vervangt, is het belangrijk dat u de juiste namen voor de gatewaysubnets gebruikt, in het bijzonder GatewaySubnet. Als u een andere naam kiest, mislukt het maken van de gateway.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a><a name="s2sconnection"></a>Stap 2 - Een S2S VPN-verbinding maken met een IPsec/IKE-beleid

#### <a name="1-create-an-ipsecike-policy"></a>1. Een IPsec/IKE-beleid maken

Met het volgende voorbeeldscript wordt een IPsec/IKE-beleid gemaakt met de volgende algoritmen en parameters:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS None, SA Lifetime 14400 seconden & 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Als u GCMAES voor IPsec gebruikt, moet u hetzelfde GCMAES-algoritme en de sleutellengte gebruiken voor zowel IPsec-versleuteling als integriteit. De bijbehorende parameters zijn bijvoorbeeld "-IpsecEncryption GCMAES256 -IpsecIntegrity GCMAES256" bij gebruik van GCMAES256.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Maak de S2S VPN-verbinding met het IPsec/IKE-beleid

Maak een S2S VPN-verbinding en pas het iPsec/IKE-beleid toe dat eerder is gemaakt.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

U optioneel -UsePolicyBasedTrafficSelectors $True toevoegen aan de cmdlet voor verbinding maken om azure VPN-gateway in staat te stellen verbinding te maken met op beleid gebaseerde VPN-apparaten op locatie, zoals hierboven beschreven.

> [!IMPORTANT]
> Zodra een IPsec/IKE-beleid is opgegeven op een verbinding, verzendt of accepteert de Azure VPN-gateway alleen het IPsec/IKE-voorstel met opgegeven cryptografische algoritmen en belangrijke sterke punten op die specifieke verbinding. Zorg ervoor dat uw on-premises VPN-apparaat voor de verbinding de exacte beleidscombinatie gebruikt of accepteert, anders zal de S2S VPN-tunnel niet worden vastgesteld.


## <a name="part-4---create-a-new-vnet-to-vnet-connection-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>Deel 4 - Een nieuwe VNet-naar-VNet-verbinding maken met het IPsec/IKE-beleid

De stappen van het maken van een VNet-naar-VNet-verbinding met een IPsec/IKE-beleid zijn vergelijkbaar met die van een S2S VPN-verbinding. Met de volgende voorbeeldscripts wordt de verbinding gemaakt zoals in het diagram wordt weergegeven:

![v2v-beleid](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Zie [Een VNet-naar-VNet-verbinding maken](vpn-gateway-vnet-vnet-rm-ps.md) voor meer gedetailleerde stappen voor het maken van een VNet-naar-VNet-verbinding. U moet [deel 3](#crossprem) voltooien om TestVNet1 en de VPN-gateway te maken en te configureren.

### <a name="step-1---create-the-second-virtual-network-and-vpn-gateway"></a><a name="createvnet2"></a>Stap 1 - Maak het tweede virtuele netwerk en VPN-gateway

#### <a name="1-declare-your-variables"></a>1.

Zorg ervoor dat u de waarden vervangt door de waarden die u voor uw configuratie wilt gebruiken.

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. Maak het tweede virtuele netwerk en VPN-gateway in de nieuwe brongroep

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Stap 2 - Een VNet-toVNet-verbinding maken met het IPsec/IKE-beleid

Net als bij de S2S VPN-verbinding, maak een IPsec / IKE beleid dan van toepassing op het beleid op de nieuwe verbinding.

#### <a name="1-create-an-ipsecike-policy"></a>1. Een IPsec/IKE-beleid maken

Het volgende voorbeeldscript maakt een ander beleid voor IPsec/IKE met de volgende algoritmen en parameters:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, SA Lifetime 14400 seconden & 102400000KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. VNet-to-VNet-verbindingen maken met het IPsec/IKE-beleid

Maak een VNet-naar-VNet-verbinding en pas het IPsec/IKE-beleid toe dat u hebt gemaakt. In dit voorbeeld bevinden beide gateways zich in hetzelfde abonnement. Het is dus mogelijk om beide verbindingen te maken en te configureren met hetzelfde IPsec/IKE-beleid in dezelfde PowerShell-sessie.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Zodra een IPsec/IKE-beleid is opgegeven op een verbinding, verzendt of accepteert de Azure VPN-gateway alleen het IPsec/IKE-voorstel met opgegeven cryptografische algoritmen en belangrijke sterke punten op die specifieke verbinding. Zorg ervoor dat het IPsec-beleid voor beide verbindingen hetzelfde is, anders wordt de VNet-naar-VNet-verbinding niet tot stand gebracht.

Na het voltooien van deze stappen wordt de verbinding in een paar minuten tot stand gebracht en hebt u de volgende netwerktopologie zoals in het begin wordt weergegeven:

![ipsec-ike-beleid](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name="part-5---update-ipsecike-policy-for-a-connection"></a><a name ="managepolicy"></a>Deel 5 - IPsec/IKE-beleid bijwerken voor een verbinding

In het laatste gedeelte ziet u hoe u het IPsec/IKE-beleid beheert voor een bestaande S2S- of VNet-verbinding. De oefening hieronder leidt u door de volgende bewerkingen op een verbinding:

1. Het IPsec/IKE-beleid van een verbinding weergeven
2. Het IPsec/IKE-beleid toevoegen of bijwerken aan een verbinding
3. Het IPsec/IKE-beleid verwijderen uit een verbinding

Dezelfde stappen zijn van toepassing op zowel S2S- als VNet-naar-VNet-verbindingen.

> [!IMPORTANT]
> IPsec/IKE-beleid wordt alleen ondersteund op *standaard-* en *HighPerformance-routegebaseerde* VPN-gateways. Het werkt niet op de Basic gateway SKU of de op beleid gebaseerde VPN-gateway.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Het IPsec/IKE-beleid van een verbinding weergeven

In het volgende voorbeeld ziet u hoe u het IPsec/IKE-beleid configureren op een verbinding. De scripts gaan ook verder van de oefeningen hierboven.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

De laatste opdracht bevat het huidige IPsec/IKE-beleid dat is geconfigureerd op de verbinding, als die er is. Het volgende is een voorbeelduitvoer voor de verbinding:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES256
IkeIntegrity        : SHA384
DhGroup             : DHGroup24
PfsGroup            : PFS24
```

Als er geen IPsec/IKE-beleid is geconfigureerd,> de opdracht (PS>$connection6. IpsecPolicies) krijgt een leeg rendement. Het betekent niet dat IPsec/IKE niet is geconfigureerd op de verbinding, maar dat er geen aangepast IPsec/IKE-beleid is. De werkelijke verbinding maakt gebruik van het standaardbeleid dat is overeengekomen tussen uw on-premises VPN-apparaat en de Azure VPN-gateway.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Een IPsec/IKE-beleid toevoegen of bijwerken voor een verbinding

De stappen om een nieuw beleid toe te voegen of een bestaand beleid voor een verbinding bij te werken zijn hetzelfde: maak een nieuw beleid en pas het nieuwe beleid toe op de verbinding.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Als u "UsePolicyBasedTrafficSelectors" wilt inschakelen wanneer u verbinding maakt met een on-premises beleidsgebaseerd VPN-apparaat, voegt u de parameter "-UsePolicyBaseTrafficSelectors" toe aan de cmdlet of stelt u deze in op $False om de optie uit te schakelen:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

U de verbinding opnieuw krijgen om te controleren of het beleid is bijgewerkt.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

U ziet de uitvoer van de laatste regel, zoals in het volgende voorbeeld wordt weergegeven:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES128
IkeIntegrity        : SHA1
DhGroup             : DHGroup14
PfsGroup            : None
```

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Een IPsec/IKE-beleid verwijderen uit een verbinding

Zodra u het aangepaste beleid van een verbinding verwijdert, keert de Azure VPN-gateway terug naar de [standaardlijst met IPsec/IKE-voorstellen](vpn-gateway-about-vpn-devices.md) en onderhandelt opnieuw met uw on-premises VPN-apparaat.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

U hetzelfde script gebruiken om te controleren of het beleid uit de verbinding is verwijderd.

## <a name="next-steps"></a>Volgende stappen

Zie [Meerdere on-premises vpn-apparaten op basis van beleid verbinden](vpn-gateway-connect-multiple-policybased-rm-ps.md) voor meer informatie over beleidsgebaseerde verkeersselectors.

Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Een virtuele machine maken](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voor de stappen.
