---
title: 'Zelf studie: een NAT-gateway maken en testen-Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Deze zelf studie laat zien hoe u een NAT-gateway maakt met behulp van de Azure PowerShell en de NAT-service test
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79202221"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Zelf studie: een NAT-gateway maken met behulp van Azure PowerShell en de NAT-service testen

In deze zelf studie maakt u een NAT-gateway om uitgaande connectiviteit te bieden voor virtuele machines in Azure. Als u de NAT-gateway wilt testen, implementeert u een virtuele bron-en doel machine. U gaat de NAT-gateway testen door uitgaande verbindingen te maken met een openbaar IP-adres. Deze verbindingen zijn afkomstig van de bron-naar de virtuele doel machine. In deze zelf studie worden de bron en bestemming in twee verschillende virtuele netwerken in dezelfde resource groep geïmplementeerd ter vereenvoudiging.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

U kunt deze zelf studie volt ooien met behulp van Azure Cloud Shell of de betreffende opdrachten lokaal uitvoeren.  Als u Azure Cloud Shell nooit hebt gebruikt, moet u [zich nu aanmelden](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep maken met [az group create](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voor beeld wordt een resource groep met de naam **myResourceGroupNAT** gemaakt op de locatie **eastus2** :


```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Voor toegang tot internet hebt u een of meer open bare IP-adressen nodig voor de NAT-gateway. Gebruik [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) voor het maken van een open bare IP-adres resource met de naam **myPublicIPsource** in **myResourceGroupNAT**. Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$publicIPsource** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pips = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pips -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voor voegsel maken

 Gebruik [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) voor het maken van een open bare IP-voor voegsel resource met de naam **myPublicIPprefixsource** in **myResourceGroupNAT**.  Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$publicIPPrefixsource** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$prips = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prips -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Een NAT-gateway resource maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service kunt maken en configureren met behulp van de NAT-gateway resource:
  - Een open bare IP-adres groep en een openbaar IP-voor voegsel dat moet worden gebruikt voor uitgaande stromen die worden vertaald door de NAT-gateway resource.
  - Wijzig de time-out voor inactiviteit van de standaard waarde van 4 minuten in 10 minuten.

Maak een globale Azure NAT-gateway met [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). Met deze opdracht maakt u een gateway resource met de naam **myNATgateway** die gebruikmaakt van het open bare IP-adres **myPublicIPsource** en het open bare IP-voor voegsel **myPublicIPprefixsource**. De time-out voor inactiviteit is ingesteld op 10 minuten.  Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$natGateway** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$nnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $nnm -ResourceGroupName $rsg -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Location $loc -Sku $sku -IdleTimeoutInMinutes 10      
  ```

Op dit punt is de NAT-gateway functioneel en ontbreekt er een configuratie van de subnetten van een virtueel netwerk.

## <a name="prepare-the-source-for-outbound-traffic"></a>De bron voorbereiden voor uitgaand verkeer

U wordt begeleid bij het instellen van een volledige test omgeving. U stelt een test met open source-hulpprogram ma's in om de NAT-gateway te controleren. We beginnen met de bron. deze maakt gebruik van de NAT-gateway die we eerder hebben gemaakt.

### <a name="configure-virtual-network-for-source"></a>Het virtuele netwerk configureren voor de bron

Maak het virtuele netwerk en koppel het subnet aan de gateway.

Maak een virtueel netwerk met de naam **myVnetsource** met een subnet met de naam **mySubnetsource** met behulp van [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) in de **MyResourceGroupNAT** met behulp van [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). De IP-adres ruimte voor het virtuele netwerk is **192.168.0.0/16**. Het subnet in het virtuele netwerk is **192.168.0.0/24**.  Het resultaat van de opdrachten wordt opgeslagen in variabelen met de naam **$subnetsource** en **$vnetsource** voor later gebruik.

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

Al het uitgaande verkeer naar Internet bestemmingen maakt nu gebruik van de NAT-service.  Het is niet nodig om een UDR te configureren.

Voordat we de NAT-gateway kunnen testen, moeten we een bron-VM maken.  Er wordt een open bare IP-adres resource toegewezen als een open bare IP op exemplaar niveau voor toegang tot deze VM vanaf buiten. Dit adres wordt alleen gebruikt voor toegang tot de test.  We laten u zien hoe de NAT-service prioriteit krijgt boven andere uitgaande opties.

U kunt deze virtuele machine ook maken zonder een openbaar IP-adres en een andere virtuele machine maken om te gebruiken als JumpBox zonder open bare IP als oefening.

### <a name="create-public-ip-for-source-vm"></a>Een openbaar IP-adres voor de bron-VM maken

We maken een openbaar IP-adres dat wordt gebruikt voor toegang tot de virtuele machine.  Gebruik [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) voor het maken van een open bare IP-adres resource met de naam **myPublicIPVM** in **myResourceGroupNAT**.  Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$publicIpsourceVM** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pipvm = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvm -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Een NSG maken en het SSH-eind punt voor de virtuele machine weer geven

Omdat standaard open bare IP-adressen standaard ' veilig ' zijn, maken we een NSG voor het toestaan van inkomende toegang voor SSH. De NAT-service is geschikt voor stroom richting. Deze NSG wordt niet gebruikt voor uitgaand verkeer wanneer de NAT-gateway is geconfigureerd op hetzelfde subnet. Gebruik [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) om een NSG-resource te maken met de naam **myNSGsource**. Gebruik [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) voor het maken van een NSG-regel voor SSH-toegang met de naam **SSH** in **myResourceGroupNAT**. Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$nsgsource** voor later gebruik.

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

### <a name="create-nic-for-source-vm"></a>NIC maken voor de bron-VM

Maak een netwerk interface met [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) met de naam **myNicsource**. Met deze opdracht worden het open bare IP-adres en de netwerk beveiligings groep gekoppeld. Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$nicsource** voor later gebruik.

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

Ssh-keygen gebruiken om een SSH-sleutel paar te maken.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Zie [SSH-sleutels gebruiken met Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) voor gedetailleerde informatie over het maken van SSH-sleutelparen, waaronder het gebruik van PuTTy.

Als u het SSH-sleutel paar maakt met behulp van de Cloud Shell, wordt het sleutel paar opgeslagen in een container installatie kopie. Dit [opslag account wordt automatisch gemaakt](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Verwijder het opslag account of de bestands share binnen pas nadat u de sleutels hebt opgehaald.

#### <a name="create-vm-configuration"></a>VM-configuratie maken

Voor het maken van een virtuele machine in PowerShell, maakt u een configuratie die instellingen bevat zoals de te gebruiken installatiekopie, grootte en verificatieopties. Vervolgens wordt de configuratie gebruikt om de virtuele machine te bouwen.

Definieer de SSH-referenties, besturingssysteeminformatie en grootte van de virtuele machine. In dit voor beeld wordt de SSH-sleutel opgeslagen in ~/.ssh/id_rsa. pub.

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
Combi neer de configuratie definities voor het maken van een virtuele machine met de naam **myVMsource** met [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) in **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigsource
```

Terwijl de opdracht onmiddellijk wordt geretourneerd, kan het enkele minuten duren voordat de virtuele machine wordt geïmplementeerd.

## <a name="prepare-destination-for-outbound-traffic"></a>Bestemming voorbereiden voor uitgaand verkeer

We gaan nu een bestemming maken voor het uitgaande verkeer dat door de NAT-service is vertaald, zodat u het kunt testen.

### <a name="configure-virtual-network-for-destination"></a>Virtueel netwerk voor bestemming configureren

We moeten een virtueel netwerk maken waarin de virtuele doel machine wordt gemaakt.  Deze opdrachten zijn dezelfde stappen als voor de bron-VM. Er zijn kleine wijzigingen toegevoegd om het doel eindpunt beschikbaar te maken.

Maak een virtueel netwerk met de naam **myVnetdestination** met een subnet met de naam **mySubnetdestination** met behulp van [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) in de **MyResourceGroupNAT** met behulp van [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). De IP-adres ruimte voor het virtuele netwerk is **192.168.0.0/16**. Het subnet in het virtuele netwerk is **192.168.0.0/24**.  Het resultaat van de opdrachten wordt opgeslagen in variabelen met de naam **$subnetdestination** en **$vnetdestination** voor later gebruik.

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

### <a name="create-public-ip-for-destination-vm"></a>Een openbaar IP-adres voor de doel-VM maken

We maken een openbaar IP-adres om te gebruiken voor toegang tot de doel-VM.  Gebruik [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) voor het maken van een open bare IP-adres resource met de naam **myPublicIPdestinationVM** in **myResourceGroupNAT**.  Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$publicIpdestinationVM** voor later gebruik.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rsg -AllocationMethod $all -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Een NSG maken en het SSH-en HTTP-eind punt voor de virtuele machine beschikbaar stellen

Standaard open bare IP-adressen zijn standaard ' veilig ', we maken een NSG om inkomende toegang voor SSH toe te staan. Gebruik [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) om een NSG-resource te maken met de naam **myNSGdestination**. Gebruik [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) om een NSG-regel te maken voor SSH-toegang met de naam **SSH**.  Gebruik [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) voor het maken van een NSG-regel voor HTTP-toegang met de naam **http**. Beide regels worden gemaakt in **myResourceGroupNAT**. Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$nsgdestination** voor later gebruik.

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

### <a name="create-nic-for-destination-vm"></a>NIC maken voor bestemmings-VM

Maak een netwerk interface met [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) met de naam **myNicdestination**. Met deze opdracht wordt gekoppeld aan het open bare IP-adres en de netwerk beveiligings groep. Het resultaat van deze opdracht wordt opgeslagen in een variabele met de naam **$nicdestination** voor later gebruik.

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

Definieer de SSH-referenties, besturingssysteeminformatie en grootte van de virtuele machine. In dit voor beeld wordt de SSH-sleutel opgeslagen in ~/.ssh/id_rsa. pub.

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
Combi neer de configuratie definities voor het maken van een virtuele machine met de naam **myVMdestination** met [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) in **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigdestination
```

Terwijl de opdracht onmiddellijk wordt geretourneerd, kan het enkele minuten duren voordat de virtuele machine wordt geïmplementeerd.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Een webserver voorbereiden en payload testen op de doel-VM

Eerst moet het IP-adres van de doel-VM worden gedetecteerd.  Gebruik [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest)om het open bare IP-adres van de virtuele machine op te halen. 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPdestinationVM'
  
Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Kopieer het open bare IP-adres en plak het in een Klad blok, zodat u het kunt gebruiken in de volgende stappen. Geef aan dat dit de virtuele doel machine is.

### <a name="sign-in-to-destination-vm"></a>Aanmelden bij de doel-VM

De SSH-referenties moeten worden opgeslagen in uw Cloud Shell van de vorige bewerking.  Open een [Azure Cloud shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine te gaan. 

```bash
ssh azureuser@<ip-address-destination>
```

Kopieer en plak de volgende opdrachten zodra u bent aangemeld.  

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

Met deze opdrachten wordt uw virtuele machine bijgewerkt, wordt nginx geïnstalleerd en wordt een bestand van 100 KB gemaakt. Dit bestand wordt opgehaald van de bron-VM met behulp van de NAT-service.

Sluit de SSH-sessie met de doel-VM.

## <a name="prepare-test-on-source-vm"></a>Test voorbereiden op de bron-VM

Eerst moet het IP-adres van de bron-VM worden gedetecteerd.  Gebruik [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest)om het open bare IP-adres van de virtuele machine op te halen. 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPsourceVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Kopieer het open bare IP-adres en plak het in een Klad blok, zodat u het kunt gebruiken in de volgende stappen. Geef aan dat dit de virtuele bron machine is.

### <a name="log-into-source-vm"></a>Aanmelden bij de bron-VM

De SSH-referenties worden opgeslagen in Cloud Shell. Open een nieuw tabblad voor [Azure Cloud shell](https://shell.azure.com) in uw browser.  Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine te gaan. 

```bash
ssh azureuser@<ip-address-source>
```

Kopieer en plak de volgende opdrachten om het testen van de NAT-service voor te bereiden.

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

Met deze opdrachten wordt uw virtuele machine bijgewerkt, installeert u go, installeert u [een van github](https://github.com/rakyll/hey) en werkt u uw shell-omgeving bij.

U bent nu klaar om de NAT-service te testen.

## <a name="validate-nat-service"></a>NAT-service valideren

Terwijl u bent aangemeld bij de bron-VM, kunt u **krul** **gebruiken en zo aanvragen genereren voor het** doel-IP-adres.

Gebruik krul om het bestand 100-KBytes op te halen.  Vervang ** \<IP-adres-doel>** in het onderstaande voor beeld met het doel-IP-adres dat u eerder hebt gekopieerd.  De para meter **--output** geeft aan dat het opgehaalde bestand wordt verwijderd.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

U kunt ook een reeks aanvragen genereren met behulp van **Hey**. Vervang opnieuw IP ** \<-adres-doel>** door het doel-IP-adres dat u eerder hebt gekopieerd.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Met deze opdracht worden 100-aanvragen gegenereerd, 10 gelijktijdig, met een time-out van dertig seconden. De TCP-verbinding wordt niet opnieuw gebruikt.  Elke aanvraag zal 100 KB ophalen.  Aan het einde van de uitvoering worden enkele statistieken gerapporteerd over hoe goed de **NAT-service** is.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) gebruiken om de resource groep en alle resources in te verwijderen wanneer u deze niet meer nodig hebt.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u een NAT-gateway gemaakt, een bron-en doel-VM gemaakt en vervolgens de NAT-gateway getest.

Controleer de metrische gegevens in Azure Monitor om uw NAT-service weer te geven. Problemen vaststellen, zoals de bron uitputting van de beschik bare SNAT-poorten.  Bron uitputting van de SNAT-poorten kan gemakkelijk worden verholpen door extra open bare IP-adres bronnen of open bare IP-prefix bronnen of beide toe te voegen.

- Meer informatie over [Virtual Network NAT](./nat-overview.md)
- Meer informatie over de [NAT gateway-resource](./nat-gateway-resource.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure cli](./quickstart-create-nat-gateway-cli.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure Portal](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

