---
title: VHD van beheerde schijf naar account in een andere regio (Linux) - PowerShell
description: 'Voorbeeld van Azure PowerShell-script: VHD van een beheerde schijf exporteren/kopiëren naar een opslagaccount in dezelfde of een andere regio'
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.openlocfilehash: 125779fcc547aa725f8c218663be23c177b42b97
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87028631"
---
# <a name="exportcopy-the-vhd-of-a-managed-disk-to-a-storage-account-in-different-region-with-powershell-linux"></a>VHD van een beheerde schijf met PowerShell exporteren/kopiëren naar een opslagaccount in een andere regio (Linux)

Met dit script wordt de VHD van een beheerde schijf geëxporteerd naar een opslagaccount in een andere regio. Eerst wordt de SAS-URI van de beheerde schijf gegenereerd, waarna deze wordt gebruikt om de onderliggende VHD naar een opslagaccount in een andere regio te kopiëren. Gebruik dit script om beheerde schijven naar een andere regio te kopiëren voor regionale uitbreiding.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.ps1 "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het genereren van de SAS-URI voor een beheerde schijf en kopieert de onderliggende VHD naar een opslagaccount met behulp van de SAS-URI. Elke opdracht in de tabel is een koppeling naar de documentatie voor de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Grant-AzDiskAccess](/powershell/module/az.compute/grant-azdiskaccess) | Hiermee genereert u de SAS-URI voor een beheerde schijf die wordt gebruikt voor het kopiëren van de onderliggende VHD naar een opslagaccount. |
| [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext) | Hiermee maakt u de context voor een opslagaccount aan de hand van de accountnaam en de sleutel. Deze context kan worden gebruikt voor het uitvoeren van lees-/schrijfbewerkingen op het opslagaccount. |
| [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) | Hiermee kopieert u de onderliggende VHD van een momentopname naar een opslagaccount. |

## <a name="next-steps"></a>Volgende stappen

[Een beheerde schijf maken op basis van een VHD](virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Een virtuele machine maken op basis van een beheerde schijf](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

U kunt extra PowerShell-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Linux-VM's](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
