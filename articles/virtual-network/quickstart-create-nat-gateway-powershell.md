---
title: 'Snelstart: een NAT-gateway maken - Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Deze quickstart laat zien hoe u een NAT-gateway maakt met Azure PowerShell
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 1d5f8d6e0b2499bbecd32e7cb3fda2cd2cad4d19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202220"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-powershell"></a>Snelstart: een NAT-gateway maken met Azure PowerShell

In deze snelstart ziet u hoe u de NAT-service van Azure Virtual Network gebruiken. U maakt een NAT-gateway om uitgaande connectiviteit te bieden voor een virtuele machine in Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

U deze zelfstudie voltooien met Azure Cloud Shell of de opdrachten lokaal uitvoeren.  Als u Azure Cloud Shell niet hebt gebruikt, [meldt u zich nu aan.](https://shell.azure.com)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=latest). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam **myResourceGroupNAT** op de locatie **Eastus2** ge:

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

Openbare IP-opties voor NAT-gateway zijn:

* **Openbare IP-adressen**
* **Openbare IP-voorvoegsels**

Beide kunnen worden gebruikt met NAT gateway.

We voegen een openbaar IP-adres en een openbaar IP-voorvoegsel toe aan dit scenario om aan te tonen.

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Om toegang te krijgen tot internet, hebt u een of meer openbare IP-adressen nodig voor de NAT-gateway. Gebruik [Nieuw-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) om een openbare IP-adresbron met de naam **myPublicIP** te maken in **myResourceGroupNAT**. Het resultaat van deze opdracht wordt opgeslagen in een variabele **$publicIP** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voorvoegsel maken

Gebruik [Nieuw-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) om een openbare IP-voorvoegselbron met de naam **myPublicIPprefix** in **myResourceGroupNAT**te maken.  Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$publicIPPrefix** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Een NAT-gatewaybron maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service maken en configureren met behulp van de NAT-gatewaybron:
  - Een openbare IP-groep en een openbaar IP-voorvoegsel die moeten worden gebruikt voor uitgaande stromen die zijn vertaald door de NAT-gatewaybron.
  - Wijzig de niet-actieve time-out van de standaardinstelling van 4 minuten naar 10 minuten.

Maak een wereldwijde Azure [NAT-gateway met Nieuw-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). Het resultaat van deze opdracht maakt een gatewaybron met de naam **myNATgateway** die het openbare IP-adres **myPublicIP** gebruikt en het openbare IP-voorvoegsel **myPublicIPprefix**. De niet-actieve time-out is ingesteld op 10 minuten.  Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$natGateway** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

Op dit punt is de NAT-gateway functioneel en ontbreekt het alleen om te configureren welke subnetten van een virtueel netwerk het moeten gebruiken.

## <a name="configure-virtual-network"></a>Virtueel netwerk configureren

Maak het virtuele netwerk en koppel het subnet aan de gateway.

Maak een virtueel netwerk genaamd **myVnet** met een subnet genaamd **mySubnet** met behulp van [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) in de **myResourceGroup** met behulp van [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). De IP-adresruimte voor het virtuele netwerk is **192.168.0.0/16**. Het subnet binnen het virtuele netwerk is **192.168.0.0/24**.  Het resultaat van de opdrachten wordt opgeslagen in variabelen met de naam **$subnet** en **$vnet** voor later gebruik.

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

Al het uitgaande verkeer naar internetbestemmingen maakt nu gebruik van de NAT-service.  Het is niet nodig om een UDR te configureren.

## <a name="create-a-vm-to-use-the-nat-service"></a>Een VM maken om de NAT-service te gebruiken

We maken nu een VM om de NAT-service te gebruiken.  Deze VM heeft een openbaar IP-adres om te gebruiken als openbaar IP op instantie, zodat u toegang hebt tot de VM.  NAT-service is de stroomrichting bewust en vervangt de standaardinternetbestemming in uw subnet. Het openbare IP-adres van de VM wordt niet gebruikt voor uitgaande verbindingen.

### <a name="create-public-ip-for-source-vm"></a>Openbaar IP maken voor bron-VM

We maken een openbaar IP om toegang te krijgen tot de VM.  Gebruik [Nieuw-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) om een openbare IP-adresbron met de naam **myPublicIPVM** te maken in **myResourceGroupNAT**.  Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$publicIpVM** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Maak een NSG en stel SSH-eindpunt voor VM bloot

Standaard openbare IP-adressen zijn 'standaard veilig', we moeten een NSG maken om inkomende toegang voor ssh mogelijk te maken. Gebruik [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) om een NSG-bron met de naam **myNSG**te maken. Gebruik [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) om een NSG-regel te maken voor SSH-toegang met de naam **ssh** in **myResourceGroupNAT.**  Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$nsg** voor later gebruik.

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>NIC voor VM maken

Maak een netwerkinterface met [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) genaamd **myNic**. Met deze opdracht wordt het IP-adres openbaar en de netwerkbeveiligingsgroep geassocieerd. Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$nic** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>VM maken

#### <a name="create-ssh-key-pair"></a>Een SSH-sleutelpaar maken

U hebt een SSH-sleutelpaar nodig om deze snelstart te volgen. Als u al een SSH-sleutelpaar hebt, kunt u deze stap overslaan.

Gebruik ssh-keygen om een SSH-sleutelpaar te maken.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Zie [SSH-sleutels gebruiken met Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) voor gedetailleerde informatie over het maken van SSH-sleutelparen, waaronder het gebruik van PuTTy.

Als u het SSH-sleutelpaar maakt met de Cloud Shell, wordt het sleutelpaar opgeslagen in een containerafbeelding. Dit [opslagaccount wordt automatisch gemaakt](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Verwijder het opslagaccount of het bestandsaandeel binnen, pas nadat u uw sleutels hebt opgehaald.

#### <a name="create-vm-configuration"></a>VM-configuratie maken

Als u een vm in PowerShell wilt maken, maakt u een configuratie met instellingen voor het gebruik van de afbeelding, de grootte en de verificatieopties. De configuratie wordt gebruikt om de VM te bouwen.

Definieer de SSH-referenties, besturingssysteeminformatie en grootte van de virtuele machine. In dit voorbeeld wordt de SSH-toets opgeslagen in ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combineer de configuratiedefinities om een VM met de naam **myVM** te maken met [Nieuw-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) in **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

Wacht tot de VM is voorbereid om te implementeren en vervolgens verder gaat met de rest van de stappen.

## <a name="discover-the-ip-address-of-the-vm"></a>Ontdek het IP-adres van de VM

Eerst moeten we het IP-adres van de VM die u hebt gemaakt ontdekken. Als u het openbare IP-adres van de VM wilt krijgen, gebruikt u [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>Kopieer het openbare IP-adres en plak het in een notitieblok, zodat u het gebruiken om toegang te krijgen tot de VM.

### <a name="sign-in-to-vm"></a>Aanmelden bij VM

De SSH-referenties moeten vanaf de vorige bewerking in uw Cloud Shell worden opgeslagen.  Open een [Azure Cloud Shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap naar SSH is opgehaald naar de virtuele machine.

```bash
ssh azureuser@<ip-address-destination>
```

U bent nu klaar om de NAT-service te gebruiken.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, u de opdracht [Verwijderen-AzResourceGroep](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) gebruiken om de resourcegroep en alle bronnen binnen te verwijderen.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een NAT-gateway en een VM gemaakt om deze te gebruiken. 

Bekijk de statistieken in Azure Monitor om uw NAT-service te bekijken. Diagnose problemen zoals resource uitputting van de beschikbare SNAT-poorten.  Uitputting van resources van SNAT-poorten wordt aangepakt door extra openbare IP-adresbronnen of openbare IP-voorvoegselbronnen of beide toe te voegen.


- Meer informatie over [Azure Virtual Network NAT](./nat-overview.md)
- Meer informatie over [NAT-gatewaybron](./nat-gateway-resource.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure-portal.](./quickstart-create-nat-gateway-portal.md)
> [!div class="nextstepaction"]


