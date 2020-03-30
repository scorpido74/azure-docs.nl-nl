---
title: Een gegeneralismeer VHD om meerdere VM's in Azure te maken
description: Upload een gegeneraliseerde VHD naar een Azure-opslagaccount om een Windows-vm te maken die u gebruiken met het implementatiemodel resourcebeheer.
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
ms.openlocfilehash: 933b648f15418c4838d3da1ea8379267765c784b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073332"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Een gegeneraliseerde VHD uploaden naar Azure om een nieuwe virtuele machine te maken

In dit onderwerp wordt een gegeneraliseerde onbeheerde schijf naar een opslagaccount geüpload en vervolgens een nieuwe virtuele machine gemaakt met de geüploade schijf. Een gegeneraliseerde VHD-afbeelding heeft al uw persoonlijke accountgegevens verwijderd met behulp van Sysprep. 

Zie [Een VM maken van een gespecialiseerde VHD](sa-create-vm-specialized.md)als u een VM wilt maken op basis van een gespecialiseerde VHD.

Dit onderwerp gaat over het gebruik van opslagaccounts, maar we raden klanten aan om in plaats daarvan beheerde schijven te gebruiken. Zie [Een nieuwe vm maken van een gegeneraliseerde VHD die naar Azure is geüpload met beheerde schijven](upload-generalized-managed.md)voor een volledige doorloop van hoe u een nieuwe virtuele machine voorbereiden, uploaden en maken met beheerde schijven.

 

## <a name="prepare-the-vm"></a>De virtuele machine voorbereiden

Een gegeneraliseerde VHD heeft al uw persoonlijke accountgegevens verwijderd met behulp van Sysprep. Als u van plan bent om de VHD te gebruiken als een afbeelding om nieuwe VM's van te maken, moet u:
  
  * [Bereid een Windows VHD voor om te uploaden naar Azure.](prepare-for-upload-vhd-image.md) 
  * Generaliseer de virtuele machine met Behulp van Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Een virtuele Windows-machine generaliseren met Sysprep
In deze sectie ziet u hoe u uw virtuele Windows-machine generaliseert voor gebruik als afbeelding. Sysprep verwijdert onder meer al uw persoonlijke accountinformatie en de machine wordt voorbereid om als een installatiekopie te worden gebruikt. Raadpleeg [Sysprep gebruiken: een inleiding](https://technet.microsoft.com/library/bb457073.aspx) voor meer informatie over Sysprep.

Controleer of de serverrollen die op de machine worden uitgevoerd, worden ondersteund door Sysprep. Zie [Sysprep-ondersteuning voor serverrollen voor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) meer informatie

> [!IMPORTANT]
> Als u Sysprep uitvoert voordat u uw VHD voor de eerste keer uploadt naar Azure, controleert u of u [uw vm](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) hebt voorbereid voordat u Sysprep uitvoert. 
> 
> 

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


## <a name="upload-the-vhd"></a>De VHD uploaden

Upload de VHD naar een Azure-opslagaccount.

### <a name="log-in-to-azure"></a>Meld u aan bij Azure.
Als PowerShell-versie 1.4 of hoger nog niet is geïnstalleerd, leest u [Azure PowerShell installeren en configureren.](/powershell/azure/overview)

1. Open Azure PowerShell en meld u aan bij uw Azure-account. Er wordt een pop-upvenster geopend voor het invoeren van uw Azure-accountreferenties.
   
    ```powershell
    Connect-AzAccount
    ```
2. Ontvang de abonnements-id's voor uw beschikbare abonnementen.
   
    ```powershell
    Get-AzSubscription
    ```
3. Stel het juiste abonnement in met de abonnements-ID. Vervang `<subscriptionID>` door de ID van het juiste abonnement.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Het opslagaccount aanschaffen
U hebt een opslagaccount in Azure nodig om de geüploade VM-afbeelding op te slaan. U een bestaand opslagaccount gebruiken of een nieuw account maken. 

Als u de beschikbare opslagaccounts wilt weergeven, typt u het:

```powershell
Get-AzStorageAccount
```

Als u een bestaand opslagaccount wilt gebruiken, gaat u door naar de sectie Vm-afbeelding uploaden.

Als u een opslagaccount wilt maken, voert u de volgende stappen uit:

1. U hebt de naam nodig van de resourcegroep waar het opslagaccount moet worden gemaakt. Als u alle brongroepen wilt vinden die zich in uw abonnement bevinden, typt u het:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Als u een resourcegroep met de naam **myResourceGroup** in de regio **West-VS** wilt maken, typt u het:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Maak een opslagaccount met de naam **mystorageaccount** in deze brongroep met de cmdlet [Nieuw-AzStorageAccount:](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>De upload starten 

Gebruik de [cmdlet Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) om de afbeelding te uploaden naar een container in uw opslagaccount. In dit voorbeeld wordt het bestand **myVHD.vhd** geüpload `"C:\Users\Public\Documents\Virtual hard disks\"` naar een opslagaccount met de naam **mystorageaccount** in de **brongroep myResourceGroup.** Het bestand zal worden geplaatst in de container met de naam **mycontainer** en de nieuwe bestandsnaam zal **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Als dit lukt, krijgt u een antwoord dat op dit lijkt:

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

Afhankelijk van uw netwerkverbinding en de grootte van uw VHD-bestand kan het even duren voordat deze opdracht is voltooid.


## <a name="create-a-new-vm"></a>Een nieuwe VM maakt 

U nu de geüploade VHD gebruiken om een nieuwe virtuele machine te maken. 

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
    $location = "WestUS"
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
In het volgende PowerShell-script ziet u hoe u de configuraties van de virtuele machine instelt en de geüploade VM-afbeelding gebruikt als bron voor de nieuwe installatie.



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

## <a name="verify-that-the-vm-was-created"></a>Controleren of de VM is gemaakt
Als u klaar bent, ziet u de nieuw gemaakte VM in de [Azure-portal](https://portal.azure.com) onder**Virtuele machines** **bladeren** > of met behulp van de volgende PowerShell-opdrachten:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Volgende stappen
Zie Virtuele machines beheren met Azure [Resource Manager en PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)als u uw nieuwe virtuele machine wilt beheren met Azure PowerShell.


