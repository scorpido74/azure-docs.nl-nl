---
title: Een gegevens schijf loskoppelen van een Windows-VM-Azure
description: Ontkoppel een gegevens schijf van een virtuele machine in azure met behulp van het Resource Manager-implementatie model.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/08/2020
ms.author: cynthn
ms.openlocfilehash: 3fce527c1a9feb3dbf9add36577cfa5ba8349882
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659324"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Een gegevensschijf loskoppelen van een virtuele Windows-machine

Wanneer u een gegevensschijf die is gekoppeld aan een virtuele machine niet meer nodig hebt, kunt u deze eenvoudig loskoppelen. Hiermee verwijdert u de schijf van de virtuele machine, maar verwijdert u deze niet uit de opslag.

> [!WARNING]
> Als u een schijf loskoppelt, wordt deze niet automatisch verwijderd. Als u bent geabonneerd op Premium-opslag, blijven er opslag kosten in rekening worden gebracht voor de schijf. Zie [prijzen en facturering bij het gebruik van Premium Storage](disks-types.md#billing)voor meer informatie.

Als u de bestaande gegevens op de schijf opnieuw wilt gebruiken, kunt u de schijf opnieuw koppelen aan dezelfde of een andere virtuele machine.

 

## <a name="detach-a-data-disk-using-powershell"></a>Een gegevens schijf loskoppelen met Power shell

U kunt een gegevens schijf *dynamisch* verwijderen met behulp van Power shell, maar u moet er wel voor zorgen dat er niets actief wordt gebruikt op de schijf voordat u deze loskoppelt van de virtuele machine.

In dit voor beeld wordt de schijf met de naam **myDisk** verwijderd uit de VM- **myVM** in de resource groep **myResourceGroup** . Eerst verwijdert u de schijf met de cmdlet [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) . Vervolgens werkt u de status van de virtuele machine bij met de cmdlet [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) om het proces voor het verwijderen van de gegevens schijf te volt ooien.

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

De schijf blijft in de opslag, maar is niet meer gekoppeld aan een virtuele machine.

## <a name="detach-a-data-disk-using-the-portal"></a>Een gegevensschijf ontkoppelen via de portal

U kunt een gegevens schijf *dynamisch* verwijderen, maar u moet er wel voor zorgen dat er geen actieve schijven meer worden gebruikt voordat de schijf wordt losgekoppeld van de virtuele machine.

1. Selecteer in het linkermenu **virtual machines**.
1. Selecteer de virtuele machine met de gegevens schijf die u wilt loskoppelen.
1. Selecteer **Schijven** onder **Instellingen**.
1. Selecteer boven in het deel venster **schijven** de optie **bewerken**.
1. Selecteer **loskoppelen**in het deel venster **schijven** helemaal rechts van de gegevens schijf die u wilt loskoppelen.
1. Selecteer boven aan de pagina **Opslaan** om uw wijzigingen op te slaan.

De schijf blijft in de opslag, maar is niet meer gekoppeld aan een virtuele machine.

## <a name="next-steps"></a>Volgende stappen

Als u de gegevens schijf opnieuw wilt gebruiken, kunt u [deze gewoon aan een andere virtuele machine koppelen](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
