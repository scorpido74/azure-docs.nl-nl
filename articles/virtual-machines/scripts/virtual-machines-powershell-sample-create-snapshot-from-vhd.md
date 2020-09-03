---
title: VHD-momentopname van het maken van veel identieke beheerde schijven (Windows) - PowerShell
description: 'Azure PowerShell-voorbeeldscript: een momentopname maken van een VHD voor het maken van meerdere identieke beheerde schijven in korte tijd'
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
ms.openlocfilehash: 47ade94ad2e0be0ce44d346336c6c7eea4fca78b
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322681"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell-windows"></a>Een momentopname van een VHD maken voor het maken van meerdere identieke beheerde schijven in korte tijd met PowerShell (Windows)

Met dit script maakt u een momentopname van een VHD-bestand in een opslagaccount in hetzelfde of een ander abonnement. Gebruik dit script voor het importeren van een gespecialiseerde (geen gegeneraliseerde/Sysprep-voorbereide) VHD naar een momentopname en gebruik vervolgens de momentopname voor het maken van meerdere identieke beheerde schijven in korte tijd. U kunt het ook gebruiken om VHD-gegevens te importeren in een momentopname en vervolgens de momentopname gebruiken voor het maken van meerdere beheerde schijven in korte tijd. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="next-steps"></a>Volgende stappen

[Een beheerde schijf maken op basis van een momentopname](virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Een virtuele machine maken door een beheerde schijf te koppelen als besturingssysteemschijf](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md)

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

U kunt extra PowerShell-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Windows-VM's](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
