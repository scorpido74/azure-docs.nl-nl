---
title: 'PowerShell: nabijheidsplaatsingsgroepen gebruiken'
description: Meer informatie over het maken en gebruiken van nabijheidsplaatsingsgroepen met Azure PowerShell.
services: virtual-machines
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.openlocfilehash: f69e245d72a63b942896cdd9f4a2225cb4c1706d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208522"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>VM's implementeren voor nabijheidsplaatsingsgroepen met PowerShell


Als u VM's zo dicht mogelijk wilt plaatsen en de laagst mogelijke latentie wilt bereiken, moet u deze implementeren binnen een [plaatsingsgroep voor de nabijheid.](co-location.md#proximity-placement-groups)

Een plaatsingsgroep voor nabijheid is een logische groepering die wordt gebruikt om ervoor te zorgen dat Azure-rekenbronnen fysiek dicht bij elkaar staan. Plaatsingsgroepen voor nabijheidzijn handig voor workloads waarbij lage latentie een vereiste is.


## <a name="create-a-proximity-placement-group"></a>Een nabijheidsplaatsingsgroep maken
Maak een plaatsingsgroep voor nabijheid met de cmdlet [Nieuw-AzProximityPlacementGroep.](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Plaatsingsgroepen voor nabijheid weergeven

U alle nabijheidsplaatsingsgroepen weergeven met de cmdlet [Get-AzProximityPlacementGroup.](/powershell/module/az.compute/get-azproximityplacementgroup)

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Een virtuele machine maken

Maak een VM in de `-ProximityPlacementGroup $ppg.Id` plaatsingsgroep in de nabijheid met de verwijzing naar de groeps-id voor plaatsing in de nabijheid wanneer u [Nieuw-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) gebruikt om de VM te maken.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

U de VM in de plaatsingsgroep zien met [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Een bestaande virtuele machine verplaatsen naar een plaatsingsgroep voor nabijheid

U ook een bestaande vm toevoegen aan een plaatsingsgroep voor nabijheid. U moet eerst stoppen met de VM, vervolgens de VM bijwerken en opnieuw starten.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Een bestaande virtuele machine uit een plaatsingsgroep voor nabijheid verplaatsen

Als u een VM uit een plaatsingsgroep voor nabijheid wilt verwijderen, moet u eerst de VM stoppen en vervolgens de VM bijwerken en opnieuw opstarten.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>Beschikbaarheidssets
U ook een beschikbaarheidsset maken in uw nabijheidsplaatsingsgroep. Gebruik dezelfde `-ProximityPlacementGroup` parameter met de cmdlet [Nieuw-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) om een beschikbaarheidsset te maken en alle VM's die in de beschikbaarheidsset zijn gemaakt, worden ook in dezelfde plaatsingsgroep voor nabijheid gemaakt.

Als u een bestaande beschikbaarheidsset wilt toevoegen of verwijderen aan een groep nabijheidsplaatsing, moet u eerst alle VM's in de beschikbaarheidsset stoppen. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Een bestaande beschikbaarheidsset verplaatsen naar een groep nabijheidsplaatsing

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Een bestaande beschikbaarheid uit een nabijheidsplaatsingsgroep verplaatsen

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>Schaalsets

U ook een schaalset maken in uw plaatsingsgroep voor nabijheid. Gebruik dezelfde `-ProximityPlacementGroup` parameter met [Nieuw-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) om een schaalset te maken en alle exemplaren worden gemaakt in dezelfde plaatsingsgroep voor nabijheid.


Als u een bestaande schaalset wilt toevoegen of verwijderen aan een plaatsingsgroep voor nabijheid, moet u eerst de schaalset stoppen. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Een bestaande schaalset verplaatsen naar een plaatsingsgroep voor nabijheid

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Een bestaande schaal die is ingesteld uit een groep nabijheidsplaatsing verplaatsen

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

U de [Azure CLI](../linux/proximity-placement-groups.md) ook gebruiken om nabijheidsplaatsingsgroepen te maken.
