---
title: 'Quickstart: Een openbare load balancer maken - Azure PowerShell'
titleSuffix: Azure Load Balancer
description: In deze quickstart wordt beschreven hoe u een load balancer maakt met behulp PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: b8a95687b1567eb6e063ccc871a4a130c5f2db69
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290348"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Quickstart: Een openbare load balancer maken voor het uitvoeren van de taakverdeling voor VM's met behulp van Azure PowerShell

Ga aan de slag met Azure Load Balancer via Azure PowerShell om een openbare load balancer en drie virtuele machines te maken.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell lokaal geïnstalleerd of Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor dit artikel versie 5.4.1 of hoger van de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

* Met de naam **myResourceGroupLB**.
* Op de locatie **eastus**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="option-1-default-create-a-load-balancer-standard-sku"></a>[Optie 1 (standaard): Een load balancer maken (Standard SKU)](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Voor productieworkloads wordt de load balancer uit de Standard SKU aanbevolen. Zie **[Azure Load Balancer-SKU's](skus.md)** voor meer informatie over SKU's.

## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Om toegang te krijgen tot uw web-app op internet, hebt u een openbaar IP-adres nodig voor de load balancer. 

Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) om:

* Een standaard zoneredundant openbaar IP-adres met de naam **myPublicIP** te maken.
* In **myResourceGroupLB**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

Als u een zonegebonden openbaar IP-adres in zone 1 wilt maken, voert u de volgende opdracht uit:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku -zone 1
```

## <a name="create-standard-load-balancer"></a>Een standaard load balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:

  * Een front-end-IP-pool die het binnenkomende netwerkverkeer op de load balancer ontvangt.
  * Een back-end-IP-pools waar de front-endpool het netwerkverkeer naartoe stuurt dat door de load balancer is verdeeld.
  * Een statustest die de status van de back-end-VM-instanties vaststelt.
  * Een load balancer-regel die bepaalt hoe het verkeer over de VM's wordt verdeeld.

### <a name="create-frontend-ip"></a>Front-end-IP maken

Maak een front-end-IP-adres met [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Met de naam **myFrontEnd**.
* Gekoppeld aan het openbare IP-adres **myPublicIP**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'

$publicIp = 
Get-AzPublicIpAddress -Name $pubIP -ResourceGroupName $rg

$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>De back-endadresgroep configureren

Maak een back-endadresgroep met [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* Met de naam **myBackEndPool**.
* In de resterende stappen worden de VM's aan deze back-end-groep gekoppeld.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Statustest maken

Met een statustest worden alle VM-instanties gecontroleerd om na te gaan of ze netwerkverkeer kunnen verzenden. 

Een virtuele machine met een mislukte test wordt verwijderd uit de load balancer. De virtuele machine wordt weer toegevoegd aan de load balancer wanneer de fout is opgelost.

Voer een statustest uit met [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* Controleert de status van de virtuele machines.
* Met de naam **myHealthProbe**.
* Protocol: **TCP**.
* Controleert **poort 80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Load balancer-regel maken

Met een load balancer-regel wordt het volgende gedefinieerd:

* De front-end-IP-configuratie voor het binnenkomende verkeer.
* De back-end-IP-adresgroep voor het ontvangen van verkeer.
* De vereiste bron- en doelpoort. 

Maak een load balancer-regel met [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Met de naam **myHTTPRule**
* Luistert aan **poort 80** in de front-endpool **myFrontEnd**.
* Verzendt netwerkverkeer volgens taakverdeling naar de back-endadresgroep **myBackEndPool** via **poort 80**. 
* Gebruikt statustest **myHealthProbe**.
* Protocol: **TCP**.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT
```

### <a name="create-load-balancer-resource"></a>Resource voor load balancer maken

Maak een openbare load balancer met [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Met de naam **myLoadBalancer**
* In **eastus**.
* In resourcegroep **myResourceGroupLB**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

## <a name="configure-virtual-network"></a>Virtueel netwerk configureren

Voordat u VM's implementeert en uw load balancer test, moet u de ondersteunende virtuele-netwerkresources maken.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* Met de naam **myVNet**.
* In resourcegroep **myResourceGroupLB**.
* Subnet met de naam **myBackendSubnet**.
* Virtueel netwerk **10.0.0.0/16**.
* Subnet **10.0.0.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'

## Create subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig
```
### <a name="create-public-ip-addresses-for-the-vms"></a>Openbare IP-adressen maken voor de VM's

Als u toegang wilt hebben tot uw VM's via een RDP-verbinding, moet u openbare IP-adressen gebruiken voor de VM's. 

Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) om standaard openbare IP-adressen te maken voor:

#### <a name="vm1"></a>VM1

* Met de naam **myVMPubIP1**.
* In resourcegroep **myResourceGroupLB**.
* Op de locatie **eastus**.
* **Standaard** SKU.
* **Statische** toewijzing voor het IP-adres.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ip1 = 'myVMPubIP1'
$sku = 'Standard'
$all = 'static'

$RdpPubIP1 = 
New-AzPublicIpAddress -Name $ip1 -ResourceGroupName $rg -Location $loc -SKU $sku -AllocationMethod $all
```

#### <a name="vm2"></a>VM2

* Met de naam **myVMPubIP2**.
* In resourcegroep **myResourceGroupLB**.
* Op de locatie **eastus**.
* **Standaard** SKU.
* **Statische** toewijzing voor het IP-adres. 

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ip2 = 'myVMPubIP2'
$sku = 'Standard'
$all = 'static'

$RdpPubIP2 = 
New-AzPublicIpAddress -Name $ip2 -ResourceGroupName $rg -Location $loc -SKU $sku -AllocationMethod $all
```

#### <a name="vm3"></a>VM3

* Met de naam **myVMPubIP3**.
* In resourcegroep **myResourceGroupLB**.
* Op de locatie **eastus**.
* **Standaard** SKU.
* **Statische** toewijzing voor het IP-adres. 

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ip3 = 'myVMPubIP3'
$sku = 'Standard'
$all = 'static'

$RdpPubIP3 = 
New-AzPublicIpAddress -Name $ip3 -ResourceGroupName $rg -Location $loc -SKU $sku -AllocationMethod $all
```

### <a name="create-network-security-group"></a>Netwerkbeveiligingsgroep maken
Maak een netwerkbeveiligingsgroep om binnenkomende verbindingen met uw virtuele netwerk te definiëren.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Een netwerkbeveiligingsgroepsregel maken voor poort 3389

Maak een netwerkbeveiligingsgroep met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Met de naam **myNSGRuleRDP**.
* Beschrijving: **RDP toestaan**.
* Toegang: **Toestaan**.
* Protocol: **TCP**.
* Richting: **Inkomend**.
* Prioriteit: **1000**.
* Bron: **internet**.
* Bronpoortbereik: **(*)** .
* Doeladresvoorvoegsel: **(*)** .
* Doel: **poort 3389**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleRDP'
$des = 'Allow RDP'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '1000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '3389'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Een netwerkbeveiligingsgroepsregel maken voor poort 80
Maak een netwerkbeveiligingsgroepregel met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Met de naam **myNSGRuleHTTP**.
* Beschrijving: **HTTP toestaan**.
* Toegang: **Toestaan**.
* Protocol: **TCP**.
* Richting: **Inkomend**.
* Prioriteit: **2000**.
* Bron: **internet**.
* Bronpoortbereik: **(*)** .
* Doeladresvoorvoegsel: **(*)** .
* Doel **poort 80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule2 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroep met [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* Met de naam **myNSG**.
* In resourcegroep **myResourceGroupLB**.
* Op locatie **eastus**.
* Met beveiligingsregels die in de vorige stappen zijn gemaakt en zijn opgeslagen in een variabele.

```azurepowershell
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1,$rule2
```

### <a name="create-network-interfaces"></a>Netwerkinterfaces maken

Maak drie netwerkinterfaces met [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>VM 1

* Met de naam **myNicVM1**.
* In resourcegroep **myResourceGroupLB**.
* Op locatie **eastus**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.
* Gekoppeld aan load balancer **myLoadBalancer** in **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$ip1 = 'myVMPubIP1'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get public ip address for VM1 ##
$pub1 = 
Get-AzPublicIPAddress -Name $ip1 -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -PublicIpAddress $pub1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Met de naam **myNicVM2**.
* In resourcegroep **myResourceGroupLB**.
* Op locatie **eastus**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.
* Gekoppeld aan load balancer **myLoadBalancer** in **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$ip2 = 'myVMPubIP2'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get public ip address for VM2 ##
$pub2 = 
Get-AzPublicIPAddress -Name $ip2 -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -PublicIpAddress $pub2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-3"></a>VM 3

* Met de naam **myNicVM3**.
* In resourcegroep **myResourceGroupLB**.
* Op locatie **eastus**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.
* Gekoppeld aan load balancer **myLoadBalancer** in **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic3 = 'myNicVM3'
$vnt = 'myVNet'
$ip3 = 'myVMPubIP3'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get public ip address for VM3 ##
$pub3 = 
Get-AzPublicIPAddress -Name $ip3 -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM3 ##
$nicVM3 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic3 -PublicIpAddress $pub3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```
### <a name="create-virtual-machines"></a>Virtuele machines maken

Stel een beheerdersnaam en -wachtwoord voor de VM’s in met [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
$cred = Get-Credential
```

Maak de virtuele machines met:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Met de naam **myVM1**.
* In resourcegroep **myResourceGroupLB**.
* Gekoppeld aan de netwerkinterface **myNicVM1**.
* Gekoppeld aan load balancer **myLoadBalancer**.
* In **Zone 1**.
* Op de locatie **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>VM2

* Met de naam **myVM2**.
* In resourcegroep **myResourceGroupLB**.
* Gekoppeld aan de netwerkinterface **myNicVM2**.
* Gekoppeld aan load balancer **myLoadBalancer**.
* In **Zone 2**.
* Op de locatie **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

#### <a name="vm3"></a>VM3

* Met de naam **myVM3**.
* In resourcegroep **myResourceGroupLB**.
* Gekoppeld aan de netwerkinterface **myNicVM3**.
* Gekoppeld aan load balancer **myLoadBalancer**.
* In **Zone 3**.
* Op de locatie **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM3'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '3'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM3 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM3.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

Het duurt enkele minuten voordat de drie VM's zijn gemaakt en geconfigureerd.

## <a name="create-outbound-rule-configuration"></a>Configuratie voor uitgaande regel maken
Met regels voor uitgaand verkeer voor de load balancer wordt SNAT voor VM's in de back-endpool geconfigureerd. 

Zie [Uitgaande verbindingen in Azure](load-balancer-outbound-connections.md) voor meer informatie over uitgaande verbindingen.

### <a name="create-outbound-public-ip-address"></a>Een openbaar IP-adres voor uitgaand verkeer maken

Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) om:

* Een standaard zoneredundant openbaar IP-adres met de naam **myPublicIPOutbound** maken.
* In **myResourceGroupLB**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIPOutbound'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

Als u een zonegebonden openbaar IP-adres in zone 1 wilt maken, voert u de volgende opdracht uit:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIPOutbound'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku -zone 1
```
### <a name="create-outbound-frontend-ip-configuration"></a>Uitgaande front-end-IP-configuratie maken

Maak een nieuwe front-end-IP-configuratie met [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/add-azloadbalancerfrontendipconfig):

* Met de naam **myFrontEndOutbound**.
* Gekoppeld aan openbaar IP-adres **myPublicIPOutbound**.

```azurepowershell-interactive
## Variables for the command ##
$fen = 'myFrontEndOutbound'

## Get the load balancer configuration  and apply the frontend config##
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg | Add-AzLoadBalancerFrontendIPConfig -Name $fen -PublicIpAddress $publicIP | Set-AzLoadBalancer
```

### <a name="create-outbound-pool"></a>Uitgaande pool maken

Maak een nieuwe uitgaande pool met [Add-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/add-azloadbalancerbackendaddresspoolconfig). 

Pas de pool en het front-end-IP-adres toe op de load balancer met [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

* Met de naam **myBackEndPoolOutbound**.

```azurepowershell-interactive
## Variables for the command ##
$ben = 'myBackEndPoolOutbound'
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'

## Get the load balancer configuration and create the outbound backend address pool##
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg | Add-AzLoadBalancerBackendAddressPoolConfig -Name $ben | Set-AzLoadBalancer
```
### <a name="create-outbound-rule-and-apply-to-load-balancer"></a>Uitgaande regel maken en toepassen op de load balancer

Maak een nieuwe regel voor uitgaand verkeer voor de uitgaande back-endool met [Add-AzLoadBalancerOutboundRuleConfig](/powershell/module/az.network/new-azloadbalanceroutboundruleconfig). 

Pas de regel toe op de load balancer met [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

* Met de naam **myOutboundRule**.
* Gekoppeld aan load balancer **myLoadBalancer**.
* Gekoppeld aan front-end **myFrontEndOutbound**.
* Protocol: **Alle**.
* Time-out voor inactiviteit: **15**.
* **10.000** uitgaande poorten.
* Gekoppeld aan back-endpool **myBackEndPoolOutbound**.
* In resourcegroep **myResourceGroupLB**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$brn = 'myOutboundRule'
$pro = 'All'
$idl = '15'
$por = '10000'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg 

## Apply the outbound rule configuration to the load balancer. ##
$lb | Add-AzLoadBalancerOutBoundRuleConfig -Name $brn -FrontendIPConfiguration $lb.FrontendIpConfigurations[1] -BackendAddressPool $lb.BackendAddressPools[1] -Protocol $pro -IdleTimeoutInMinutes $idl -AllocatedOutboundPort $por | Set-AzLoadBalancer
```

### <a name="add-virtual-machines-to-outbound-pool"></a>Virtuele machines toevoegen aan een uitgaande pool

Voeg de netwerkinterfaces van de virtuele machines toe aan de uitgaande pool van de load balancer met [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig):


#### <a name="vm1"></a>VM1
* In back-endadresgroep **myBackEndPoolOutbound**.
* In resourcegroep **myResourceGroupLB**.
* Gekoppeld aan de netwerkinterface **myNicVM1** en **ipconfig1**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic1 = 'myNicVM1'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic1 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[1].id | Set-AzNetworkInterface
```

#### <a name="vm2"></a>VM2
* In back-endadresgroep **myBackEndPoolOutbound**.
* In resourcegroep **myResourceGroupLB**.
* Gekoppeld aan de netwerkinterface **myNicVM2** en **ipconfig1**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic2 = 'myNicVM2'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic2 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[1].id | Set-AzNetworkInterface
```

#### <a name="vm3"></a>VM3
* In back-endadresgroep **myBackEndPoolOutbound**.
* In resourcegroep **myResourceGroupLB**.
* Gekoppeld aan de netwerkinterface **myNicVM3** en **ipconfig1**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic3 = 'myNicVM3'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic3 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[1].id | Set-AzNetworkInterface

```

# <a name="option-2-create-a-load-balancer-basic-sku"></a>[Optie 2: Een load balancer (Basic SKU) maken](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Voor productieworkloads wordt de load balancer uit de Standard SKU aanbevolen. Zie **[Azure Load Balancer-SKU's](skus.md)** voor meer informatie over SKU's.

## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Om toegang te krijgen tot uw web-app op internet, hebt u een openbaar IP-adres nodig voor de load balancer. 

Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) om:

* Een standaard zoneredundant openbaar IP-adres met de naam **myPublicIP** te maken.
* In **myResourceGroupLB**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Basic'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

## <a name="create-basic-load-balancer"></a>Een eenvoudige load balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:

  * Een front-end-IP-pool die het binnenkomende netwerkverkeer op de load balancer ontvangt.
  * Een back-end-IP-pools waar de front-endpool het netwerkverkeer naartoe stuurt dat door de load balancer is verdeeld.
  * Een statustest die de status van de back-end-VM-instanties vaststelt.
  * Een load balancer-regel die bepaalt hoe het verkeer over de VM's wordt verdeeld.

### <a name="create-frontend-ip"></a>Front-end-IP maken

Maak een front-end-IP-adres met [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Met de naam **myFrontEnd**.
* Gekoppeld aan het openbare IP-adres **myPublicIP**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'

$publicIp = 
Get-AzPublicIpAddress -Name $pubIP -ResourceGroupName $rg

$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>De back-endadresgroep configureren

Maak een back-endadresgroep met [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* Met de naam **myBackEndPool**.
* In de resterende stappen worden de VM's aan deze back-end-groep gekoppeld.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Statustest maken

Met een statustest worden alle VM-instanties gecontroleerd om na te gaan of ze netwerkverkeer kunnen verzenden. 

Een virtuele machine met een mislukte test wordt verwijderd uit de load balancer. De virtuele machine wordt weer toegevoegd aan de load balancer wanneer de fout is opgelost.

Voer een statustest uit met [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* Controleert de status van de virtuele machines.
* Met de naam **myHealthProbe**.
* Protocol: **TCP**.
* Controleert **poort 80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Load balancer-regel maken

Met een load balancer-regel wordt het volgende gedefinieerd:

* De front-end-IP-configuratie voor het binnenkomende verkeer.
* De back-end-IP-adresgroep voor het ontvangen van verkeer.
* De vereiste bron- en doelpoort. 

Maak een load balancer-regel met [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Met de naam **myHTTPRule**
* Luistert aan **poort 80** in de front-endpool **myFrontEnd**.
* Verzendt netwerkverkeer volgens taakverdeling naar de back-endadresgroep **myBackEndPool** via **poort 80**. 
* Gebruikt statustest **myHealthProbe**.
* Protocol: **TCP**.
* Schakel SNAT (Source Network Address Translation) voor uitgaand verkeer in met behulp van het front-end-IP-adres.


```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool
```

### <a name="create-load-balancer-resource"></a>Resource voor load balancer maken

Maak een openbare load balancer met [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Met de naam **myLoadBalancer**
* In **eastus**.
* In resourcegroep **myResourceGroupLB**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

## <a name="configure-virtual-network"></a>Virtueel netwerk configureren

Voordat u VM's implementeert en uw load balancer test, moet u de ondersteunende virtuele-netwerkresources maken.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* Met de naam **myVNet**.
* In resourcegroep **myResourceGroupLB**.
* Subnet met de naam **myBackendSubnet**.
* Virtueel netwerk **10.0.0.0/16**.
* Subnet **10.0.0.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'

## Create subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig
```
### <a name="create-public-ip-addresses-for-the-vms"></a>Openbare IP-adressen maken voor de VM's

Als u toegang wilt hebben tot uw VM's via een RDP-verbinding, moet u openbare IP-adressen gebruiken voor de VM's. 

Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) om standaard openbare IP-adressen te maken voor:

#### <a name="vm1"></a>VM1

* Met de naam **myVMPubIP1**.
* In resourcegroep **myResourceGroupLB**.
* Op de locatie **eastus**.
* **Standaard** SKU.
* **Statische** toewijzing voor het IP-adres.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ip1 = 'myVMPubIP1'
$sku = 'Basic'
$all = 'static'

$RdpPubIP1 = 
New-AzPublicIpAddress -Name $ip1 -ResourceGroupName $rg -Location $loc -SKU $sku -AllocationMethod $all
```

#### <a name="vm2"></a>VM2

* Met de naam **myVMPubIP2**.
* In resourcegroep **myResourceGroupLB**.
* Op de locatie **eastus**.
* **Standaard** SKU.
* **Statische** toewijzing voor het IP-adres. 

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ip2 = 'myVMPubIP2'
$sku = 'Basic'
$all = 'static'

$RdpPubIP2 = 
New-AzPublicIpAddress -Name $ip2 -ResourceGroupName $rg -Location $loc -SKU $sku -AllocationMethod $all
```

#### <a name="vm3"></a>VM3

* Met de naam **myVMPubIP3**.
* In resourcegroep **myResourceGroupLB**.
* Op de locatie **eastus**.
* **Standaard** SKU.
* **Statische** toewijzing voor het IP-adres. 

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ip3 = 'myVMPubIP3'
$sku = 'Basic'
$all = 'static'

$RdpPubIP2 = 
New-AzPublicIpAddress -Name $ip3 -ResourceGroupName $rg -Location $loc -SKU $sku -AllocationMethod $all
```

### <a name="create-network-security-group"></a>Netwerkbeveiligingsgroep maken
Maak een netwerkbeveiligingsgroep om binnenkomende verbindingen met uw virtuele netwerk te definiëren.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Een netwerkbeveiligingsgroepsregel maken voor poort 3389

Maak een netwerkbeveiligingsgroep met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Met de naam **myNSGRuleRDP**.
* Beschrijving: **RDP toestaan**.
* Toegang: **Toestaan**.
* Protocol: **TCP**.
* Richting: **Inkomend**.
* Prioriteit: **1000**.
* Bron: **internet**.
* Bronpoortbereik: **(*)** .
* Doeladresvoorvoegsel: **(*)** .
* Doel: **poort 3389**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleRDP'
$des = 'Allow RDP'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '1000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '3389'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Een netwerkbeveiligingsgroepsregel maken voor poort 80
Maak een netwerkbeveiligingsgroepregel met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Met de naam **myNSGRuleHTTP**.
* Beschrijving: **HTTP toestaan**.
* Toegang: **Toestaan**.
* Protocol: **TCP**.
* Richting: **Inkomend**.
* Prioriteit: **2000**.
* Bron: **internet**.
* Bronpoortbereik: **(*)** .
* Doeladresvoorvoegsel: **(*)** .
* Doel **poort 80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule2 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroep met [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* Met de naam **myNSG**.
* In resourcegroep **myResourceGroupLB**.
* Op locatie **eastus**.
* Met beveiligingsregels die in de vorige stappen zijn gemaakt en zijn opgeslagen in een variabele.

```azurepowershell
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1,$rule2
```

### <a name="create-network-interfaces"></a>Netwerkinterfaces maken

Maak drie netwerkinterfaces met [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>VM 1

* Met de naam **myNicVM1**.
* In resourcegroep **myResourceGroupLB**.
* Op locatie **eastus**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.
* Gekoppeld aan load balancer **myLoadBalancer** in **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$ip1 = 'myVMPubIP1'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get public ip address for VM1 ##
$pub1 = 
Get-AzPublicIPAddress -Name $ip1 -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -PublicIpAddress $pub1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Met de naam **myNicVM2**.
* In resourcegroep **myResourceGroupLB**.
* Op locatie **eastus**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.
* Gekoppeld aan load balancer **myLoadBalancer** in **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$ip2 = 'myVMPubIP2'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get public ip address for VM2 ##
$pub2 = 
Get-AzPublicIPAddress -Name $ip2 -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -PublicIpAddress $pub2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-3"></a>VM 3

* Met de naam **myNicVM3**.
* In resourcegroep **myResourceGroupLB**.
* Op locatie **eastus**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.
* Gekoppeld aan load balancer **myLoadBalancer** in **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic3 = 'myNicVM3'
$vnt = 'myVNet'
$ip3 = 'myVMPubIP3'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get public ip address for VM3 ##
$pub3 = 
Get-AzPublicIPAddress -Name $ip3 -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM3 ##
$nicVM3 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic3 -PublicIpAddress $pub3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>Een beschikbaarheidsset voor virtuele machines maken

Gebruik [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) om een beschikbaarheidsset voor de virtuele machines te maken:

* Met de naam **myAvSet**.
* In resourcegroep **myResourceGroupLB**.
* Op de locatie **eastus**.

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'myResourceGroupLB'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>Virtuele machines maken

Stel een beheerdersnaam en -wachtwoord voor de VM’s in met [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
$cred = Get-Credential
```

Maak de virtuele machines met:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Met de naam **myVM1**.
* In resourcegroep **myResourceGroupLB**.
* Gekoppeld aan de netwerkinterface **myNicVM1**.
* Gekoppeld aan load balancer **myLoadBalancer**.
* Op de locatie **eastus**.
* In de beschikbaarheidsset **myAvSet**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>VM2

* Met de naam **myVM2**.
* In resourcegroep **myResourceGroupLB**.
* Gekoppeld aan de netwerkinterface **myNicVM2**.
* Gekoppeld aan load balancer **myLoadBalancer**.
* Op de locatie **eastus**.
* In de beschikbaarheidsset **myAvSet**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

#### <a name="vm3"></a>VM3

* Met de naam **myVM3**.
* In resourcegroep **myResourceGroupLB**.
* Gekoppeld aan de netwerkinterface **myNicVM3**.
* Gekoppeld aan load balancer **myLoadBalancer**.
* Op de locatie **eastus**.
* In de beschikbaarheidsset **myAvSet**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM3'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM3 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM3.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

Het duurt enkele minuten voordat de drie VM's zijn gemaakt en geconfigureerd.

---

### <a name="install-iis-with-a-custom-web-page"></a>IIS installeren met een aangepaste webpagina

Installeer IIS als volgt met een aangepaste webpagina op beide back-end-VM's:

1. Gebruik [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) om de openbare IP-adressen van de drie VM's op te halen.

   ```azurepowershell-interactive
   ## Variables for commands. ##
   $rg = 'myResourceGroupLB'
   $ip1 = 'myVMPubIP1'
   $ip2 = 'myVMPubIP2'
   $ip3 = 'myVMPubIP3'

   ## VM1 ##
   (Get-AzPublicIPAddress -ResourceGroupName $rg -Name $ip1).IpAddress

   ## VM2 ##
   (Get-AzPublicIPAddress -ResourceGroupName $rg -Name $ip2).IpAddress

   ## VM3 ##
   (Get-AzPublicIPAddress -ResourceGroupName $rg -Name $ip3).IpAddress
   ```
   
2. Maak Extern-bureaubladverbindingen bij **myVM1**, **myVM2** en **myVM3** met behulp van de openbare IP-adressen van de virtuele machines.

3. Voer de referenties in voor elke VM om de RDP-sessie te starten.

4. Start Windows PowerShell op elke VM en gebruik de volgende opdrachten om de IIS-server te installeren en het standaard HTM-bestand bij te werken.

   ```powershell
   # Install IIS
   Install-WindowsFeature -name Web-Server -IncludeManagementTools

   # Remove default htm file
   remove-item  C:\inetpub\wwwroot\iisstart.htm
    
   #Add custom htm file
   Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from host " + $env:computername)
   ```

5. Sluit de RDP-verbindingen bij **myVM1**, **myVM2** en **myVM3**.


## <a name="test-the-load-balancer"></a>Load balancer testen
Gebruik [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) om het openbare IP-adres van de load balancer op te halen:

* Met de naam **MyPublicIP**
* In resourcegroep **myResourceGroupLB**.

```azurepowershell-interactive
## Variables for command. ##
$rg = 'myResourceGroupLB'
$ipn = 'myPublicIP'

Get-AzPublicIPAddress -ResourceGroupName $rg -Name $ipn | select IpAddress
```

Kopieer het openbare IP-adres en plak het in de adresbalk van de browser.

![Load balancer testen](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de load balancer en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt. 

```azurepowershell-interactive
## Variable for command. ##
$rg = 'myResourceGroupLB'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u:

* Een standaard of eenvoudige openbare load balancer gemaakt
* Virtuele machines gekoppeld. 
* De load balancer-verkeersregel en de statustest geconfigureerd.
* De load balancer getest.

Zie [Wat is Azure Load Balancer?](load-balancer-overview.md) en de [veelgestelde vragen over Load Balancer](load-balancer-faqs.md) voor meer informatie over Azure Load Balancer.

Meer informatie over [Load Balancer en beschikbaarheidszones](load-balancer-standard-availability-zones.md).
