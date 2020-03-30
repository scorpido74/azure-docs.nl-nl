---
title: Het formaat van een Windows-vm in Azure wijzigen
description: Wijzig de VM-grootte die wordt gebruikt voor een virtuele Azure-machine.
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
ms.date: 01/13/2020
ms.author: cynthn
ms.openlocfilehash: 6718804d4635edb2628b53017ab9d377928afad8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941722"
---
# <a name="resize-a-windows-vm"></a>Het formaat van een Windows-VM wijzigen

In dit artikel ziet u hoe u een vm naar een andere [VM-grootte verplaatst.](sizes.md)

Nadat u een virtuele machine (VM) hebt gemaakt, u de vm omhoog of omlaag schalen door de VM-grootte te wijzigen. In sommige gevallen moet u de VM eerst deallocateeren. Dit kan gebeuren als de nieuwe grootte niet beschikbaar is op het hardwarecluster dat momenteel de VM host.

Als uw vm Premium Storage gebruikt, moet u ervoor zorgen dat u een **s-versie** van het formaat kiest om premiumopslagondersteuning te krijgen. Kies bijvoorbeeld Standard_E4 **_v3**in plaats van Standard_E4_v3.

## <a name="use-the-portal"></a>Gebruik de portal

1. Open de [Azure Portal](https://portal.azure.com).
1. Open de pagina voor de virtuele machine.
1. Selecteer **Grootte**in het linkermenu .
1. Kies een nieuw formaat in de lijst met beschikbare formaten en selecteer **Formaat wijzigen**.


Als de virtuele machine momenteel actief is, wordt deze opnieuw gestart door de grootte te wijzigen. Het stoppen van de virtuele machine kan extra maten onthullen.

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>PowerShell gebruiken om het formaat van een vm te wijzigen die niet in een beschikbaarheidsset is

Stel een aantal variabelen in. Vervang de waarden door uw eigen gegevens.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Vermeld de VM-formaten die beschikbaar zijn in het hardwarecluster waar de VM wordt gehost. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Als de gewenste grootte wordt weergegeven, voert u de volgende opdrachten uit om het formaat van de virtuele machine te wijzigen. Als de gewenste maat niet wordt vermeld, ga dan verder naar stap 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Als de gewenste grootte niet wordt weergegeven, voert u de volgende opdrachten uit om de vm uit te wijzen, het formaat ervan te wijzigen en de VM opnieuw te starten. Vervang ** \<de nieuweVMsize->** door de gewenste grootte.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Als u de VM deallocatert, worden dynamische IP-adressen die aan de vm zijn toegewezen, vrijgegeven. Het besturingssysteem en de gegevensschijven worden niet beïnvloed. 
> 
> 

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>PowerShell gebruiken om het formaat van een virtuele machine in een beschikbaarheidsset te wijzigen

Als de nieuwe grootte voor een VM in een beschikbaarheidsset niet beschikbaar is op het hardwarecluster dat momenteel als host van de VM is gehost, moeten alle VM's in de beschikbaarheidsset worden verplaatst om het formaat van de VM te wijzigen. Mogelijk moet u ook de grootte van andere VM's bijwerken in de beschikbaarheidsset nadat het formaat van één virtuele machine is gewijzigd. Voer de volgende stappen uit om het formaat van een virtuele machine in een beschikbaarheidsset te wijzigen.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Vermeld de VM-formaten die beschikbaar zijn in het hardwarecluster waar de VM wordt gehost. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Als de gewenste grootte wordt weergegeven, voert u de volgende opdrachten uit om het formaat van de virtuele machine te wijzigen. Als deze niet wordt vermeld, gaat u naar de volgende sectie.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Als de gewenste grootte niet wordt weergegeven, gaat u verder met de volgende stappen om alle VM's in de beschikbaarheidsset te detoewijzen, het formaat van VM's te wijzigen en deze opnieuw te starten.

Stop alle VM's in de beschikbaarheidsset.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Het formaat en het opnieuw starten van de VM's in de beschikbaarheidsset wijzigen.
   
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

Voer voor extra schaalbaarheid meerdere VM-exemplaren uit en schaal t.a.v. uit. Zie [Windows-machines automatisch schalen in een virtuele machineschaalset](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)voor meer informatie.

