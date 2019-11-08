---
title: 'Azure PowerShell-voorbeeldscript: een beheerde schijf maken op basis van een VHD-bestand in een opslagaccount in hetzelfde of een ander abonnement | Microsoft Docs'
description: 'Azure PowerShell-voorbeeldscript: een beheerde schijf maken op basis van een VHD-bestand in een opslagaccount in hetzelfde of een ander abonnement'
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
ms.openlocfilehash: cf9500a48f9c18910ea54a01fd1da99da746f021
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749805"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Een beheerde schijf maken op basis van een VHD-bestand in een opslagaccount in hetzelfde of een ander abonnement met PowerShell

Met dit script maakt u een beheerde schijf op basis van een VHD-bestand in een opslagaccount in hetzelfde of een ander abonnement. Gebruik dit script voor het importeren van een gespecialiseerde (geen gegeneraliseerde/op het systeem voorbereide) VHD naar een beheerde OS-schijf voor het maken van een virtuele machine. Of gebruik het script om een gegevens-VHD naar een beheerde gegevensschijf te importeren. 

Maak niet binnen korte tijd meerdere identieke beheerde schijven van een VHD-bestand. Als u beheerde schijven wilt maken van een VHD-bestand, wordt een blob-momentopname van het VHD-bestand gemaakt dat vervolgens wordt gebruikt om beheerde schijven te maken. Er kan slechts één blob-momentopname in een minuut worden gemaakt. Dit zorgt voor schijffouten vanwege bandbreedtebeperking. Om deze bandbreedtebeperking te voorkomen, maakt u een [beheerde momentopname vanuit het VHD-bestand](virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) en gebruikt u de beheerde momentopname om vervolgens in korte tijd meerdere beheerde schijven te maken. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten om een beheerde schijf te maken op basis van een VHD in een ander abonnement. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Hiermee wordt de schijfconfiguratie gemaakt die voor het maken van de schijf wordt gebruikt. Dit omvat opslagtype, locatie, resource-ID van het opslagaccount waarin de bovenliggende VHD is opgeslagen, VHD-URI van de bovenliggende VHD. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Hiermee maakt u een schijf die de schijfconfiguratie, de schijfnaam en de naam van de resourcegroep als parameters gebruikt. |

## <a name="next-steps"></a>Volgende stappen

[Een virtuele machine maken door een beheerde schijf te koppelen als besturingssysteemschijf](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

U kunt extra PowerShell-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Windows-VM's](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
