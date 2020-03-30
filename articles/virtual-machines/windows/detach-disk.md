---
title: Een gegevensschijf loskoppelen van een Windows-vm - Azure
description: Maak een gegevensschijf los van een virtuele machine in Azure met behulp van het implementatiemodel ResourceBeheer.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/08/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 301f3abd26f702f3f29c8833c835ba7d0e41bcaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834608"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Een gegevensschijf loskoppelen van een virtuele Windows-machine

Wanneer u een gegevensschijf die is gekoppeld aan een virtuele machine niet meer nodig hebt, kunt u deze eenvoudig loskoppelen. Hiermee wordt de schijf uit de virtuele machine verwijderd, maar wordt deze niet uit de opslag verwijderd.

> [!WARNING]
> Als u een schijf losmaakt, wordt deze niet automatisch verwijderd. Als u zich hebt geabonneerd op Premium-opslag, blijft u opslagkosten maken voor de schijf. Zie [Prijzen en facturering bij gebruik van Premium Storage](disks-types.md#billing)voor meer informatie.

Als u de bestaande gegevens op de schijf opnieuw wilt gebruiken, kunt u de schijf opnieuw koppelen aan dezelfde of een andere virtuele machine.

 

## <a name="detach-a-data-disk-using-powershell"></a>Een gegevensschijf loskoppelen met PowerShell

U een gegevensschijf *opwarmen* met PowerShell, maar zorg ervoor dat de schijf actief wordt gebruikt voordat u deze loskoppelt van de vm.

In dit voorbeeld verwijderen we de schijf met de naam **myDisk** uit de VM **myVM** in de brongroep **myResourceGroup.** Eerst verwijdert u de schijf met de cmdlet [Remove-AzVMDataDisk.](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) Vervolgens werkt u de status van de virtuele machine bij met behulp van de [cmdlet Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) om het proces van het verwijderen van de gegevensschijf te voltooien.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

De schijf blijft in opslag, maar is niet langer aangesloten op een virtuele machine.

## <a name="detach-a-data-disk-using-the-portal"></a>Een gegevensschijf ontkoppelen via de portal

U een gegevensschijf *opwarmen,* maar zorg ervoor dat de schijf actief wordt gebruikt voordat u deze loskoppelt van de virtuele machine.

1. Selecteer **virtuele machines**in het linkermenu .
1. Selecteer de virtuele machine met de gegevensschijf die u wilt loskoppelen.
1. Selecteer **Schijven** onder **Instellingen**.
1. Selecteer Boven aan het deelvenster **Schijven** de optie **Bewerken**.
1. Selecteer Losmaken in het deelvenster **Schijven** helemaal rechts van de gegevensschijf die u wilt **loskoppelen**.
1. Selecteer **Opslaan** boven aan de pagina om uw wijzigingen op te slaan.

De schijf blijft in opslag, maar is niet langer aangesloten op een virtuele machine.

## <a name="next-steps"></a>Volgende stappen

Als u de gegevensschijf opnieuw wilt gebruiken, u deze gewoon [koppelen aan een andere virtuele machine](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
