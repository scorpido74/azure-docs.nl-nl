---
title: IPv6 dual stack applicatie implementeren - Standard Load Balancer - PowerShell
titlesuffix: Azure Virtual Network
description: In dit artikel ziet u hoe u een IPv6 dual stack-toepassing implementeert met Standard Load Balancer in het virtuele Azure-netwerk van Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/17/2019
ms.author: kumud
ms.openlocfilehash: 96ede56e7b21d2447d238306e00f2c4fbca56f04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122233"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---powershell-preview"></a>Een IPv6 dual stack-toepassing implementeren in Azure - PowerShell (voorbeeld)

In dit artikel ziet u hoe u een dual stack -toepassing (IPv4 + IPv6) implementeert met behulp van Standard Load Balancer in Azure die een virtueel dual stack-netwerk en subnet bevat, een standaardloadbalansr met dubbele (IPv4 + IPv6) front-endconfiguraties, VM's met NIC's die een dubbele IP-configuratie, netwerkbeveiligingsgroep en openbare IP-gebruikers.

> [!Important]
> IPv6-ondersteuning voor Azure Virtual Network is momenteel in openbare preview. Deze preview wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

Voordat u uw virtuele dual-stacknetwerk maken, moet u een resourcegroep maken met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep met de naam *myRGDualStack* in het *oosten van de VS-* locatie ge:

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>IPv4- en IPv6-openbare IP-adressen maken
Om toegang te krijgen tot uw virtuele machines vanaf het internet, hebt u IPv4- en IPv6-openbare IP-adressen nodig voor de load balancer. Openbare IP-adressen maken met [Nieuw-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). In het volgende voorbeeld wordt iPv4- en IPv6-openbaar IP-adres gemaakt met de naam *dsPublicIP_v4* en *dsPublicIP_v6* in de *dsRG1-brongroep:*

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4 `
  -Sku Standard
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6 `
  -Sku Standard
```
Als u toegang wilt krijgen tot uw virtuele machines via een RDP-verbinding, maakt u een IPV4-openbare IP-adressen voor de virtuele machines met [Nieuw-AzPublicIpAddress.](/powershell/module/az.network/new-azpublicipaddress)

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -Sku Standard `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Static `
   -Sku Standard `
   -IpAddressVersion IPv4
```

## <a name="create-standard-load-balancer"></a>Een Standard Load Balancer maken

In deze sectie configureert u dual frontend IP (IPv4 en IPv6) en de back-end address pool voor de load balancer en maakt u vervolgens een Standaard Load Balancer.

### <a name="create-front-end-ip"></a>Maak een front-end IP-adres

Maak een front-end-IP-adres met [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). In het volgende voorbeeld worden IP-configuraties voor IPv4 en IPv6 met de naam *dsLbFrontEnd_v4* en *dsLbFrontEnd_v6:*

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>De back-endadresgroep configureren

Maak een back-end adresgroep met [Nieuw-AzLoadBalancerBackendAddressConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). In de resterende stappen worden de VM's aan deze back-end-groep gekoppeld. In het volgende voorbeeld worden back-endadresgroepen gemaakt met de naam *dsLbBackEndPool_v4* en *dsLbBackEndPool_v6* vm's met zowel IPV4- als IPv6 NIC-configuraties op te nemen:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```
### <a name="create-a-health-probe"></a>Een statustest maken
Gebruik [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) om een statussonde te maken om de status van de VM's te controleren.
```azurepowershell
$probe = New-AzLoadBalancerProbeConfig -Name MyProbe -Protocol tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
```
### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Als u ervoor wilt zorgen dat alleen gezonde VM's verkeer ontvangen, u optioneel een statussonde definiëren. Basic load balancer gebruikt een IPv4-sonde om de status van zowel IPv4- als IPv6-eindpunten op de VM's te beoordelen. Standaard load balancer bevat ondersteuning voor expliciet IPv6 health probes.

Maak een load balancer-regel met [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). In het volgende voorbeeld worden regels voor load balancer met de naam *dsLBrule_v4* en *dsLBrule_v6* en wordt verkeer op *TCP-poort* *80* in evenwicht gebracht met de IP-configuraties van IPv4 en IPv6:

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
   -probe $probe

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
   -probe $probe
```

### <a name="create-load-balancer"></a>Load balancer maken

Maak een standaard load balancer met [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). In het volgende voorbeeld wordt een openbare Standaardload Balancer gemaakt met de naam *myLoadBalancer* met behulp van de IP-configuraties iPv4 en IPv6, backendpools en regels voor taakverdeling die u in de vorige stappen hebt gemaakt:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Standard" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Netwerkbronnen maken
Voordat u een aantal VM's implementeert en uw balancer testen, moet u ondersteunende netwerkbronnen maken - beschikbaarheidsset, netwerkbeveiligingsgroep, virtueel netwerk en virtuele NIC's. 
### <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken
Verbeter de hoge beschikbaarheid van uw app door uw VM's in een beschikbaarheidsset te plaatsen.

Maak een beschikbaarheidsset met [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). In het volgende voorbeeld wordt een beschikbaarheidsset met de naam *myAvailabilitySet* gemaakt:

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroep voor de regels die inkomende en uitgaande communicatie in uw VNET regelen.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Een netwerkbeveiligingsgroepsregel maken voor poort 3389

Maak een netwerkbeveiligingsgroepsregel die RDP-verbindingen via poort 3389 toestaat met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
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

```azurepowershell-interactive
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

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In het volgende voorbeeld wordt een virtueel netwerk met de naam *dsVnet* met *mySubnet aanmaken:*

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>NIC's maken

Virtuele NIC's maken met [New-AzNetworkInterface.](/powershell/module/az.network/new-aznetworkinterface) In het volgende voorbeeld worden twee virtuele NIC's met IPv4- en IPv6-configuraties gemaakt. (Eén virtuele NIC voor elke VM die u in de volgende stappen voor uw app maakt).

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
    
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>Virtuele machines maken

Stel een beheerdersnaam en -wachtwoord voor de VM’s in met [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

Nu kunt u de VM’s maken met [New-AzVM](/powershell/module/az.compute/new-azvm). In het volgende voorbeeld worden twee VM's en de vereiste onderdelen van het virtuele netwerk gemaakt als deze nog niet bestaan. 

```azurepowershell-interactive
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

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>IP-adressen van de IPv4- en IPv6-eindpunten bepalen
Download alle netwerkinterfaceobjecten in de brongroep om de IP's die in deze implementatie worden gebruikt samen te vatten met `get-AzNetworkInterface`. Ontvang ook de frontend-adressen van de IPv4- en IPv6-eindpunten van de Load Balancer met `get-AzpublicIpAddress`.

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
De volgende afbeelding toont een voorbeelduitvoer met de privé-IPv4- en IPv6-adressen van de twee VM's en de IP-adressen van de IPv4- en IPv6-adressen van de Load Balancer.

![IP-samenvatting van de implementatie van Dual Stack(IPv4/IPv6) in Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>IPv6 dual stack virtueel netwerk weergeven in Azure portal
U het virtuele IPv6-dual stack-netwerk in Azure-portal als volgt bekijken:
1. Voer in de zoekbalk van het portaal *dsVnet*in.
2. Wanneer **dsVnet** in de zoekresultaten wordt weergegeven, selecteert u deze. Hiermee wordt de **overzichtspagina** van het virtuele dual stack-netwerk genaamd *dsVnet*gelanceerd. De dual stack virtuele netwerk toont de twee NIC's met zowel IPv4 en IPv6 configuraties gelegen in de dual stack subnet genaamd *dsSubnet*.

  ![IPv6 dual stack virtueel netwerk in Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Het virtuele IPv6 voor Azure-netwerk is beschikbaar in de Azure-portal in alleen-lezen voor deze preview-release.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt.

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Standard Load Balancer gemaakt met een dubbele ip-configuratie (IPv4 en IPv6). U hebt ook een twee virtuele machines gemaakt die NIC's met dubbele IP-configuraties (IPV4 + IPv6) bevatten die zijn toegevoegd aan de back-endpool van de load balancer. Zie [Wat is IPv6 voor Azure Virtual Network voor](ipv6-overview.md) meer informatie over IPv6-ondersteuning in virtuele Azure-netwerken?
