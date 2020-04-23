---
title: Os-schijf ruilen voor een Azure VM met PowerShell '
description: Wijzig de schijf van het besturingssysteem die wordt gebruikt door een virtuele Azure-machine met PowerShell.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 566347414ffe707b1d68a61b00ba21d19ff2b1eb
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869391"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>De besturingssysteemschijf wijzigen die door een Azure VM wordt gebruikt met PowerShell

Als u een bestaande virtuele machine hebt, maar u de schijf wilt ruilen voor een back-upschijf of een andere osschijf, u Azure PowerShell gebruiken om de besturingssysteemschijven te verwisselen. U hoeft de vm niet te verwijderen en opnieuw te maken. U zelfs een beheerde schijf in een andere resourcegroep gebruiken, zolang deze nog niet in gebruik is.

 

De VM moet wel worden gestopt\deallocated, waarna de resource-id van de beheerde schijf kan worden vervangen door de resource-id van een andere beheerde schijf.

Controleer of de VM-grootte en het opslagtype compatibel zijn met de schijf die u wilt koppelen. Als de schijf die u wilt gebruiken bijvoorbeeld in Premium Storage staat, moet de VM in staat zijn om Premium-opslag te gebruiken (zoals een DS-serie). Beide schijven moeten ook dezelfde grootte hebben.

Een lijst met schijven in een resourcegroep met [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Wanneer u de naam hebt van de schijf die u wilt gebruiken, stelt u die in als de osschijf voor de VM. In dit voorbeeld wordt de VM met de naam *myVM* toegewezen en wordt de schijf met de naam *newDisk* toegewezen als de nieuwe OS-schijf. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Volgende stappen**

Zie [Momentopname een schijf](snapshot-copy-managed-disk.md)als u een kopie van een schijf wilt maken.
