---
title: VHD-momentopname van het maken van veel identieke beheerde schijven (Linux) - PowerShell
description: 'Azure PowerShell-voorbeeldscript: een momentopname maken van een VHD voor het maken van meerdere identieke beheerde schijven in korte tijd'
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
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 8a80056ed3368f6a2a5c4ad7fa31424524e824b2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079924"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell-linux"></a>Een momentopname maken van een VHD voor het maken van meerdere identieke beheerde schijven in korte tijd met PowerShell (Linux)

Met dit script maakt u een momentopname van een VHD-bestand in een opslagaccount in hetzelfde of een ander abonnement. Gebruik dit script voor het importeren van een gespecialiseerde (geen gegeneraliseerde/Sysprep-voorbereide) VHD naar een momentopname en gebruik vervolgens de momentopname voor het maken van meerdere identieke beheerde schijven in korte tijd. U kunt het ook gebruiken om VHD-gegevens te importeren in een momentopname en vervolgens de momentopname gebruiken voor het maken van meerdere beheerde schijven in korte tijd.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten om een beheerde schijf te maken op basis van een VHD in een ander abonnement. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Hiermee wordt de schijfconfiguratie gemaakt die voor het maken van de schijf wordt gebruikt. Dit omvat opslagtype, locatie, resource-ID van het opslagaccount waarin de bovenliggende VHD is opgeslagen en de VHD-URI van de bovenliggende VHD. |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Hiermee maakt u een schijf die de schijfconfiguratie, de schijfnaam en de naam van de resourcegroep als parameters gebruikt. |

## <a name="next-steps"></a>Volgende stappen

[Een beheerde schijf maken op basis van een momentopname](virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

U kunt extra PowerShell-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Linux-VM's](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
