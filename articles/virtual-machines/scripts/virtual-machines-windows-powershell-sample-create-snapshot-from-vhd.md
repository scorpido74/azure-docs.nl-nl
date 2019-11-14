---
title: 'Azure PowerShell-voorbeeldscript: een momentopname maken van een VHD voor het maken van meerdere identieke beheerde schijven in korte tijd'
description: 'Azure PowerShell-voorbeeldscript: een momentopname maken van een VHD voor het maken van meerdere identieke beheerde schijven in korte tijd'
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 1104cd762fb054c13d4bd1ebb5f49afef670bcc3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039037"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>Een momentopname maken van een VHD voor het maken van meerdere identieke beheerde schijven in korte tijd met PowerShell

Met dit script maakt u een momentopname van een VHD-bestand in een opslagaccount in hetzelfde of een ander abonnement. Gebruik dit script voor het importeren van een gespecialiseerde (geen gegeneraliseerde/Sysprep-voorbereide) VHD naar een momentopname en gebruik vervolgens de momentopname voor het maken van meerdere identieke beheerde schijven in korte tijd. U kunt het ook gebruiken om VHD-gegevens te importeren in een momentopname en vervolgens de momentopname gebruiken voor het maken van meerdere beheerde schijven in korte tijd. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten om een beheerde schijf te maken op basis van een VHD in een ander abonnement. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Hiermee wordt de schijfconfiguratie gemaakt die voor het maken van de schijf wordt gebruikt. Dit omvat opslagtype, locatie, resource-ID van het opslagaccount waarin de bovenliggende VHD is opgeslagen en de VHD-URI van de bovenliggende VHD. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Hiermee maakt u een schijf die de schijfconfiguratie, de schijfnaam en de naam van de resourcegroep als parameters gebruikt. |

## <a name="next-steps"></a>Volgende stappen

[Een beheerde schijf maken op basis van een momentopname](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Een virtuele machine maken door een beheerde schijf te koppelen als besturingssysteemschijf](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

U kunt extra PowerShell-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Windows-VM's](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
