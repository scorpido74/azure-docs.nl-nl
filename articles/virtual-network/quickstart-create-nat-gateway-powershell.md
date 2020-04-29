---
title: 'Snelstartgids: een NAT-gateway maken-Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Deze Quick Start laat zien hoe u een NAT-gateway maakt met behulp van Azure PowerShell
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79202220"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-powershell"></a>Snelstartgids: een NAT-gateway maken met behulp van Azure PowerShell

In deze Quick start ziet u hoe u de NAT-service van Azure Virtual Network gebruikt. U maakt een NAT-gateway om uitgaande connectiviteit te bieden voor een virtuele machine in Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

U kunt deze zelf studie volt ooien met behulp van Azure Cloud Shell of de opdrachten lokaal uitvoeren.  Als u Azure Cloud Shell nog niet hebt gebruikt, [meldt u zich nu](https://shell.azure.com)aan.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=latest). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voor beeld wordt een resource groep met de naam **myResourceGroupNAT** gemaakt op de locatie **eastus2** :

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

Open bare IP-opties voor de NAT-gateway zijn:

* **Openbare IP-adressen**
* **Voor voegsels voor open bare IP**

Beide kunnen worden gebruikt met NAT-gateway.

We voegen een openbaar IP-adres en een openbaar IP-voor voegsel toe aan dit scenario om te demonstreren.

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Voor toegang tot internet hebt u een of meer open bare IP-adressen nodig voor de NAT-gateway. Gebruik [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) voor het maken van een open bare IP-adres resource met de naam **myPublicIP** in **myResourceGroupNAT**. Het resultaat van deze opdracht wordt opgeslagen in een variabele **$publicIP** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voor voegsel maken

Gebruik [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) voor het maken van een open bare IP-voor voegsel resource met de naam **myPublicIPprefix** in **myResourceGroupNAT**.  Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$publicIPPrefix** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Een NAT-gateway resource maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service kunt maken en configureren met behulp van de NAT-gateway resource:
  - Een open bare IP-adres groep en een openbaar IP-voor voegsel dat moet worden gebruikt voor uitgaande stromen die worden vertaald door de NAT-gateway resource.
  - Wijzig de time-out voor inactiviteit van de standaard waarde van 4 minuten in 10 minuten.

Maak een globale Azure NAT-gateway met [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). Met deze opdracht maakt u een gateway resource met de naam **myNATgateway** die gebruikmaakt van het open bare IP-adres **myPublicIP** en het open bare IP-voor voegsel **myPublicIPprefix**. De time-out voor inactiviteit is ingesteld op 10 minuten.  Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$natGateway** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

Op dit punt is de NAT-gateway functioneel en ontbreekt er een configuratie van de subnetten van een virtueel netwerk.

## <a name="configure-virtual-network"></a>Virtueel netwerk configureren

Maak het virtuele netwerk en koppel het subnet aan de gateway.

Maak een virtueel netwerk met de naam **myVnet** met een subnet met de naam **mySubnet** met behulp van [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) in de **MyResourceGroup** met behulp van [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). De IP-adres ruimte voor het virtuele netwerk is **192.168.0.0/16**. Het subnet in het virtuele netwerk is **192.168.0.0/24**.  Het resultaat van de opdrachten wordt opgeslagen in variabelen met de naam **$subnet** en **$vnet** voor later gebruik.

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

Al het uitgaande verkeer naar Internet bestemmingen maakt nu gebruik van de NAT-service.  Het is niet nodig om een UDR te configureren.

## <a name="create-a-vm-to-use-the-nat-service"></a>Een virtuele machine maken voor het gebruik van de NAT-service

We gaan nu een VM maken voor het gebruik van de NAT-service.  Deze VM heeft een openbaar IP-adres dat kan worden gebruikt als een openbaar IP-adres op exemplaar niveau om toegang te krijgen tot de virtuele machine.  De NAT-service is stroom richting, en vervangt de standaard Internet bestemming in uw subnet. Het open bare IP-adres van de virtuele machine wordt niet gebruikt voor uitgaande verbindingen.

### <a name="create-public-ip-for-source-vm"></a>Een openbaar IP-adres voor de bron-VM maken

We maken een openbaar IP-adres dat wordt gebruikt voor toegang tot de virtuele machine.  Gebruik [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) voor het maken van een open bare IP-adres resource met de naam **myPublicIPVM** in **myResourceGroupNAT**.  Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$publicIpVM** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Een NSG maken en het SSH-eind punt voor de virtuele machine weer geven

Standaard open bare IP-adressen zijn standaard beveiligd. we moeten een NSG maken om inkomende toegang voor SSH toe te staan. Gebruik [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) om een NSG-resource te maken met de naam **mijnnbg**. Gebruik [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) voor het maken van een NSG-regel voor SSH-toegang met de naam **SSH** in **myResourceGroupNAT**.  Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$NSG** voor later gebruik.

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

Maak een netwerk interface met [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) met de naam **myNic**. Met deze opdracht worden het open bare IP-adres en de netwerk beveiligings groep gekoppeld. Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$NIC** voor later gebruik.

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

Ssh-keygen gebruiken om een SSH-sleutel paar te maken.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Zie [SSH-sleutels gebruiken met Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) voor gedetailleerde informatie over het maken van SSH-sleutelparen, waaronder het gebruik van PuTTy.

Als u het SSH-sleutel paar maakt met behulp van de Cloud Shell, wordt het sleutel paar opgeslagen in een container installatie kopie. Dit [opslag account wordt automatisch gemaakt](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Verwijder het opslag account of de bestands share binnen pas nadat u de sleutels hebt opgehaald.

#### <a name="create-vm-configuration"></a>VM-configuratie maken

Als u een virtuele machine in Power shell wilt maken, maakt u een configuratie met instellingen voor de installatie kopie voor gebruik, grootte en verificatie opties. De configuratie wordt gebruikt om de virtuele machine te bouwen.

Definieer de SSH-referenties, besturingssysteeminformatie en grootte van de virtuele machine. In dit voor beeld wordt de SSH-sleutel opgeslagen in ~/.ssh/id_rsa. pub.

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
Combi neer de configuratie definities voor het maken van een virtuele machine met de naam **myVM** met [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) in **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

Wacht totdat de virtuele machine gereed is voor de implementatie en ga door met de overige stappen.

## <a name="discover-the-ip-address-of-the-vm"></a>Het IP-adres van de virtuele machine detecteren

Eerst moet het IP-adres worden gedetecteerd van de virtuele machine die u hebt gemaakt. Gebruik [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest)om het open bare IP-adres van de virtuele machine op te halen. 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>Kopieer het open bare IP-adres en plak het in een Klad blok, zodat u het kunt gebruiken om toegang te krijgen tot de virtuele machine.

### <a name="sign-in-to-vm"></a>Aanmelden bij de VM

De SSH-referenties moeten worden opgeslagen in uw Cloud Shell van de vorige bewerking.  Open een [Azure Cloud shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine te gaan.

```bash
ssh azureuser@<ip-address-destination>
```

U bent nu klaar om de NAT-service te gebruiken.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) gebruiken om de resource groep en alle resources in te verwijderen wanneer u deze niet meer nodig hebt.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een NAT-gateway en een virtuele machine gemaakt om deze te gebruiken. 

Controleer de metrische gegevens in Azure Monitor om uw NAT-service weer te geven. Problemen vaststellen, zoals de bron uitputting van de beschik bare SNAT-poorten.  Bron uitputting van de SNAT-poorten wordt opgelost door extra open bare IP-adres bronnen of open bare IP-prefix bronnen of beide toe te voegen.


- Meer informatie over [Azure Virtual Network NAT](./nat-overview.md)
- Meer informatie over de [NAT gateway-resource](./nat-gateway-resource.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure cli](./quickstart-create-nat-gateway-cli.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure Portal](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]


