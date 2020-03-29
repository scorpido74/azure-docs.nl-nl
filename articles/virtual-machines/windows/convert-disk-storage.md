---
title: Beheerde schijven opslag converteren tussen standaard en premium SSD
description: Azure-beheerde schijven converteren van Standaard naar Premium of Premium naar Standard met Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e339f0c7ca0807eec3e160eeb3464044c2ef29ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720942"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Het opslagtype van een beheerde schijf bijwerken

Er zijn vier schijftypen azure-beheerde schijven: Azure ultra SSD's (preview), premium SSD, standaard SSD en standaard HDD. U schakelen tussen de drie GA-schijftypen (premium SSD, standaard SSD en standaard HDD) op basis van uw prestatiebehoeften. U bent nog niet in staat om over te schakelen van of naar een ultra SSD, moet u een nieuwe implementeren.

Deze functionaliteit wordt niet ondersteund voor niet-beheerde schijven. Maar u [een onbeheerde schijf](convert-unmanaged-to-managed-disks.md) eenvoudig converteren naar een beheerde schijf om te kunnen schakelen tussen schijftypen.

 

## <a name="prerequisites"></a>Vereisten

* Omdat conversie een herstart van de virtuele machine (VM) vereist, moet u de migratie van uw schijfopslag plannen tijdens een reeds bestaand onderhoudsvenster.
* Als uw schijf niet wordt beheerd, [converteert u deze eerst naar een beheerde schijf,](convert-unmanaged-to-managed-disks.md) zodat u schakelen tussen opslagopties.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Schakelen tussen alle beheerde schijven van een virtuele machine tussen Premium en Standard

In dit voorbeeld ziet u hoe u alle schijven van een VM converteert van Standaard naar Premium-opslag of van Premium naar Standaardopslag. Als u premium beheerde schijven wilt gebruiken, moet uw vm een [VM-formaat](sizes.md) gebruiken dat Premium-opslag ondersteunt. In dit voorbeeld wordt ook overgenegen naar een grootte die premium opslag ondersteunt:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Schakelen tussen afzonderlijke beheerde schijven tussen Standaard en Premium

Voor uw dev/testworkload wilt u misschien een mix van Standaard- en Premium-schijven om uw kosten te verlagen. U ervoor kiezen om alleen die schijven te upgraden die betere prestaties nodig hebben. In dit voorbeeld ziet u hoe u één VM-schijf converteert van Standaard naar Premium-opslag of van Premium naar Standaardopslag. Als u premium beheerde schijven wilt gebruiken, moet uw vm een [VM-formaat](sizes.md) gebruiken dat Premium-opslag ondersteunt. In dit voorbeeld ziet u ook hoe u overschakelen naar een grootte die Premium-opslag ondersteunt:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Beheerde schijven converteren van Standaard naar Premium in de Azure-portal

Volg deze stappen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer de VM in de lijst **met virtuele machines** in de portal.
3. Als de vm niet is gestopt, selecteert u **Stoppen** boven aan het deelvenster **VM-overzicht** en wacht u tot de VM is gestopt.
3. Selecteer **Schijven** in het menu in het deelvenster voor de virtuele machine.
4. Selecteer de schijf die u wilt converteren.
5. Selecteer **Configuratie** in het menu.
6. Wijzig het **accounttype** van **Standaard HDD** naar **Premium SSD.**
7. Klik **op Opslaan**en sluit het schijfvenster.

De conversie van het schijftype is onmiddellijk. U uw vm starten na de conversie.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Managed disks schakelen tussen Standard HDD en Standard SSD 

In dit voorbeeld ziet u hoe u één VM-schijf converteert van Standard HDD naar Standard SSD of van Standard SSD naar Standard HDD:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Volgende stappen

Maak een alleen-lezen kopie van een virtuele machine met behulp van een [momentopname](snapshot-copy-managed-disk.md).
