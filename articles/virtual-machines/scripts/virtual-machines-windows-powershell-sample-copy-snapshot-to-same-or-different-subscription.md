---
title: Een momentopname van een beheerde schijf naar een abonnement kopiëren (Windows) - PowerShell
description: 'Azure PowerShell-voorbeeldscript: een momentopname van een beheerde schijf kopiëren (verplaatsen) naar hetzelfde of een ander abonnement'
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/28/2019
ms.author: ramankum
ms.openlocfilehash: 0dadb4102a16d63b028e7202e2d7a94f135d06bb
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501057"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell-windows"></a>Met PowerShell een momentopname van een beheerde schijf kopiëren naar hetzelfde of een ander abonnement (Windows)

Met dit script wordt een momentopname van een beheerde schijf gekopieerd naar hetzelfde of een ander abonnement. Gebruik dit script in de volgende scenario's:

1. Migreer een momentopname in Premium-opslag (Premium_LRS) naar de Standard-opslag (Standard_LRS of Standard_ZRS) om uw kosten te verlagen.
1. Migreer een momentopname van lokaal redundante opslag (Premium_LRS, Standard_LRS) naar zone-redundante opslag (Standard_ZRS) om te profiteren van de hogere betrouwbaarheid van ZRS-opslag.
1. Verplaats een momentopname naar een ander abonnement in dezelfde regio voor langere gegevensretentie.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om een momentopname te maken in het doelabonnement met behulp van de id van de bronmomentopname. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig) | Hiermee maakt u de configuratie voor momentopnamen die wordt gebruikt voor het maken van een momentopname. De opdracht bevat de resource-id van de bovenliggende momentopname en de locatie, die dezelfde is als de locatie van de bovenliggende momentopname.  |
| [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) | Hiermee maakt u een momentopname aan de hand van de configuratie voor momentopnamen, de naam voor de momentopname en de naam van de resourcegroep die als parameters zijn doorgegeven. |

## <a name="next-steps"></a>Volgende stappen

[Een virtuele machine maken van een momentopname](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

U kunt extra PowerShell-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Windows-VM's](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
