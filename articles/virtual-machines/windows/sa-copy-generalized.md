---
title: Een onbeheerde afbeelding van een gegeneraliseerde vm in Azure maken
description: Maak een onmanged afbeelding van een gegeneraliseerde Windows VM om te gebruiken om meerdere kopieën van een VM in Azure te maken.
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
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: f25968fb74f0f10b1d498866c036dd04d4d5d134
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073380"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Een onbeheerde VM-afbeelding maken op basis van een Azure VM

Dit artikel gaat over het gebruik van opslagaccounts. We raden u aan beheerde schijven en beheerde afbeeldingen te gebruiken in plaats van een opslagaccount. Zie [Een beheerde afbeelding van een gegeneraliseerde vm vastleggen in Azure](capture-image-resource.md)voor meer informatie.

In dit artikel ziet u hoe u Azure PowerShell gebruiken om een afbeelding van een gegeneraliseerde Azure VM te maken met behulp van een opslagaccount. U de afbeelding vervolgens gebruiken om een andere virtuele machine te maken. De afbeelding bevat de OS-schijf en de gegevensschijven die aan de virtuele machine zijn gekoppeld. De afbeelding bevat niet de virtuele netwerkbronnen, dus u moet deze bronnen instellen wanneer u de nieuwe virtuele vm maakt. 

 

## <a name="generalize-the-vm"></a>De VM generaliseren 
In deze sectie ziet u hoe u uw virtuele Windows-machine generaliseert voor gebruik als afbeelding. Generaliseren van een VM verwijdert onder andere al uw persoonlijke accountgegevens en bereidt de machine voor om als afbeelding te worden gebruikt. Raadpleeg [Sysprep gebruiken: een inleiding](https://technet.microsoft.com/library/bb457073.aspx) voor meer informatie over Sysprep.

Controleer of de serverrollen die op de machine worden uitgevoerd, worden ondersteund door Sysprep. Zie [Sysprep-ondersteuning voor serverrollen voor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) meer informatie

> [!IMPORTANT]
> Als u uw VHD voor de eerste keer uploadt naar Azure, controleert u of u [uw vm](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) hebt voorbereid voordat u Sysprep uitvoert. 
> 
> 

U ook een Linux-VM generaliseren met behulp van `sudo waagent -deprovision+user` PowerShell en vervolgens PowerShell gebruiken om de VM vast te leggen. Zie [Hoe u een virtuele Linux-machine](../linux/capture-image.md)generaliseren en vastleggen met de Azure CLI voor informatie over het gebruik van de CLI om een VM vast te leggen.


1. Meld u aan bij de Windows-VM.
2. Open het venster met de opdrachtprompt als beheerder. Wijzig de directory in **%windir%\system32\sysprep** en voer dan `sysprep.exe`run uit.
3. In het dialoogvenster **Hulpprogramma voor systeemvoorbereiding** selecteert u **OOBE (Out-of-Box Experience) van systeem starten** en zorgt u dat het selectievakje **Generaliseren** is ingeschakeld.
4. Selecteer **Afsluiten**opties in **Afsluiten opties**.
5. Klik op **OK**.
   
    ![Sysprep starten](./media/upload-generalized-managed/sysprepgeneral.png)
6. Wanneer Sysprep is voltooid, wordt de virtuele machine afgesloten. 

> [!IMPORTANT]
> Start de VM pas opnieuw op als u klaar bent met het uploaden van de VHD naar Azure of het maken van een afbeelding van de VM. Als de VM per ongeluk opnieuw wordt gestart, voert u Sysprep uit om deze opnieuw te generaliseren.
> 
> 

## <a name="log-in-to-azure-powershell"></a>Aanmelden bij Azure PowerShell
1. Open Azure PowerShell en meld u aan bij uw Azure-account.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Er wordt een pop-upvenster geopend voor het invoeren van uw Azure-accountreferenties.
2. Ontvang de abonnements-id's voor uw beschikbare abonnementen.
   
    ```powershell
    Get-AzSubscription
    ```
3. Stel het juiste abonnement in met de abonnements-ID.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Detoewijzen van de VM en de status instellen op gegeneraliseerd

> [!IMPORTANT] 
> U geen tags uit een virtuele machine toevoegen, bewerken of verwijderen nadat deze is gemarkeerd als algemeen. Als u een tag aan de virtuele machine wilt toevoegen, moet u de tags toevoegen voordat u deze als algemeen markeert.
> 

1. Detoewijzing van de VM-resources.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    De *status* voor de VM in de Azure-portal verandert **van Gestopt** naar Gestopt **(deallocated).**
2. Stel de status van de virtuele machine in **op Algemeen.** 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Controleer de status van de VM. De sectie **OSState/generalized** voor de VM moet de **DisplayStatus** hebben ingesteld op **VM algemeen**.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>De installatiekopie maken

Maak met deze opdracht een onbeheerde virtuele machineafbeelding in de opslagcontainer van de bestemming. De afbeelding wordt gemaakt in hetzelfde opslagaccount als de oorspronkelijke virtuele machine. De `-Path` parameter slaat een kopie van de JSON-sjabloon voor de bron-VM op uw lokale computer op. De `-DestinationContainerName` parameter is de naam van de container die u wilt houden van uw afbeeldingen. Als de container niet bestaat, wordt deze voor u gemaakt.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
U de URL van uw afbeelding ophalen via de JSON-bestandssjabloon. Ga naar de **resources** > **storageProfile** > **osDisk** > **image** > **uri** sectie voor het volledige pad van uw afbeelding. De URL van de `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`afbeelding ziet eruit als: .
   
U ook de URI in de portal verifiëren. De afbeelding wordt gekopieerd naar een container met de naam **systeem** in uw opslagaccount. 

## <a name="create-a-vm-from-the-image"></a>Maak een VM vanuit de installatiekopie

Nu u een of meer VM's maken van de niet-beheerde afbeelding.

### <a name="set-the-uri-of-the-vhd"></a>Stel de URI van de VHD in

De URI voor de VHD te gebruiken is in het formaat: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. In dit voorbeeld de VHD met de naam **myVHD** is in de opslag rekening **mystorageaccount** in de container **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
Maak het vNet en subnet van het [virtuele netwerk.](../../virtual-network/virtual-networks-overview.md)

1. Maak het subnet. In het volgende voorbeeld wordt een subnet met de naam **mySubnet** gemaakt in de brongroep **myResourceGroup** met het adresvoorvoegsel van **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Maak het virtuele netwerk. In het volgende voorbeeld wordt een virtueel netwerk met de naam **myVnet** in de **West-Amerikaanse** locatie gemaakt met het adresvoorvoegsel van **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Een openbaar IP-adres en netwerkinterface maken
Om te kunnen communiceren met de virtuele machine in het virtuele netwerk, hebt u een [openbaar IP-adres](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) en een netwerkinterface nodig.

1. Een openbaar IP-adres maken. In dit voorbeeld wordt een openbaar IP-adres met de naam **myPip gemaakt.** 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Maak de NIC. In dit voorbeeld wordt een NIC met de naam **myNic .** 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>De netwerkbeveiligingsgroep en een RDP-regel maken
Als u inloggen op uw VM met RDP, moet u een beveiligingsregel hebben die RDP-toegang op poort 3389 toestaat. 

In dit voorbeeld wordt een NSG met de naam **myNsg** met de naam **myRdpRule** een regel met rdp-verkeer via poort 3389. Zie Poorten openen voor [een VM in Azure met PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie over NSG's.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Een variabele maken voor het virtuele netwerk
Maak een variabele voor het voltooide virtuele netwerk. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>De virtuele machine maken
De volgende PowerShell voltooit de configuraties van de virtuele machine en gebruikt onbeheerde afbeelding als bron voor de nieuwe installatie.

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

### <a name="verify-that-the-vm-was-created"></a>Controleren of de VM is gemaakt
Als u klaar bent, ziet u de nieuw gemaakte VM in de [Azure-portal](https://portal.azure.com) onder**Virtuele machines** **bladeren** > of met behulp van de volgende PowerShell-opdrachten:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Volgende stappen
Zie Virtuele machines beheren met Azure [Resource Manager en PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)als u uw nieuwe virtuele machine wilt beheren met Azure PowerShell.


