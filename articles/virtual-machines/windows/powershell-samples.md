---
title: PowerShell-voorbeelden voor virtuele Azure-machines
description: PowerShell-voorbeelden voor virtuele Azure-machines
author: cynthn
ms.service: virtual-machines-windows
ms.topic: sample
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e8489ecb9343cf70bd8523aa45f04a6bde171b98
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321749"
---
# <a name="azure-virtual-machine-powershell-samples"></a>PowerShell-voorbeelden voor virtuele Azure-machines

De volgende tabel biedt links naar voorbeelden van PowerShell-scripts waarmee Windows-VM's kunnen worden gemaakt en beheerd.

| Script | Beschrijving |
|---|---|
|**Virtuele machines maken**||
| [Snel een virtuele machine maken](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een resourcegroep, een virtuele machine en alle bijbehorende resources met een minimaal aantal prompts.|
| [Een volledig geconfigureerde virtuele machine maken](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een resourcegroep, een virtuele machine en alle bijbehorende resources.|
| [Highly Available Virtual Machines maken](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u verschillende virtuele machines met een configuratie met hoge beschikbaarheid en taakverdeling.|
| [Een VM maken en een configuratiescript uitvoeren](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een virtuele machine en gebruikt u de aangepaste scriptextensie voor Azure om IIS te installeren. |
| [Een VM maken en een DSC-configuratie uitvoeren](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een virtuele machine en gebruikt u de Desired State Configuration-extensie (DSC) voor Azure om IIS te installeren. |
| [Een VHD uploaden en VM's maken](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Hiermee uploadt u een lokaal VHD-bestand naar Azure, maakt u een installatiekopie van de VHD en maakt u vervolgens een virtuele machine van die installatiekopie. |
| [Een VM maken op basis van een beheerde besturingssysteemschijf](./../scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een virtuele machine door een bestaande beheerde schijf als besturingssysteemschijf te koppelen. |
| [Een VM maken op basis van een momentopname](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een virtuele machine op basis van een momentopname door eerst een beheerde schijf te maken op basis van de momentopname en vervolgens de nieuwe beheerde schijf als besturingssysteemschijf te koppelen. |
|**Opslag beheren**||
| [Een beheerde schijf maken op basis van een VHD in hetzelfde of een ander abonnement](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een beheerde schijf op basis van een gespecialiseerde VHD als een besturingssysteemschijf of op basis van een gegevens-VHD als een gegevensschijf in hetzelfde of een ander abonnement.  |
| [Een beheerde schijf maken op basis van een momentopname](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u beheerde schijf op basis van een momentopname. |
| [Een beheerde schijf kopiëren naar hetzelfde of een ander abonnement](../scripts/virtual-machines-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Hiermee kopieert u een beheerde schijf naar hetzelfde of een ander abonnement dat zich in dezelfde regio bevindt als de bovenliggende beheerde schijf.
| [Een momentopname als VHD exporteren naar een opslagaccount](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee exporteert u een beheerde momentopname als een VHD naar een opslagaccount in een andere regio. |
| [De VHD van een beheerde schijf exporteren naar een opslagaccount](../scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee exporteert u de onderliggende VHD van een beheerde schijf naar een opslagaccount in dezelfde of een andere regio. |
| [Een momentopname maken van een VHD](../scripts/virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een momentopname van een VHD en gebruikt u die momentopname om snel meerdere identieke beheerde schijven te maken.  |
| [Een momentopname kopiëren naar hetzelfde of een ander abonnement](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee kopieert u een momentopname naar hetzelfde of een ander abonnement dat zich in dezelfde regio bevindt als de bovenliggende momentopname. |
|**Virtuele machines beveiligen**||
| [Een virtuele machine en de bijbehorende gegevensschijven versleutelen](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Hiermee maakt u een Azure-sleutelkluis, een versleutelingssleutel en een service-principal, en versleutelt u vervolgens een virtuele machine. |
|**Virtuele machines bewaken**||
| [VM bewaken met Azure-bewaking](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een virtuele machine, installeert u de Azure Log Analytics-agent en registreert u de virtuele machine bij een Log Analytics-werkruimte.  |
| [Details verzamelen over alle virtuele machines in een abonnement met PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een CSV met de naam van de virtuele machine, de naam van de resourcegroep, de regio, het virtuele netwerk, het subnet, het privé-IP-adres, het type besturingssysteem en het openbare IP-adres van de virtuele machines in het opgegeven abonnement.
| | |
