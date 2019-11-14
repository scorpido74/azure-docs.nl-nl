---
title: Power shell gebruiken om de grootte van een Windows-VM in azure te wijzigen
description: Wijzig de grootte van een virtuele Windows-machine die is gemaakt in het Resource Manager-implementatie model met behulp van Azure Power shell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 4b30f2fd8e095b00898e083e33c23c7c9a915b99
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073371"
---
# <a name="resize-a-windows-vm"></a>Het formaat van een Windows-VM wijzigen

In dit artikel wordt beschreven hoe u een virtuele machine naar een andere [VM-grootte](sizes.md) kunt verplaatsen met behulp van Azure Power shell.

Nadat u een virtuele machine (VM) hebt gemaakt, kunt u de VM omhoog of omlaag schalen door de VM-grootte te wijzigen. In sommige gevallen moet u eerst de toewijzing van de VM ongedaan maken. Dit kan gebeuren als de nieuwe grootte niet beschikbaar is in het hardwareprofiel dat momenteel als host fungeert voor de virtuele machine.

Als uw virtuele machine gebruikmaakt van Premium Storage, moet u ervoor zorgen dat u een **s** -versie van de grootte kiest om Premium Storage ondersteuning te krijgen. Kies bijvoorbeeld Standard_E4**s**_v3 in plaats van Standard_E4_v3.

 

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Het formaat van een Windows-virtuele machine niet wijzigen in een beschikbaarheidsset

Stel enkele variabelen in. Vervang de waarden door uw eigen gegevens.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Geef een lijst weer van de VM-grootten die beschikbaar zijn op het hardware-cluster waarop de virtuele machine wordt gehost. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Als de gewenste grootte wordt weer gegeven, voert u de volgende opdrachten uit om de grootte van de virtuele machine te wijzigen. Als de gewenste grootte niet wordt weer gegeven, gaat u naar stap 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Als de gewenste grootte niet wordt weer gegeven, voert u de volgende opdrachten uit om de toewijzing van de VM ongedaan te maken, de grootte ervan te wijzigen en de virtuele machine opnieuw op te starten. Vervang **\<newVMsize >** door de gewenste grootte.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Als u de toewijzing van de virtuele machine ongedaan hebt, worden dynamische IP-adressen vrijgegeven die aan de VM zijn toegewezen Het besturings systeem en de gegevens schijven worden niet beïnvloed. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Het formaat van een virtuele Windows-machine in een beschikbaarheidsset wijzigen

Als de nieuwe grootte voor een virtuele machine in een beschikbaarheidsset niet beschikbaar is in het hardwareprofiel dat momenteel als host fungeert voor de virtuele machine, moeten alle virtuele machines in de beschikbaarheidsset worden vrijgegeven om de grootte van de virtuele machine te wijzigen. Het is ook mogelijk dat u de grootte van andere virtuele machines in de beschikbaarheidsset moet bijwerken nadat het formaat van een virtuele machine is gewijzigd. Als u de grootte van een virtuele machine in een beschikbaarheidsset wilt wijzigen, voert u de volgende stappen uit.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Geef een lijst weer van de VM-grootten die beschikbaar zijn op het hardware-cluster waarop de virtuele machine wordt gehost. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Als de gewenste grootte wordt weer gegeven, voert u de volgende opdrachten uit om de grootte van de virtuele machine te wijzigen. Als deze niet wordt weer gegeven, gaat u naar de volgende sectie.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Als de gewenste grootte niet wordt weer gegeven, gaat u door met de volgende stappen om de toewijzing van alle virtuele machines in de beschikbaarheidsset ongedaan te maken, de virtuele machines te wijzigen en opnieuw te starten.

Stop alle virtuele machines in de beschikbaarheidsset.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Wijzig de grootte van de virtuele machines in de beschikbaarheidsset en start deze opnieuw.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Volgende stappen

Voor extra schaal baarheid voert u meerdere VM-exemplaren uit en uitschalen. Zie [Windows-machines automatisch schalen in een schaalset voor virtuele machines](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)voor meer informatie.

