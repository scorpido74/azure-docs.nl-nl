---
title: Een gegevens schijf loskoppelen van een Windows-VM-Azure | Microsoft Docs
description: Ontkoppel een gegevens schijf van een virtuele machine in azure met behulp van het Resource Manager-implementatie model.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: efbb6ccef9096ed89f6ccd16f8d3b37c9a97b278
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103214"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Een gegevens schijf loskoppelen van een virtuele Windows-machine

Wanneer u een gegevensschijf die is gekoppeld aan een virtuele machine niet meer nodig hebt, kunt u deze eenvoudig loskoppelen. Hiermee verwijdert u de schijf van de virtuele machine, maar verwijdert u deze niet uit de opslag.

> [!WARNING]
> Als u een schijf loskoppelt, wordt deze niet automatisch verwijderd. Als u bent geabonneerd op Premium-opslag, blijven er opslag kosten in rekening worden gebracht voor de schijf. Zie [prijzen en facturering bij het gebruik van Premium Storage](disks-types.md#billing)voor meer informatie.

Als u de bestaande gegevens op de schijf opnieuw wilt gebruiken, kunt u de schijf opnieuw koppelen aan dezelfde of een andere virtuele machine.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="detach-a-data-disk-using-powershell"></a>Een gegevens schijf loskoppelen met Power shell

U kunt een gegevens schijf *dynamisch* verwijderen met behulp van Power shell, maar u moet er wel voor zorgen dat er niets actief wordt gebruikt op de schijf voordat u deze loskoppelt van de virtuele machine.

In dit voor beeld wordt de schijf met de naam **myDisk** verwijderd uit de VM- **myVM** in de resource groep **myResourceGroup** . Eerst verwijdert u de schijf met de cmdlet [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) . Vervolgens werkt u de status van de virtuele machine bij met de cmdlet [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) om het proces voor het verwijderen van de gegevens schijf te volt ooien.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

De schijf blijft in de opslag, maar is niet meer gekoppeld aan een virtuele machine.

## <a name="detach-a-data-disk-using-the-portal"></a>Een gegevensschijf ontkoppelen via de portal

1. Selecteer in het linkermenu **virtual machines**.
2. Selecteer de virtuele machine met de gegevens schijf die u wilt loskoppelen en klik op **stoppen** om de VM opnieuw toe te wijzen.
3. Selecteer **schijven**in het deel venster virtuele machine.
4. Selecteer boven in het deel venster **schijven** de optie **bewerken**.
5. Klik in het deel venster **schijven** helemaal rechts van de gegevens schijf die u wilt loskoppelen op de ![knop knop afbeelding](./media/detach-disk/detach.png) loskoppelen.
5. Nadat de schijf is verwijderd, klikt u boven aan het deel venster op **Opslaan** .
6. Klik in het deel venster virtuele machine op **overzicht** en klik vervolgens op de knop **Start** boven aan het deel venster om de VM opnieuw op te starten.

De schijf blijft in de opslag, maar is niet meer gekoppeld aan een virtuele machine.

## <a name="next-steps"></a>Volgende stappen

Als u de gegevens schijf opnieuw wilt gebruiken, kunt u [deze gewoon aan een andere virtuele machine koppelen](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)