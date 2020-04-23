---
title: Een Windows-VM maken op basis van een gespecialiseerde VHD in Azure
description: Maak een nieuwe Windows-vm door een gespecialiseerde beheerde schijf als besturingssysteemschijf te koppelen met behulp van het implementatiemodel Resource Manager.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 2939726898abc2abc0e62d0e36feedbfe7ba3645
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086399"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Een virtuele Windows-machine maken vanaf een speciale schijf met PowerShell

Maak een nieuwe VM door een gespecialiseerde beheerde schijf als besturingssysteemschijf te koppelen. Een gespecialiseerde schijf is een kopie van een virtuele harde schijf (VHD) van een bestaande VM die de gebruikersaccounts, toepassingen en andere statusgegevens van uw oorspronkelijke VM bevat. 

Wanneer u een gespecialiseerde VHD gebruikt om een nieuwe vm te maken, behoudt de nieuwe VM de computernaam van de oorspronkelijke VM. Andere computerspecifieke informatie wordt ook bewaard en in sommige gevallen kan deze dubbele informatie problemen veroorzaken. Houd u bij het kopiëren van een vm bewust van de typen computerspecifieke informatie waarop uw toepassingen vertrouwen.

U hebt verschillende mogelijkheden:
* [Gebruik een bestaande beheerde schijf](#option-1-use-an-existing-disk). Deze optie is handig als u een vm hebt die niet goed werkt. U de vm verwijderen en vervolgens de beheerde schijf opnieuw gebruiken om een nieuwe virtuele machine te maken. 
* [Een VHD uploaden](#option-2-upload-a-specialized-vhd) 
* [Een bestaande Azure-vm kopiëren met behulp van momentopnamen](#option-3-copy-an-existing-azure-vm)

U de Azure-portal ook gebruiken om een nieuwe VM te [maken vanuit een gespecialiseerde VHD.](create-vm-specialized-portal.md)

In dit artikel ziet u hoe u beheerde schijven gebruiken. Zie Een VM maken van een gespecialiseerde [VHD in een opslagaccount](sa-create-vm-specialized.md)als u een verouderde implementatie hebt.

We raden u aan het aantal gelijktijdige implementaties te beperken tot 20 VM's van één VHD of momentopname. 

## <a name="option-1-use-an-existing-disk"></a>Optie 1: Een bestaande schijf gebruiken

Als u een vm hebt die u hebt verwijderd en u de os-schijf opnieuw wilt gebruiken om een nieuwe virtuele machine te maken, gebruikt u [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
U deze schijf nu als de osschijf aan een [nieuwe VM](#create-the-new-vm)koppelen.

## <a name="option-2-upload-a-specialized-vhd"></a>Optie 2: Upload een gespecialiseerde VHD

U de VHD uploaden van een gespecialiseerde VM die is gemaakt met een on-premises virtualisatietool, zoals Hyper-V, of een VM die vanuit een andere cloud wordt geëxporteerd.

### <a name="prepare-the-vm"></a>De virtuele machine voorbereiden
Gebruik de VHD-as-is om een nieuwe virtuele machine te maken. 
  
  * [Bereid een Windows VHD voor om te uploaden naar Azure.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) **Generaliseer** de VM niet met Behulp van Sysprep.
  * Verwijder alle hulpprogramma's voor gastvirtualisatie en -agents die op de VM zijn geïnstalleerd (zoals VMware-hulpprogramma's).
  * Controleer of de VM is geconfigureerd om de IP-adres- en DNS-instellingen van DHCP op te halen. Dit zorgt ervoor dat de server een IP-adres binnen het virtuele netwerk verkrijgt wanneer deze wordt opgestart. 


### <a name="upload-the-vhd"></a>De VHD uploaden

U nu een VHD rechtstreeks uploaden naar een beheerde schijf. Zie Een [VHD uploaden naar Azure met Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md)voor instructies.

## <a name="option-3-copy-an-existing-azure-vm"></a>Optie 3: Een bestaande Azure VM kopiëren

U een kopie van een VM maken die beheerde schijven gebruikt door een momentopname van de vm te maken en vervolgens die momentopname te gebruiken om een nieuwe beheerde schijf en een nieuwe vm te maken.

Als u een bestaande virtuele machine naar een andere regio wilt kopiëren, u azcopy gebruiken om een kopie van een schijf in een andere regio te [maken.](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) 

### <a name="take-a-snapshot-of-the-os-disk"></a>Maak een momentopname van de OS-schijf

U een momentopname maken van een hele VM (inclusief alle schijven) of van slechts één schijf. In de volgende stappen ziet u hoe u een momentopname maakt van alleen de OS-schijf van uw VM met de cmdlet [Nieuw-AzSnapshot.](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) 

Stel eerst een aantal parameters in. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Download het VM-object.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Download de naam van de OS-schijf.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Maak de momentopnameconfiguratie. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Neem de foto.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Als u deze momentopname wilt gebruiken om een VM te `-AccountType Premium_LRS` maken die goed moet presteren, voegt u de parameter toe aan de opdracht Nieuw-AzSnapshotConfig. Met deze parameter wordt de momentopname zo gemaakt dat deze wordt opgeslagen als een Premium Managed Disk. Premium Managed Disks zijn duurder dan Standaard, dus zorg ervoor dat je Premium nodig hebt voordat je deze parameter gebruikt.

### <a name="create-a-new-disk-from-the-snapshot"></a>Een nieuwe schijf maken op basis van de momentopname

Maak een beheerde schijf van de momentopname met [behulp van Nieuw-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). In dit voorbeeld wordt *myOSDisk* gebruikt voor de schijfnaam.

Maak een nieuwe resourcegroep voor de nieuwe virtuele machine.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Stel de naam van de OS-schijf in. 

```powershell
$osDiskName = 'myOsDisk'
```

Maak de beheerde schijf.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>De nieuwe VM maken 

Maak netwerken en andere VM-bronnen die door de nieuwe VM kunnen worden gebruikt.

### <a name="create-the-subnet-and-virtual-network"></a>Het subnet en het virtuele netwerk maken

Maak het [virtuele netwerk](../../virtual-network/virtual-networks-overview.md) en subnet voor de VM.

1. Maak het subnet. In dit voorbeeld wordt een subnet met de naam *mySubNet*, in de brongroep *myDestinationResourceGroup,* en wordt het subnetadresvoorvoegsel ingesteld op *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Maak het virtuele netwerk. In dit voorbeeld wordt de naam van het virtuele netwerk ingesteld op *myVnetName*, de locatie naar *West US*en het adresvoorvoegsel voor het virtuele netwerk op *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>De netwerkbeveiligingsgroep en een RDP-regel maken
Als u zich wilt aanmelden bij uw VM met extern bureaublad-protocol (RDP), moet u een beveiligingsregel hebben waarmee RDP-toegang op poort 3389 mogelijk is. In ons voorbeeld is de VHD voor de nieuwe VM gemaakt van een bestaande gespecialiseerde VM, zodat u een account gebruiken dat bestond op de bronvirtuele machine voor RDP.

In dit voorbeeld wordt de naam van de netwerkbeveiligingsgroep (NSG) ingesteld op *myNsg* en de naam van de RDP-regel op *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Zie Poorten openen voor een vm [in Azure met PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie over eindpunten en NSG-regels.

### <a name="create-a-public-ip-address-and-nic"></a>Een openbaar IP-adres en NIC maken
Om de communicatie met de virtuele machine in het virtuele netwerk mogelijk te maken, hebt u een [openbaar IP-adres](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) en een netwerkinterface nodig.

1. Maak het openbare IP.Create the public IP. In dit voorbeeld wordt de naam van het openbare IP-adres ingesteld op *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Maak de NIC. In dit voorbeeld wordt de NIC-naam ingesteld op *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>De naam en grootte van de VM instellen

In dit voorbeeld wordt de VM-naam ingesteld op *myVM* en de VM-grootte op *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Voeg de NIC toe
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>De osschijf toevoegen 

Voeg de OS-schijf toe aan de configuratie met [set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk). In dit voorbeeld wordt de grootte van de schijf ingesteld op *128 GB* en wordt de beheerde schijf als een *Windows-schijf* gekoppeld.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>De VM voltooien 

Maak de VM met behulp van [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) met de configuraties die we zojuist hebben gemaakt.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Als deze opdracht succesvol is, ziet u de uitvoer als volgt:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Controleren of de VM is gemaakt
U moet de nieuw gemaakte VM zien in de [Azure-portal](https://portal.azure.com) onder**Virtuele machines** **bladeren** > of met de volgende PowerShell-opdrachten.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Volgende stappen
Meld u aan bij uw nieuwe virtuele machine. Zie [Verbinding maken en inloggen op een virtuele Azure-machine met Windows voor](connect-logon.md)meer informatie.

