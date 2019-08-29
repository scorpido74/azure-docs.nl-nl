---
title: Een beschikbaarheidsset met Vm's wijzigen | Microsoft Docs
description: Meer informatie over het wijzigen van de beschikbaarheidsset voor uw virtuele machines met behulp van Azure PowerShell en het Resource Manager-implementatie model.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/12/2019
ms.author: cynthn
ms.openlocfilehash: 0a91a80c18b04e257daa9a42fd7933351fe3a35c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080139"
---
# <a name="change-the-availability-set-for-a-windows-vm"></a>De beschikbaarheidsset voor een Windows-VM wijzigen
In de volgende stappen wordt beschreven hoe u de beschikbaarheidsset van een virtuele machine wijzigt met behulp van Azure PowerShell. Een virtuele machine kan alleen worden toegevoegd aan een beschikbaarheidsset wanneer deze wordt gemaakt. Als u de beschikbaarheidsset wilt wijzigen, moet u de virtuele machine verwijderen en vervolgens opnieuw maken. 

Dit artikel is voor het laatst getest op 2/12/2019 met behulp van de [Azure Cloud shell](https://shell.azure.com/powershell) en de [AZ Power shell-module](https://docs.microsoft.com/powershell/azure/install-az-ps) versie 1.2.0.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="change-the-availability-set"></a>De beschikbaarheidsset wijzigen 

Het volgende script geeft een voor beeld van het verzamelen van de vereiste informatie, het verwijderen van de oorspronkelijke VM en het opnieuw maken van de gegevens in een nieuwe beschikbaarheidsset.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
  
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
        {
            Add-AzVMNetworkInterface `
            -VM $newVM `
            -Id $nic.Id -Primary
            }
        else
            {
              Add-AzVMNetworkInterface `
              -VM $newVM `
              -Id $nic.Id 
                }
    }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>Volgende stappen

Voeg extra opslag ruimte toe aan uw virtuele machine door een extra [gegevens schijf](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)toe te voegen.

