---
title: Een virtuele machine maken door een beheerde schijf als besturingssysteemschijf te koppelen (Linux) - PowerShell
description: Voorbeeld van Azure PowerShell-script - Een VM maken door een beheerde schijf te koppelen als besturingssysteemschijf
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
ms.openlocfilehash: f38d80f950bc27b6a53d43bdd42e7c325fddec39
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87010339"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell-linux"></a>Een virtuele machine maken met behulp van een bestaande beheerde besturingssysteemschijf met PowerShell (Linux)

Met dit script maakt u een virtuele machine door een bestaande beheerde schijf als besturingssysteemschijf te koppelen. Gebruik dit script in de volgende scenario's:
* Een virtuele machine maken van een bestaande beheerde besturingssysteemschijf die is gekopieerd van een beheerde schijf in een ander abonnement
* Een virtuele machine maken van een bestaande beheerde schijf die is gemaakt met behulp van een speciaal VHD-bestand 
* Een virtuele machine maken van een bestaande beheerde schijf die is gemaakt aan de hand van een momentopname 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep, VM, en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het opvragen van de eigenschappen van de beheerde schijf, het koppelen van een beheerde schijf aan een nieuwe virtuele machine en het maken van een virtuele machine. Elk item in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzDisk](/powershell/module/az.compute/get-azdisk) | Hiermee haalt u een schijfobject op dat is gebaseerd op de naam en de resourcegroep van een schijf. De eigenschap Id van het geretourneerde schijfobject wordt gebruikt om de schijf te koppelen aan een nieuwe VM |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Hiermee maakt u een VM-configuratie. Deze configuratie bevat informatie zoals de naam, het besturingssysteem en de beheerdersreferenties van de virtuele machine. De configuratie wordt gebruikt tijdens het maken van de virtuele machine. |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | Hiermee wordt een beheerde schijf met behulp van de eigenschap Id van de schijf gekoppeld als besturingssysteemschijf aan een nieuwe virtuele machine |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Hiermee maakt u een netwerkinterface. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Hiermee maakt u een virtuele machine. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. |

Gebruik voor marketplace-installatiekopieën [Set AzVMPlan](/powershell/module/az.compute/set-azvmplan) om de plangegevens in te stellen.

```powershell
Set-AzVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

U kunt extra PowerShell-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Linux-VM's](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
