---
title: 'Zelfstudie: Een NAT-gateway maken en testen - Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: In deze zelfstudie ziet u hoe u een NAT-gateway maakt met de Azure PowerShell en de NAT-service test
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 61cda5e61d14c4eeaf2d88483603707598b1c911
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202221"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Zelfstudie: Een NAT-gateway maken met Azure PowerShell en de NAT-service testen

In deze zelfstudie maakt u een NAT-gateway om uitgaande connectiviteit te bieden voor virtuele machines in Azure. Als u de NAT-gateway wilt testen, implementeert u een virtuele bron- en doelmachine. U test de NAT-gateway door uitgaande verbindingen te maken met een openbaar IP-adres. Deze verbindingen komen van de bron naar de virtuele bestemming machine. Deze zelfstudie implementeert bron en bestemming in twee verschillende virtuele netwerken in dezelfde brongroep voor eenvoud.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

U deze zelfstudie voltooien met Azure Cloud Shell of de desbetreffende opdrachten lokaal uitvoeren.  Als u Azure Cloud Shell nog nooit hebt gebruikt, moet u [zich nu aanmelden.](https://shell.azure.com)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep maken met [az group create](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam **myResourceGroupNAT** op de locatie **Eastus2** ge:


```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Om toegang te krijgen tot internet, hebt u een of meer openbare IP-adressen nodig voor de NAT-gateway. Gebruik [Nieuw-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) om een openbare IP-adresbron met de naam **myPublicIPsource** te maken in **myResourceGroupNAT**. Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$publicIPsource** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pips = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pips -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voorvoegsel maken

 Gebruik [Nieuw-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) om een openbare IP-voorvoegselbron met de naam **myPublicIPprefixsource** te maken in **myResourceGroupNAT**.  Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$publicIPPrefixsource** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$prips = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prips -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Een NAT-gatewaybron maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service maken en configureren met behulp van de NAT-gatewaybron:
  - Een openbare IP-groep en een openbaar IP-voorvoegsel die moeten worden gebruikt voor uitgaande stromen die zijn vertaald door de NAT-gatewaybron.
  - Wijzig de niet-actieve time-out van de standaardinstelling van 4 minuten naar 10 minuten.

Maak een wereldwijde Azure [NAT-gateway met Nieuw-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). Het resultaat van deze opdracht maakt een gatewaybron met de naam **myNATgateway** die het openbare IP-adres **myPublicIPsource** gebruikt en het openbare IP-voorvoegsel **myPublicIPprefixsource**. De niet-actieve time-out is ingesteld op 10 minuten.  Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$natGateway** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$nnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $nnm -ResourceGroupName $rsg -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Location $loc -Sku $sku -IdleTimeoutInMinutes 10      
  ```

Op dit punt is de NAT-gateway functioneel en ontbreekt het alleen om te configureren welke subnetten van een virtueel netwerk het moeten gebruiken.

## <a name="prepare-the-source-for-outbound-traffic"></a>De bron voorbereiden op uitgaand verkeer

Wij begeleiden u bij het opzetten van een volledige testomgeving. U stelt een test in met open-sourcetools om de NAT-gateway te verifiëren. We beginnen met de bron, die de NAT-gateway gebruikt die we eerder hebben gemaakt.

### <a name="configure-virtual-network-for-source"></a>Virtueel netwerk configureren voor bron

Maak het virtuele netwerk en koppel het subnet aan de gateway.

Maak een virtueel netwerk met de naam **myVnetsource** met een subnet genaamd **mySubnetsource** met behulp van [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) in de **myResourceGroupNAT** met behulp van [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). De IP-adresruimte voor het virtuele netwerk is **192.168.0.0/16**. Het subnet binnen het virtuele netwerk is **192.168.0.0/24**.  Het resultaat van de opdrachten wordt opgeslagen in variabelen met de naam **$subnetsource** en **$vnetsource** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$sbn = 'mySubnetsource'
$spfx = '192.168.0.0/24'
$vnm = 'myVnetsource'
$vpfx = '192.168.0.0/16'
$loc = 'eastus2'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $sbn -AddressPrefix $spfx -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnet
```

Al het uitgaande verkeer naar internetbestemmingen maakt nu gebruik van de NAT-service.  Het is niet nodig om een UDR te configureren.

Voordat we de NAT-gateway kunnen testen, moeten we een bron-VM maken.  We wijzen een openbare IP-adresbron toe als openbaar IP-adres op instantieom toegang te krijgen tot deze vm van buitenaf. Dit adres wordt alleen gebruikt om toegang te krijgen voor de test.  We laten zien hoe de NAT-service voorrang heeft op andere uitgaande opties.

U deze VM ook maken zonder een openbaar IP en een andere VM maken om te gebruiken als een jumpbox zonder een openbaar IP als een oefening.

### <a name="create-public-ip-for-source-vm"></a>Openbaar IP maken voor bron-VM

We maken een openbaar IP om toegang te krijgen tot de VM.  Gebruik [Nieuw-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) om een openbare IP-adresbron met de naam **myPublicIPVM** te maken in **myResourceGroupNAT**.  Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$publicIpsourceVM** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pipvm = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvm -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Maak een NSG en stel SSH-eindpunt voor VM bloot

Omdat standaard openbare IP-adressen standaard 'veilig' zijn, maken we een NSG om inkomende toegang voor ssh toe te staan. NAT-service is stroomrichting bewust. Deze NSG wordt niet gebruikt voor uitgaande zodra NAT-gateway is geconfigureerd op hetzelfde subnet. Gebruik [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) om een NSG-bron met de naam **myNSGsource**te maken. Gebruik [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) om een NSG-regel te maken voor SSH-toegang met de naam **ssh** in **myResourceGroupNAT.** Het resultaat van deze opdracht wordt opgeslagen in variabele met de naam **$nsgsource** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-source-vm"></a>NIC voor bron-VM maken

Maak een netwerkinterface met [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) met de naam **myNicsource**. Met deze opdracht wordt het ip-adres openbaar en de netwerkbeveiligingsgroep aandeast. Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$nicsource** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $loc
```

### <a name="create-a-source-vm"></a>Een bron-VM maken

#### <a name="create-ssh-key-pair"></a>Een SSH-sleutelpaar maken

U hebt een SSH-sleutelpaar nodig om deze snelstart te volgen. Als u al een SSH-sleutelpaar hebt, kunt u deze stap overslaan.

Gebruik ssh-keygen om een SSH-sleutelpaar te maken.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Zie [SSH-sleutels gebruiken met Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) voor gedetailleerde informatie over het maken van SSH-sleutelparen, waaronder het gebruik van PuTTy.

Als u het SSH-sleutelpaar maakt met de Cloud Shell, wordt het sleutelpaar opgeslagen in een containerafbeelding. Dit [opslagaccount wordt automatisch gemaakt](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Verwijder het opslagaccount of het bestandsaandeel binnen, pas nadat u uw sleutels hebt opgehaald.

#### <a name="create-vm-configuration"></a>VM-configuratie maken

Voor het maken van een virtuele machine in PowerShell, maakt u een configuratie die instellingen bevat zoals de te gebruiken installatiekopie, grootte en verificatieopties. Vervolgens wordt de configuratie gebruikt om de virtuele machine te bouwen.

Definieer de SSH-referenties, besturingssysteeminformatie en grootte van de virtuele machine. In dit voorbeeld wordt de SSH-toets opgeslagen in ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combineer de configuratiedefinities om een VM met de naam **myVMsource** te maken met [Nieuw-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) in **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigsource
```

Hoewel de opdracht onmiddellijk terugkeert, kan het enkele minuten duren voordat de VM wordt geïmplementeerd.

## <a name="prepare-destination-for-outbound-traffic"></a>Bestemming voorbereiden op uitgaand verkeer

We maken nu een bestemming voor het uitgaande verkeer dat door de NAT-service is vertaald, zodat u het testen.

### <a name="configure-virtual-network-for-destination"></a>Virtueel netwerk configureren voor bestemming

We moeten een virtueel netwerk creëren waar de virtuele machine van de bestemming zal zijn.  Deze opdrachten zijn dezelfde stappen als voor de bron-VM. Er zijn kleine wijzigingen toegevoegd om het eindpunt van de bestemming bloot te leggen.

Maak een virtueel netwerk met de naam **myVnetdestination** met een subnet genaamd **mySubnetdestination** met behulp van [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) in de **myResourceGroupNAT** met behulp van [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). De IP-adresruimte voor het virtuele netwerk is **192.168.0.0/16**. Het subnet binnen het virtuele netwerk is **192.168.0.0/24**.  Het resultaat van de opdrachten wordt opgeslagen in variabelen met de naam **$subnetdestination** en **$vnetdestination** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnetdestination
```

### <a name="create-public-ip-for-destination-vm"></a>Openbaar IP maken voor doel-VM

We maken een openbaar IP om toegang te krijgen tot de doel-VM.  Gebruik [Nieuw-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) om een openbare IP-adresbron met de naam **myPublicIPdestinationVM** in **myResourceGroupNAT**te maken.  Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$publicIpdestinationVM** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rsg -AllocationMethod $all -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Maak een NSG en stel SSH en HTTP-eindpunt voor VM bloot

Standaard openbare IP-adressen zijn 'standaard veilig', we maken een NSG om inkomende toegang voor ssh mogelijk te maken. Gebruik [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) om een NSG-bron met de naam **myNSGdestination**te maken. Gebruik [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) om een NSG-regel te maken voor SSH-toegang met de naam **ssh**.  Gebruik [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) om een NSG-regel te maken voor HTTP-toegang met de naam **http**. Beide regels worden gemaakt in **myResourceGroupNAT**. Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$nsgdestination** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule,$httprule
```

### <a name="create-nic-for-destination-vm"></a>NIC voor doelVM maken

Maak een netwerkinterface met [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) met de naam **myNicdestination**. Deze opdracht wordt gekoppeld aan het openbare IP-adres en de netwerkbeveiligingsgroep. Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$nicdestination** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $loc
```

### <a name="create-a-destination-vm"></a>Een doel-VM maken

#### <a name="create-vm-configuration"></a>VM-configuratie maken

Voor het maken van een virtuele machine in PowerShell, maakt u een configuratie die instellingen bevat zoals de te gebruiken installatiekopie, grootte en verificatieopties. Vervolgens wordt de configuratie gebruikt om de virtuele machine te bouwen.

Definieer de SSH-referenties, besturingssysteeminformatie en grootte van de virtuele machine. In dit voorbeeld wordt de SSH-toets opgeslagen in ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$vmd = 'myVMdestination'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combineer de configuratiedefinities om een VM met de naam **myVMdestination** te maken met [Nieuw-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) in **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigdestination
```

Hoewel de opdracht onmiddellijk terugkeert, kan het enkele minuten duren voordat de VM wordt geïmplementeerd.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Een webserver voorbereiden en payload testen op doel-VM

Eerst moeten we het IP-adres van de doel-VM ontdekken.  Als u het openbare IP-adres van de VM wilt krijgen, gebruikt u [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPdestinationVM'
  
Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Kopieer het openbare IP-adres en plak het in een notitieblok, zodat u het in volgende stappen gebruiken. Geef aan dat dit de virtuele bestemmingsmachine is.

### <a name="sign-in-to-destination-vm"></a>Aanmelden bij doel-VM

De SSH-referenties moeten vanaf de vorige bewerking in uw Cloud Shell worden opgeslagen.  Open een [Azure Cloud Shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap naar SSH is opgehaald naar de virtuele machine. 

```bash
ssh azureuser@<ip-address-destination>
```

Kopieer en plak de volgende opdrachten zodra u bent ingelogd.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Deze opdrachten werken uw virtuele machine bij, installeren nginx en maken een bestand van 100 KBytes. Dit bestand wordt opgehaald van de bron-VM met behulp van de NAT-service.

Sluit de SSH-sessie af met de doel-VM.

## <a name="prepare-test-on-source-vm"></a>Test voorbereiden op bron-VM

Eerst moeten we het IP-adres van de bron VM te ontdekken.  Als u het openbare IP-adres van de VM wilt krijgen, gebruikt u [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPsourceVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Kopieer het openbare IP-adres en plak het in een notitieblok, zodat u het in volgende stappen gebruiken. Geef aan dat dit de virtuele bronmachine is.

### <a name="log-into-source-vm"></a>Inloggen bij bron-VM

Nogmaals, de SSH-referenties worden opgeslagen in Cloud Shell. Open een nieuw tabblad voor [Azure Cloud Shell](https://shell.azure.com) in uw browser.  Gebruik het IP-adres dat in de vorige stap naar SSH is opgehaald naar de virtuele machine. 

```bash
ssh azureuser@<ip-address-source>
```

Kopieer en plak de volgende opdrachten om u voor te bereiden op het testen van de NAT-service.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Deze opdrachten werken uw virtuele machine bij, installeren go, installeren [hey](https://github.com/rakyll/hey) van GitHub en werken uw shell-omgeving bij.

U bent nu klaar om NAT-service te testen.

## <a name="validate-nat-service"></a>NAT-service valideren

Terwijl u bent aangemeld bij de bron-VM, u **curl** en **hey** gebruiken om aanvragen naar het IP-adres van de bestemming te genereren.

Gebruik curl om het bestand 100-KBytes op te halen.  Vervang ** \<ip-adres-bestemming>** in het onderstaande voorbeeld door het doel-IP-adres dat u eerder hebt gekopieerd.  De parameter **--output** geeft aan dat het opgehaalde bestand wordt verwijderd.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

U ook het genereren van een reeks verzoeken met behulp van **hey**. Vervang nogmaals ** \<ip-adres-bestemming>** door het doel-IP-adres dat u eerder hebt gekopieerd.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Deze opdracht genereert 100 aanvragen, 10 gelijktijdig, met een time-out van 30 seconden. De TCP-verbinding wordt niet opnieuw gebruikt.  Elke aanvraag haalt 100 Kbytes op.  Aan het einde van de run, **hey** zal een aantal statistieken over hoe goed de NAT service deed verslag.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, u de opdracht [Verwijderen-AzResourceGroep](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) gebruiken om de resourcegroep en alle bronnen binnen te verwijderen.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een NAT-gateway gemaakt, een bron- en doel-VM gemaakt en vervolgens de NAT-gateway getest.

Bekijk de statistieken in Azure Monitor om uw NAT-service te bekijken. Diagnose problemen zoals resource uitputting van de beschikbare SNAT-poorten.  Uitputting van resources van SNAT-poorten is eenvoudig aan te pakken door extra openbare IP-adresbronnen of openbare IP-voorvoegselbronnen of beide toe te voegen.

- Meer informatie over [NAT van virtueel netwerk](./nat-overview.md)
- Meer informatie over [NAT-gatewaybron](./nat-gateway-resource.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure-portal.](./quickstart-create-nat-gateway-portal.md)

> [!div class="nextstepaction"]

