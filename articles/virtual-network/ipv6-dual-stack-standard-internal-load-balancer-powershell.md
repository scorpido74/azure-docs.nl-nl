---
title: Een IPv6 dual stack-toepassing implementeren met standaard interne load balancer in Azure - PowerShell
titlesuffix: Azure Virtual Network
description: In dit artikel ziet u hoe u een IPv6 dual stack-toepassing implementeert met Standard Internal Load Balancer in het virtuele Azure-netwerk van Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: fdf726fd31e8b92a04a1c136eb5cd7110e0c6d5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72333363"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>Een IPv6 dual stack-toepassing implementeren met standaard interne belastingsbalansr in Azure - PowerShell (voorbeeld)

In dit artikel ziet u hoe u een Dual Stack (IPv4 + IPv6)-toepassing implementeert in Azure met een dual stack virtueel netwerk en subnet, een standaard interne load balancer met dubbele (IPv4 + IPv6) front-endconfiguraties, VM's met NIC's met een dubbele IP configuratie, netwerkbeveiligingsgroep en openbare IP's.

> [!Important]
> IPv6-ondersteuning voor Azure Virtual Network is momenteel in openbare preview. Deze preview wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

De procedure voor het maken van een IPv6-geschikte interne load balancer is bijna identiek aan het proces voor het maken van een internet-facing IPv6 Load Balancer [hier](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)beschreven . De enige verschillen voor het maken van een interne load balancer zijn in de front-end configuratie zoals geïllustreerd in het PowerShell-voorbeeld hieronder:

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "ace:cab:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

De wijzigingen die het bovenstaande een interne load balancer front-end configuratie zijn:
- De `PrivateIpAddressVersion` is opgegeven als "IPv6"
- Het `-PublicIpAddress` argument is weggelaten of `-PrivateIpAddress`vervangen door . Houd er rekening mee dat het privéadres zich in het bereik van de IP-ruimte van subnet moet bevinden waarin de interne load balancer wordt geïmplementeerd. Als een `-PrivateIpAddress` statische wordt weggelaten, wordt het volgende gratis IPv6-adres geselecteerd uit het subnet waarin de interne load Balancer wordt geïmplementeerd.
- Het dual stack subnet waarin de interne load balancer wordt `-Subnet` `-SubnetId` ingezet, wordt gespecificeerd met een of argument.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest PowerShell lokaal te installeren en te gebruiken, vereist dit artikel de Azure PowerShell-moduleversie 6.9.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="prerequisites"></a>Vereisten
Voordat u een dual stack-toepassing implementeert in Azure, moet u uw abonnement voor deze voorbeeldfunctie configureren met de volgende Azure PowerShell:

Registreer als volgt:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Het duurt maximaal 30 minuten voordat de functieregistratie is voltooid. U uw registratiestatus controleren door de volgende opdracht Azure PowerShell uit te voeren: Controleer de registratie als volgt:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Voer de volgende opdracht uit nadat de registratie is voltooid:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u uw virtuele dual-stacknetwerk maken, moet u een resourcegroep maken met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep met de naam *dsStd_ILB_RG* in de *locatie oost-VS:*

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>IPv4- en IPv6-openbare IP-adressen maken
Om toegang te krijgen tot uw virtuele machines vanaf het internet, hebt u IPv4- en IPv6-openbare IP-adressen voor de VM's nodig. Openbare IP-adressen maken met [Nieuw-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). In het volgende voorbeeld wordt iPv4- en IPv6-openbaar IP-adres gemaakt met de naam *RdpPublicIP_1* en *RdpPublicIP_2* in de *brongroep dsStd_ILB_RG:*

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>Het virtuele netwerk en het subnet maken

Maak een virtueel netwerk met [behulp van New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) met dual stack een subnet configuratie met behulp van [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). In het volgende voorbeeld wordt een virtueel netwerk met de naam *dsVnet* met *dsSubnet aanmaken.*

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>Een Standard Load Balancer maken

In deze sectie configureert u dual frontend IP (IPv4 en IPv6) en de back-end address pool voor de load balancer en maakt u vervolgens een Standaard Load Balancer.

### <a name="create-front-end-ip"></a>Maak een front-end IP-adres

Maak een front-end-IP-adres met [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). In het volgende voorbeeld worden IP-configuraties voor IPv4 en IPv6 met de naam *dsLbFrontEnd_v4* en *dsLbFrontEnd_v6:*

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "ace:cab:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>De back-endadresgroep configureren

Maak een back-end adresgroep met [Nieuw-AzLoadBalancerBackendAddressConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). In de resterende stappen worden de VM's aan deze back-end-groep gekoppeld. In het volgende voorbeeld worden back-endadresgroepen gemaakt met de naam *dsLbBackEndPool_v4* en *dsLbBackEndPool_v6* vm's met zowel IPV4- als IPv6 NIC-configuraties op te nemen:

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Als u ervoor wilt zorgen dat alleen gezonde VM's verkeer ontvangen, u optioneel een statussonde definiëren. Basic load balancer gebruikt een IPv4-sonde om de status van zowel IPv4- als IPv6-eindpunten op de VM's te beoordelen. Standaard load balancer bevat ondersteuning voor expliciet IPv6 health probes.

Maak een load balancer-regel met [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). In het volgende voorbeeld worden regels voor load balancer met de naam *dsLBrule_v4* en *dsLBrule_v6* en wordt verkeer op *TCP-poort* *80* in evenwicht gebracht met de IP-configuraties van IPv4 en IPv6:

```azurepowershell
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>Load balancer maken

Maak een standaard load balancer met [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). In het volgende voorbeeld wordt een openbare Standaardload Balancer gemaakt met de naam *myInternalLoadBalancer* met behulp van de IP-configuraties iPv4 en IPv6, backendpools en regels voor taakverdeling die u in de voorgaande stappen hebt gemaakt:

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Netwerkbronnen maken
Voordat u een aantal VM's implementeert en uw balancer testen, moet u ondersteunende netwerkbronnen maken - beschikbaarheidsset, netwerkbeveiligingsgroep en virtuele NIC's. 

### <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken
Als u de hoge beschikbaarheid van uw toepassing wilt verbeteren, plaatst u uw VM's in een beschikbaarheidsset.

Maak een beschikbaarheidsset met [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). In het volgende voorbeeld wordt een beschikbaarheidsset met de naam *dsAVset geopperd:*

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroep voor de regels die inkomende en uitgaande communicatie in uw VNet regelen.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Een netwerkbeveiligingsgroepsregel maken voor poort 3389

Maak een netwerkbeveiligingsgroepsregel die RDP-verbindingen via poort 3389 toestaat met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleRDP' `
  -Description 'Allow RDP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>Een netwerkbeveiligingsgroepsregel maken voor poort 80

Maak een netwerkbeveiligingsgroepregel om internetverbindingen via poort 80 toe te staan met [New-AzNetworkSecurityRuleConfig.](/powershell/module/az.network/new-aznetworksecurityruleconfig)

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroep met [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>NIC's maken

Virtuele NIC's maken met [New-AzNetworkInterface.](/powershell/module/az.network/new-aznetworkinterface) In het volgende voorbeeld worden twee virtuele NIC's met IPv4- en IPv6-configuraties gemaakt. (Eén virtuele NIC voor elke VM die u in de volgende stappen voor uw app maakt).

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>Virtuele machines maken

Stel een beheerdersnaam en -wachtwoord voor de VM’s in met [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

Nu kunt u de VM’s maken met [New-AzVM](/powershell/module/az.compute/new-azvm). In het volgende voorbeeld worden twee VM's en de vereiste onderdelen van het virtuele netwerk gemaakt als deze nog niet bestaan.

```azurepowershell
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1


$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>IPv6 dual stack virtueel netwerk weergeven in Azure portal
U het virtuele IPv6-dual stack-netwerk in Azure-portal als volgt bekijken:
1. Voer in de zoekbalk van het portaal *dsVnet*in.
2. Wanneer **dsVnet** in de zoekresultaten wordt weergegeven, selecteert u deze. Hiermee wordt de **overzichtspagina** van het virtuele dual stack-netwerk genaamd *dsVnet*gelanceerd. De dual stack virtuele netwerk toont de twee NIC's met zowel IPv4 en IPv6 configuraties gelegen in de dual stack subnet genaamd *dsSubnet*.

![IPv6 Dual Stack Virtual Network met standaard interne load balancer](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> Het virtuele IPv6 voor Azure-netwerk is beschikbaar in de Azure-portal in alleen-lezen voor deze preview-release.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt.

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Standard Load Balancer gemaakt met een dubbele ip-configuratie (IPv4 en IPv6). U hebt ook een twee virtuele machines gemaakt die NIC's met dubbele IP-configuraties (IPV4 + IPv6) bevatten die zijn toegevoegd aan de back-endpool van de load balancer. Zie [Wat is IPv6 voor Azure Virtual Network voor](ipv6-overview.md) meer informatie over IPv6-ondersteuning in virtuele Azure-netwerken?