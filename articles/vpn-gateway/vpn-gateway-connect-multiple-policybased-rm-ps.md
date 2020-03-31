---
title: 'Azure VPN-gateway: gateways verbinden met meerdere on-premises op beleid gebaseerde VPN-apparaten'
description: Configureer een Azure-routegebaseerde VPN-gateway naar meerdere vpn-apparaten op basis van beleid met Azure Resource Manager en PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: yushwang
ms.openlocfilehash: 687c33e50a986cf8af08d0201fe0159a79cf02a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123325"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Azure VPN-gateways verbinden met meerdere on-premises vpn-apparaten op basis van beleid met PowerShell

Met dit artikel u een op Azure-route gebaseerde VPN-gateway configureren om verbinding te maken met meerdere on-premises op beleid gebaseerde VPN-apparaten die gebruikmaken van aangepast IPsec/IKE-beleid op S2S VPN-verbindingen.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a><a name="about"></a>Over beleidsgebaseerde en routegebaseerde VPN-gateways

Beleidsgebaseerde *versus* routegebaseerde VPN-apparaten verschillen in de manier waarop de IPsec-verkeerskiezer op een verbinding is ingesteld:

* **Op beleid gebaseerd** VPN-apparaten gebruiken de combinaties van voorvoegsels van beide netwerken om te bepalen hoe verkeer wordt versleuteld / gedecodeerd via IPsec-tunnels. Het is meestal gebouwd op firewall-apparaten die packet filtering uitvoeren. IPsec-tunnelversleuteling en -ontsleuteling worden toegevoegd aan de engine voor pakketfiltering en verwerking.
* **Op route gebaseerd** VPN-apparaten gebruiken elke -to-any (wildcard) traffic selectors en laten routerings-/doorstuurtabellen het verkeer naar verschillende IPsec-tunnels leiden. Het is meestal gebouwd op router platforms waar elke IPsec tunnel is gemodelleerd als een netwerkinterface of VTI (virtuele tunnel interface).

In de volgende diagrammen worden de twee modellen belicht:

### <a name="policy-based-vpn-example"></a>Vpn-voorbeeld op basis van beleid
![op beleid gebaseerd](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Vpn-voorbeeld op basis van route
![routegebaseerd](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Azure-ondersteuning voor vpn op basis van beleid
Momenteel ondersteunt Azure beide modi van VPN-gateways: routegebaseerde VPN-gateways en beleidsgebaseerde VPN-gateways. Ze zijn gebouwd op verschillende interne platforms, wat resulteert in verschillende specificaties:

|                          | **Beleidsgebaseerde VPN-gateway** | **Routegebaseerde VPN-gateway**       |**Routegebaseerde VPN-gateway**                          |
| ---                      | ---                         | ---                              |---                                                 |
| **Azure Gateway SKU**    | Basic                       | Basic                            | VpnGw1, VpnGw2, VpnGw3, VpnGw4, VpnGw5  |
| **IKE-versie**          | IKEv1                       | IKEv2                            | IKEv1 en IKEv2                         |
| **Max. S2S-verbindingen** | **1**                       | 10                               | 30                     |
|                          |                             |                                  |                                                    |

Met het aangepaste IPsec/IKE-beleid u nu Azure-routegebaseerde VPN-gateways configureren om op voorvoegsel gebaseerde verkeersselectors te gebruiken met optie**PolicyBasedTrafficSelectors**, om verbinding te maken met on-premises vpn-apparaten op basis van beleid. Met deze mogelijkheid u verbinding maken vanaf een Azure virtueel netwerk en VPN-gateway naar meerdere on-premises op beleid gebaseerde VPN/firewall-apparaten, waardoor de enkele verbindingslimiet wordt verwijderd uit de huidige VPN-gateways op basis van Azure-beleid.

> [!IMPORTANT]
> 1. Om deze connectiviteit mogelijk te maken, moeten uw on-premises vpn-apparaten op basis van beleid **IKEv2** ondersteunen om verbinding te maken met de Azure-routegebaseerde VPN-gateways. Controleer de specificaties van uw VPN-apparaat.
> 2. De on-premises netwerken die via beleidsgebaseerde VPN-apparaten met dit mechanisme verbinding maken, kunnen alleen verbinding maken met het virtuele Azure-netwerk; **ze kunnen niet via dezelfde Azure VPN-gateway naar andere on-premises netwerken of virtuele netwerken worden geleid.**
> 3. De configuratieoptie maakt deel uit van het aangepaste IPsec/IKE-verbindingsbeleid. Als u de op beleid gebaseerde verkeerskiezeroptie inschakelt, moet u het volledige beleid opgeven (IPsec/IKE-versleutelings- en integriteitsalgoritmen, sleutelsterktes en SA-levensduur).

In het volgende diagram ziet u waarom transitroutering via Azure VPN-gateway niet werkt met de op beleid gebaseerde optie:

![op beleid gebaseerde doorvoer](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Zoals in het diagram wordt weergegeven, heeft de Azure VPN-gateway verkeersselectoren van het virtuele netwerk naar elk van de on-premises netwerkvoorvoegsels, maar niet de voorvoegsels voor verbindingen. On-premises site 2, site 3 en site 4 kunnen bijvoorbeeld elk communiceren met respectievelijk VNet1, maar kunnen geen verbinding maken via de Azure VPN-gateway met elkaar. Het diagram toont de cross-connect verkeersselectors die niet beschikbaar zijn in de Azure VPN-gateway onder deze configuratie.

## <a name="workflow"></a><a name="workflow"></a>Werkstroom

De instructies in dit artikel volgen hetzelfde voorbeeld als beschreven in [Het Beleid voor IPsec/IKE configureren voor S2S- of VNet-verbindingen](vpn-gateway-ipsecikepolicy-rm-powershell.md) om een S2S VPN-verbinding tot stand te brengen. Dit wordt weergegeven in het volgende diagram:

![s2s-beleid](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

De workflow om deze connectiviteit in te schakelen:
1. Maak het virtuele netwerk, de VPN-gateway en de lokale netwerkgateway voor uw cross-premises verbinding.
2. Maak een IPsec/IKE-beleid.
3. Pas het beleid toe wanneer u een S2S- of VNet-verbinding maakt en **schakel de op beleid gebaseerde verkeerskiezer** op de verbinding in.
4. Als de verbinding al is gemaakt, u het beleid toepassen of bijwerken op een bestaande verbinding.

## <a name="before-you-begin"></a>Voordat u begint

* Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).

* [!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enable-policy-based-traffic-selectors"></a><a name="enablepolicybased"></a>Beleidsgebaseerde verkeerskiezer inschakelen

In deze sectie ziet u hoe u op beleid gebaseerde verkeerskiezer inschakelt op een verbinding. Zorg ervoor dat u [deel 3 van het beleidsartikel IPsec/IKE configureren](vpn-gateway-ipsecikepolicy-rm-powershell.md)hebt voltooid. De stappen in dit artikel gebruiken dezelfde parameters.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Stap 1 - De virtuele netwerk-, VPN-gateway en lokale netwerkgateway maken

#### <a name="connect-to-your-subscription-and-declare-your-variables"></a>Maak verbinding met uw abonnement en declareer uw variabelen

1. Als u PowerShell lokaal op uw computer gebruikt, meldt u zich aan met de cmdlet *Connect-AzAccount.* Of gebruik in plaats daarvan Azure Cloud Shell in uw browser.

2. Declareer uw variabelen. Voor deze oefening gebruiken we de volgende variabelen:

   ```azurepowershell-interactive
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

#### <a name="create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>De virtuele netwerk-, VPN-gateway en lokale netwerkgateway maken

1. Maak een resourcegroep.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
2. Gebruik het volgende voorbeeld om het virtuele netwerk TestVNet1 te maken met drie subnetten en de VPN-gateway. Als u waarden wilt vervangen, is het belangrijk dat u altijd uw gatewaysubnet specifiek 'GatewaySubnet' noemt. Als u een andere naam kiest, mislukt het maken van de gateway.

    ```azurepowershell-interactive
    $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
    
    New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
    
    $gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    
    New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
    
    New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
    ```

### <a name="step-2---create-an-s2s-vpn-connection-with-an-ipsecike-policy"></a>Stap 2 - Een S2S VPN-verbinding maken met een IPsec/IKE-beleid

1. Maak een IPsec/IKE-beleid.

   > [!IMPORTANT]
   > U moet een IPsec/IKE-beleid maken om de optie 'UsePolicyBasedTrafficSelectors' op de verbinding in te schakelen.

   In het volgende voorbeeld wordt een IPsec/IKE-beleid met de volgende algoritmen en parameters gezoend:
    * IKEv2: AES256, SHA384, DHGroup24
    * IPsec: AES256, SHA256, PFS None, SA Lifetime 14400 seconden & 102400000KB

   ```azurepowershell-interactive
   $ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
   ```
1. Maak de S2S VPN-verbinding met beleidsgebaseerde verkeersselectors en IPsec/IKE-beleid en pas het IPsec/IKE-beleid toe dat in de vorige stap is gemaakt. Wees je bewust van de extra parameter "-UsePolicyBasedTrafficSelectors $True", die beleidsgebaseerde verkeersselectors op de verbinding mogelijk maakt.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
   $lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

   New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
   ```
1. Na het voltooien van de stappen maakt de S2S VPN-verbinding gebruik van het ipsec/IKE-beleid dat is gedefinieerd en worden beleidsgebaseerde verkeersselectors op de verbinding ingeschakeld. U dezelfde stappen herhalen om meer verbindingen toe te voegen aan aanvullende on-premises vpn-apparaten op basis van beleid vanaf dezelfde Azure VPN-gateway.

## <a name="to-update-policy-based-traffic-selectors"></a><a name="update"></a>Verkeerskiezer voor beleid bijwerken
In deze sectie ziet u hoe u de optie op beleid gebaseerde verkeerskiezer voor een bestaande S2S VPN-verbinding bijwerkt.

1. Haal de verbindingsbron op.

   ```azurepowershell-interactive
   $RG1          = "TestPolicyRG1"
   $Connection16 = "VNet1toSite6"
   $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
   ```
1. Bekijk de optie verkeerskiezer op basis van beleid.
In de volgende regel wordt weergegeven of de op beleid gebaseerde verkeerskiezer voor de verbinding wordt gebruikt:

   ```azurepowershell-interactive
   $connection6.UsePolicyBasedTrafficSelectors
   ```

   Als de regel **'True'** retourneert, worden op beleidsgebaseerde verkeerskiezer op de verbinding geconfigureerd. anders keert het terug "**False**."
1. Zodra u de verbindingsbron hebt verkregen, u de op beleid gebaseerde verkeerskiezer op een verbinding in- of uitschakelen.

   - Inschakelen

      In het volgende voorbeeld wordt de optie op beleid gebaseerde verkeerskiezer opgenomen, maar het IPsec/IKE-beleid ongewijzigd gelaten:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
      ```

   - Uitschakelen

      In het volgende voorbeeld wordt de optie op basis van beleid gebaseerde verkeerskiezer uitgeschakeld, maar wordt het IPsec/IKE-beleid ongewijzigd gelaten:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
      ```

## <a name="next-steps"></a>Volgende stappen
Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Een virtuele machine maken](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voor de stappen.

Bekijk ook [het Beleid voor IPsec/IKE configureren voor S2S VPN- of VNet-naar-VNet-verbindingen](vpn-gateway-ipsecikepolicy-rm-powershell.md) voor meer informatie over aangepast IPsec/IKE-beleid.
