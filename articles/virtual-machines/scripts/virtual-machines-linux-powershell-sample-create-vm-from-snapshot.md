---
title: Een virtuele machine maken op basis van een momentopname (Linux) - PowerShell-voorbeeld
description: 'Azure PowerShell-voorbeeldscript: een virtuele machine maken op basis van een momentopname'
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: d6bf74b9040ec3f677c23c84f8ece22befb35d56
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87010288"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell-linux"></a>Een virtuele machine maken van een momentopname met PowerShell (Linux)

Met dit script maakt u een virtuele machine van een momentopname van een besturingssysteemschijf.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from managed os disk")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep, VM, en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het opvragen van de momentopname-eigenschappen van de beheerde schijf, het maken van een beheerde schijf aan de hand van de momentopname en het maken van een virtuele machine. Elk item in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | Hiermee haalt u een momentopname op met de naam van de momentopname. |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Hiermee maakt u een schijfconfiguratie. Deze configuratie wordt gebruikt bij het maken van de schijf. |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Hiermee maakt u een beheerde schijf. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Hiermee maakt u een VM-configuratie. Deze configuratie bevat informatie zoals de naam, het besturingssysteem en de beheerdersreferenties van de virtuele machine. De configuratie wordt gebruikt tijdens het maken van de virtuele machine. |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | Hiermee wordt de beheerde schijf als besturingssysteemschijf gekoppeld aan de virtuele machine |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Hiermee maakt u een netwerkinterface. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Hiermee maakt u een virtuele machine. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

U kunt extra PowerShell-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Linux-VM's](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
