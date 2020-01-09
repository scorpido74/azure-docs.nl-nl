---
title: Een moment opname van een beheerde schijf kopiëren naar een abonnement-Power shell-voor beeld
description: 'Azure PowerShell script voorbeeld: een moment opname van een beheerde schijf kopiëren (of verplaatsen) naar hetzelfde of een ander abonnement'
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 65a3e39206864f10c41e79ba6b3e7a89da99dc6f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463773"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Met PowerShell een momentopname van een beheerde schijf kopiëren naar hetzelfde of een ander abonnement

Met dit script wordt een momentopname van een beheerde schijf gekopieerd naar hetzelfde of een ander abonnement. Gebruik dit script voor de volgende scenario's:

1. Migreer een moment opname in Premium Storage (Premium_LRS) naar de standaard opslag (Standard_LRS of Standard_ZRS) om uw kosten te verlagen.
1. Migreer een moment opname van lokaal redundante opslag (Premium_LRS, Standard_LRS) naar zone redundante opslag (Standard_ZRS) om te profiteren van de hogere betrouw baarheid van ZRS-opslag.
1. Een moment opname verplaatsen naar een ander abonnement in dezelfde regio voor een langere Bewaar periode.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om een momentopname te maken in het doelabonnement met behulp van de id van de bronmomentopname. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | Hiermee maakt u de configuratie voor momentopnamen die wordt gebruikt voor het maken van een momentopname. De opdracht bevat de resource-id van de bovenliggende momentopname en de locatie, die dezelfde is als de locatie van de bovenliggende momentopname.  |
| [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Hiermee maakt u een momentopname aan de hand van de configuratie voor momentopnamen, de naam voor de momentopname en de naam van de resourcegroep die als parameters zijn doorgegeven. |

## <a name="next-steps"></a>Volgende stappen

[Een virtuele machine maken van een momentopname](./virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

U kunt extra PowerShell-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Linux-VM's](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).