---
title: Een gegevensschijf koppelen aan een Windows-vm in Azure met PowerShell
description: Een nieuwe of bestaande gegevensschijf koppelen aan een Windows-vm met PowerShell met het implementatiemodel Resource Manager.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ce995a84d2290845e83416caf9c8b0004242eed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267753"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Een gegevensschijf koppelen aan een Windows-vm met PowerShell

In dit artikel ziet u hoe u zowel nieuwe als bestaande schijven aan een virtuele Windows-machine koppelt met PowerShell. 

Bekijk eerst deze tips:

* De grootte van de virtuele machine bepaalt hoeveel gegevensschijven u koppelen. Zie [Grootte voor virtuele machines voor](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)meer informatie .
* Om premium SSD's te gebruiken, heb je een [premium VM-type met opslag](sizes-memory.md)nodig, zoals de virtuele machine uit de DS-serie of gs-serie.

Dit artikel maakt gebruik van PowerShell in de [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u de Cloud Shell wilt openen, selecteert **u Probeer deze** boven aan een codeblok.

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Een lege gegevensschijf toevoegen aan een virtuele machine

In dit voorbeeld ziet u hoe u een lege gegevensschijf toevoegt aan een bestaande virtuele machine.

### <a name="using-managed-disks"></a>Beheerde schijven gebruiken

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Beheerde schijven gebruiken in een beschikbaarheidszone

Als u een schijf wilt maken in een beschikbaarheidszone, gebruikt u [Nieuw-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig) met de `-Zone` parameter. In het volgende voorbeeld wordt een schijf gemaakt in zone *1*.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>De schijf initialiseren

Nadat u een lege schijf hebt toegevoegd, moet u deze initialiseren. Als u de schijf wilt initialiseren, u zich aanmelden bij een virtuele machine en schijfbeheer gebruiken. Als u [WinRM](https://docs.microsoft.com/windows/desktop/WinRM/portal) en een certificaat op de VM hebt ingeschakeld toen u deze hebt gemaakt, u externe PowerShell gebruiken om de schijf te initialiseren. U ook een aangepaste scriptextensie gebruiken:

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

Het scriptbestand kan code bevatten om de schijven te initialiseren, bijvoorbeeld:

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Een bestaande gegevensschijf aan een vm koppelen

U een bestaande beheerde schijf als gegevensschijf aan een virtuele machine koppelen.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Volgende stappen

U beheerde schijven ook implementeren met behulp van sjablonen. Zie [Beheerde schijven gebruiken in Azure Resource Manager-sjablonen](using-managed-disks-template-deployments.md) of de [snelstartsjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk) voor het implementeren van meerdere gegevensschijven voor meer informatie.
