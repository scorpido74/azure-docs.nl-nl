---
title: Beheerde schijf maken op basis van een momentopname - PowerShell-voorbeeld
description: 'Azure PowerShell-voorbeeldscript: een beheerde schijf maken op basis van een momentopname'
services: virtual-machines
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: f3a890083e4763bcff95a1361bed69907b80e033
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322664"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>Een beheerde schijf maken met PowerShell op basis van een momentopname 

Met dit script maakt u een beheerde schijf op basis van een momentopname. Gebruik het script om een virtuele machine te herstellen op basis van momentopnamen van het besturingssysteem en gegevensschijven. Maak beheerde OS- en gegevensschijven op basis van de respectieve momentopnamen en maak vervolgens een nieuwe virtuele machine door beheerde schijven te koppelen. U kunt ook gegevensschijven van een bestaande virtuele machine herstellen door het koppelen van gegevensschijven die zijn gemaakt op basis van momentopnamen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten om een beheerde schijf te maken op basis van een momentopname. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | Hiermee worden de eigenschappen van de momentopname opgehaald.  |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Hiermee wordt de schijfconfiguratie gemaakt die voor het maken van de schijf wordt gebruikt. Het bevat de resource-id van momentopname, de locatie (die dezelfde is als de locatie van de bovenliggende momentopname) en het opslagtype.  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Hiermee maakt u een schijf die de schijfconfiguratie, de schijfnaam en de naam van de resourcegroep als parameters gebruikt. |


## <a name="next-steps"></a>Volgende stappen

[Een virtuele machine maken op basis van een beheerde schijf](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

U kunt extra PowerShell-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Windows-VM's](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
