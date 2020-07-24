---
title: Een onbeheerde installatie kopie maken van een gegeneraliseerde VM in azure
description: Maak een unmanged-installatie kopie van een gegeneraliseerde Windows-VM die u kunt gebruiken om meerdere exemplaren van een virtuele machine in azure te maken.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.custom: storage-accounts
ms.openlocfilehash: 8077689bce988124da34428842df8fd2ef757bf0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87020182"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Een niet-beheerde VM-installatiekopie maken van een Azure-VM

In dit artikel wordt beschreven hoe u opslag accounts gebruikt. U wordt aangeraden beheerde schijven en beheerde installatie kopieën te gebruiken in plaats van een opslag account. Zie [een beheerde installatie kopie van een gegeneraliseerde vm in azure vastleggen](capture-image-resource.md)voor meer informatie.

In dit artikel wordt beschreven hoe u Azure PowerShell kunt gebruiken om een installatie kopie van een gegeneraliseerde Azure-VM te maken met behulp van een opslag account. Vervolgens kunt u de installatie kopie gebruiken om een andere virtuele machine te maken. De installatie kopie bevat de besturingssysteem schijf en de gegevens schijven die aan de virtuele machine zijn gekoppeld. De installatie kopie bevat geen virtuele netwerk resources, dus moet u deze resources instellen wanneer u de nieuwe VM maakt. 

 

## <a name="generalize-the-vm"></a>De virtuele machine generaliseren 
In deze sectie wordt beschreven hoe u uw virtuele Windows-machine generaliseren voor gebruik als een installatie kopie. Als u een virtuele machine generaliseert, worden alle gegevens van uw persoonlijke account, onder andere, verwijderd en wordt de computer voor bereid voor gebruik als installatie kopie. Raadpleeg [Sysprep gebruiken: een inleiding](/previous-versions/windows/it-pro/windows-xp/bb457073(v=technet.10)) voor meer informatie over Sysprep.

Zorg ervoor dat de server functies die op de computer worden uitgevoerd, worden ondersteund door Sysprep. Zie [Sysprep-ondersteuning voor Server functies](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) voor meer informatie.

> [!IMPORTANT]
> Als u de VHD voor het eerst uploadt naar Azure, moet u ervoor zorgen dat u [uw VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) hebt voor bereid voordat u Sysprep uitvoert. 
> 
> 

U kunt een virtuele Linux-machine ook generaliseren met `sudo waagent -deprovision+user` en vervolgens Power shell gebruiken om de virtuele machine vast te leggen. Zie [een virtuele Linux-machine generaliseren en vastleggen met behulp van de Azure cli](../linux/capture-image.md)voor meer informatie over het gebruik van de CLI om een virtuele machine vast te leggen.


1. Meld u aan bij de Windows-VM.
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

## <a name="log-in-to-azure-powershell"></a>Aanmelden bij Azure PowerShell
1. Open Azure PowerShell en meld u aan bij uw Azure-account.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Er wordt een pop-upvenster geopend om uw Azure-account referenties in te voeren.
2. Haal de abonnement-Id's op voor uw beschik bare abonnementen.
   
    ```powershell
    Get-AzSubscription
    ```
3. Stel het juiste abonnement in met de abonnements-ID.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>De toewijzing van de virtuele machine ongedaan maken en de status instellen op gegeneraliseerd

> [!IMPORTANT] 
> U kunt geen Tags toevoegen, bewerken of verwijderen van een virtuele machine nadat deze is gemarkeerd als gegeneraliseerd. Als u een tag wilt toevoegen aan de VM, moet u ervoor zorgen dat u de Tags toevoegt voordat u deze markeert als gegeneraliseerd.
> 

1. De toewijzing van de VM-resources ongedaan maken.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    De *status* van de virtuele machine in de Azure Portal verandert van **gestopt** in **gestopt (toewijzing opgeheven)**.
2. Stel de status van de virtuele machine in op **gegeneraliseerd**. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Controleer de status van de virtuele machine. Voor de sectie **OSState/generalis** voor de virtuele machine moet de **DisplayStatus** zijn ingesteld op **VM gegeneraliseerd**.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>De installatiekopie maken

Maak met behulp van deze opdracht een niet-beheerde installatie kopie van een virtuele machine in de doel opslag container. De installatie kopie wordt gemaakt in hetzelfde opslag account als de oorspronkelijke virtuele machine. Met de `-Path` para meter wordt een kopie van de JSON-sjabloon voor de bron-VM opgeslagen op de lokale computer. De `-DestinationContainerName` para meter is de naam van de container die u in uw afbeeldingen wilt opslaan. Als de container niet bestaat, wordt deze voor u gemaakt.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
U kunt de URL van uw installatie kopie ophalen uit de sjabloon voor het JSON-bestand. Ga **naar de**  >  sectie**storageProfile**  >  **osDisk**  >  **image**  >  **uri** van osDisk voor het volledige pad van de installatie kopie. De URL van de afbeelding ziet er als volgt uit: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd` .
   
U kunt ook de URI in de portal controleren. De installatie kopie wordt gekopieerd naar een container met de naam **System** in uw opslag account. 

## <a name="create-a-vm-from-the-image"></a>Maak een VM vanuit de installatiekopie

U kunt nu een of meer virtuele machines maken op basis van de niet-beheerde installatie kopie.

### <a name="set-the-uri-of-the-vhd"></a>De URI van de VHD instellen

De URI voor de VHD die moet worden gebruikt, heeft de volgende indeling: https://**mystorageaccount**. blob.core.Windows.net/**mycontainer** / **MyVhdName**. VHD. In dit voor beeld bevindt de VHD met de naam **myVHD** zich in het opslag account **mystorageaccount** in de container **mycontainer**.

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
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Een openbaar IP-adres en een netwerk interface maken
Om te kunnen communiceren met de virtuele machine in het virtuele netwerk, hebt u een [openbaar IP-adres](../../virtual-network/public-ip-addresses.md) en een netwerkinterface nodig.

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
De volgende Power shell voltooit de configuraties van de virtuele machine en maakt gebruik van onbeheerde installatie kopieën als bron voor de nieuwe installatie.

</br>

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

### <a name="verify-that-the-vm-was-created"></a>Controleren of de virtuele machine is gemaakt
Als u klaar bent, ziet u de zojuist gemaakte vm in de [Azure Portal](https://portal.azure.com) onder **Browse**  >  **virtuele machines**bladeren of met behulp van de volgende Power shell-opdrachten:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Volgende stappen
Zie [virtuele machines beheren met Azure Resource Manager en Power shell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie over het beheren van uw nieuwe virtuele machine met Azure PowerShell.
