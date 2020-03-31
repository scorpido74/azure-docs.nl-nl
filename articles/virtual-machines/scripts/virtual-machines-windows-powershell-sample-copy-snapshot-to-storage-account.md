---
title: Momentopname exporteren/kopiëren als VHD naar een opslagaccount in verschillende regio's - PowerShell-voorbeeld
description: 'Azure PowerShell-voorbeeldscript: momentopname exporteren/kopiëren als VHD naar een opslagaccount in een andere regio'
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
ms.openlocfilehash: e8a141710b9011fc46eb8df594725bf76c3d69cf
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385126"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>Beheerde momentopnamen exporteren/kopiëren als VHD naar een opslagaccount in een andere regio met PowerShell

Met dit script wordt een beheerde momentopname geëxporteerd naar een opslagaccount in een andere regio. Eerst wordt de SAS-URI van de momentopname gegenereerd, die vervolgens wordt gekopieerd naar een opslagaccount in een andere regio. Gebruik dit script voor het onderhouden van back-ups van uw beheerde schijven in een andere regio voor herstel na noodgevallen.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het genereren van de SAS-URI voor een beheerde momentopname en kopieert de momentopname naar een opslagaccount met behulp van de SAS-URI. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Grant-AzSnapshotAccess](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Genereert een SAS-URI voor een momentopname die wordt gebruikt om deze te kopiëren naar een opslagaccount. |
| [New-AzStorageContext](https://docs.microsoft.com/powershell/module/az.storage/new-azstoragecontext) | Hiermee maakt u de context voor een opslagaccount aan de hand van de accountnaam en de sleutel. Deze context kan worden gebruikt voor het uitvoeren van lees-/schrijfbewerkingen op het opslagaccount. |
| [Start-AzStorageBlobCopy](https://docs.microsoft.com/powershell/module/az.storage/start-azstorageblobcopy) | Hiermee kopieert u de onderliggende VHD van een momentopname naar een opslagaccount. |

## <a name="next-steps"></a>Volgende stappen

[Een beheerde schijf maken op basis van een VHD](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[Een virtuele machine maken van een beheerde schijf](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

U kunt extra PowerShell-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Windows-VM's](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
