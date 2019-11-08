---
title: Een VHD met generalisatie uploaden om meerdere virtuele machines in azure te maken | Microsoft Docs
description: Upload een gegeneraliseerde VHD naar een Azure Storage-account om een Windows-VM te maken voor gebruik met het Resource Manager-implementatie model.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 24ed7b75dfa8cb09c530a3f4a896aa9ff9aa92b5
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749174"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Een gegeneraliseerde VHD uploaden naar Azure om een nieuwe virtuele machine te maken

In dit onderwerp wordt beschreven hoe u een gegeneraliseerde niet-beheerde schijf naar een opslag account uploadt en vervolgens een nieuwe VM maakt met behulp van de geüploade schijf. Voor een gegeneraliseerde VHD-installatie kopie zijn al uw persoonlijke account gegevens verwijderd met behulp van Sysprep. 

Als u een virtuele machine wilt maken op basis van een gespecialiseerde VHD in een opslag account, raadpleegt u [een virtuele machine maken op basis van een gespecialiseerde VHD](sa-create-vm-specialized.md).

In dit onderwerp wordt beschreven hoe u opslag accounts gebruikt, maar we raden klanten aan om in plaats daarvan te gaan gebruiken Managed Disks. Zie [een nieuwe virtuele machine maken van een gegeneraliseerde VHD die is geüpload naar Azure met Managed disks](upload-generalized-managed.md)voor een volledige procedure voor het voorbereiden, uploaden en maken van een nieuwe virtuele machine met Managed disks.

 

## <a name="prepare-the-vm"></a>De virtuele machine voorbereiden

Voor een gegeneraliseerde VHD zijn al uw persoonlijke account gegevens verwijderd met behulp van Sysprep. Als u van plan bent om de VHD te gebruiken als een installatie kopie om nieuwe Vm's te maken op basis van, moet u het volgende doen:
  
  * [Bereid een Windows VHD voor om te uploaden naar Azure](prepare-for-upload-vhd-image.md). 
  * De virtuele machine generaliseren met Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Een virtuele Windows-machine generaliseren met Sysprep
In deze sectie wordt beschreven hoe u uw virtuele Windows-machine generaliseren voor gebruik als een installatie kopie. Sysprep verwijdert onder meer al uw persoonlijke accountinformatie en de machine wordt voorbereid om als een installatiekopie te worden gebruikt. Raadpleeg [Sysprep gebruiken: een inleiding](https://technet.microsoft.com/library/bb457073.aspx) voor meer informatie over Sysprep.

Zorg ervoor dat de server functies die op de computer worden uitgevoerd, worden ondersteund door Sysprep. Zie [Sysprep-ondersteuning voor Server functies](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) voor meer informatie.

> [!IMPORTANT]
> Als u Sysprep uitvoert voordat u de VHD voor het eerst uploadt naar Azure, moet u ervoor zorgen dat u [uw VM hebt voor bereid](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voordat u Sysprep uitvoert. 
> 
> 

1. Meld u aan bij de virtuele Windows-machine.
2. Open het venster met de opdrachtprompt als beheerder. Wijzig de directory in **%windir%\system32\sysprep** en voer dan `sysprep.exe`run uit.
3. In het dialoogvenster **Hulpprogramma voor systeemvoorbereiding** selecteert u **OOBE (Out-of-Box Experience) van systeem starten** en zorgt u dat het selectievakje **Generaliseren** is ingeschakeld.
4. Selecteer **Afsluiten**in het **afsluit opties**.
5. Klik op **OK**.
   
    ![Sysprep starten](./media/upload-generalized-managed/sysprepgeneral.png)
6. Wanneer Sysprep is voltooid, wordt de virtuele machine afgesloten. 

> [!IMPORTANT]
> Start de virtuele machine niet opnieuw op voordat u klaar bent met het uploaden van de VHD naar Azure of het maken van een installatie kopie van de virtuele machine. Als de virtuele machine per ongeluk opnieuw wordt opgestart, voert u Sysprep uit om deze opnieuw te generaliseren.
> 
> 


## <a name="upload-the-vhd"></a>De VHD uploaden

Upload de VHD naar een Azure Storage-account.

### <a name="log-in-to-azure"></a>Meld u aan bij Azure.
Als u Power shell-versie 1,4 of hoger nog niet hebt geïnstalleerd, leest u [hoe u Azure PowerShell installeert en configureert](/powershell/azure/overview).

1. Open Azure PowerShell en meld u aan bij uw Azure-account. Er wordt een pop-upvenster geopend om uw Azure-account referenties in te voeren.
   
    ```powershell
    Connect-AzAccount
    ```
2. Haal de abonnement-Id's op voor uw beschik bare abonnementen.
   
    ```powershell
    Get-AzSubscription
    ```
3. Stel het juiste abonnement in met de abonnements-ID. Vervang `<subscriptionID>` door de ID van het juiste abonnement.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Het opslag account ophalen
U hebt een opslag account in azure nodig om de geüploade VM-installatie kopie op te slaan. U kunt een bestaand opslag account gebruiken of een nieuwe maken. 

Als u de beschik bare opslag accounts wilt weer geven, typt u:

```powershell
Get-AzStorageAccount
```

Als u een bestaand opslag account wilt gebruiken, gaat u verder naar de sectie de VM-installatie kopie uploaden.

Als u een opslag account wilt maken, voert u de volgende stappen uit:

1. U hebt de naam nodig van de resource groep waar het opslag account moet worden gemaakt. Als u alle resource groepen in uw abonnement wilt weten, typt u:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Als u een resource groep met de naam **myResourceGroup** wilt maken in de regio **VS-West** , typt u:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Maak een opslag account met de naam **mystorageaccount** in deze resource groep met behulp van de cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) :
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Het uploaden starten 

Gebruik de cmdlet [add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) om de installatie kopie te uploaden naar een container in uw opslag account. In dit voor beeld wordt het bestand **myVHD. VHD** geüpload van `"C:\Users\Public\Documents\Virtual hard disks\"` naar een opslag account met de naam **mystorageaccount** in de resource groep **myResourceGroup** . Het bestand wordt in de container met de naam **mycontainer** geplaatst en de nieuwe bestands naam wordt **myUploadedVHD. VHD**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Als dat lukt, krijgt u een antwoord dat er ongeveer als volgt uitziet:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Afhankelijk van uw netwerk verbinding en de grootte van het VHD-bestand kan het enige tijd duren voordat deze opdracht is voltooid.


## <a name="create-a-new-vm"></a>Een nieuwe VM maakt 

U kunt nu de geüploade VHD gebruiken om een nieuwe virtuele machine te maken. 

### <a name="set-the-uri-of-the-vhd"></a>De URI van de VHD instellen

De URI voor de VHD die moet worden gebruikt, heeft de volgende indeling: https://**mystorageaccount**. blob.core.windows.net/**mycontainer**/**MyVhdName**. VHD. In dit voor beeld bevindt de VHD met de naam **myVHD** zich in het opslag account **mystorageaccount** in de container **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
Maak het vNet en het subnet van het [virtuele netwerk](../../virtual-network/virtual-networks-overview.md).

1. Maak het subnet. In het volgende voor beeld wordt een subnet met de naam **mySubnet** in de resource groep **myResourceGroup** gemaakt met het adres voorvoegsel **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Maak het virtuele netwerk. In het volgende voor beeld wordt een virtueel netwerk met de naam **myVnet** gemaakt op de locatie **VS-West** met het adres voorvoegsel van **10.0.0.0/16**.  
   
    ```powershell
    $location = "WestUS"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Een openbaar IP-adres en een netwerk interface maken
Om te kunnen communiceren met de virtuele machine in het virtuele netwerk, hebt u een [openbaar IP-adres](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) en een netwerkinterface nodig.

1. Maak een openbaar IP-adres. In dit voor beeld wordt een openbaar IP-adres gemaakt met de naam **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Maak de NIC. In dit voor beeld wordt een NIC gemaakt met de naam **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>De netwerk beveiligings groep en een RDP-regel maken
Als u zich met RDP wilt aanmelden bij uw VM, moet u een beveiligings regel hebben waarmee RDP-toegang wordt toegestaan op poort 3389. 

In dit voor beeld wordt een NSG met de naam **mijnnbg** gemaakt die een regel bevat met de naam **myRdpRule** die RDP-verkeer via poort 3389 toestaat. Zie [poorten openen voor een virtuele machine in azure met behulp van Power shell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie over nsg's.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Een variabele voor het virtuele netwerk maken
Maak een variabele voor het voltooide virtuele netwerk. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>De virtuele machine maken
In het volgende Power shell-script ziet u hoe u de configuraties van virtuele machines instelt en hoe u de geüploade VM-installatie kopie gebruikt als bron voor de nieuwe installatie.



```powershell
# Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Controleren of de virtuele machine is gemaakt
Als u klaar bent, ziet u de zojuist gemaakte VM in de [Azure Portal](https://portal.azure.com) onder **Bladeren** > **virtuele machines**of met behulp van de volgende Power shell-opdrachten:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Volgende stappen
Zie [virtuele machines beheren met Azure Resource Manager en Power shell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie over het beheren van uw nieuwe virtuele machine met Azure PowerShell.


