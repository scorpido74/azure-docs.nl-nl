---
title: PowerShell-voorbeelden van Azure Virtual Machine PowerShell
description: PowerShell-voorbeelden van Azure Virtual Machine PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 434cf3f9342ad10bab39030e65cdd4bc4d0edc27
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458859"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure Virtual Machine PowerShell-voorbeelden

In de volgende tabel vindt u koppelingen naar PowerShell-scriptvoorbeelden waarmee virtuele Vm's (VM's) worden gemaakt en beheren.

| | |
|---|---|
|**Virtuele machines maken**||
| [Maak snel een virtuele machine](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een resourcegroep, een virtuele machine en alle bijbehorende resources, met een minimum aan prompts.|
| [Een volledig geconfigureerde virtuele machine maken](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een resourcegroep, een virtuele machine en alle bijbehorende resources.|
| [Maak zeer beschikbare virtuele machines](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee worden verschillende virtuele machines gemaakt in een zeer beschikbare en load-balanced configuratie.|
| [Een VM maken en een configuratiescript uitvoeren](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een virtuele machine en gebruikt u de Azure Custom Script-extensie om IIS te installeren. |
| [Een VM maken en een DSC-configuratie uitvoeren](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een virtuele machine en gebruikt u de DSC-extensie (Azure Desired State Configuration) om IIS te installeren. |
| [Een VHD uploaden en VM's maken](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Hiermee wordt een lokaal VHD-bestand naar Azure geüpload, wordt een afbeelding van de VHD gemaakt en wordt er vervolgens een VM gemaakt van die afbeelding. |
| [Een vm maken vanaf een beheerde osschijf](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een virtuele machine door een bestaande beheerde schijf als OS-schijf te koppelen. |
| [Een VM maken op basis van een momentopname](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een virtuele machine op basis van een momentopname door eerst een beheerde schijf van de momentopname te maken en vervolgens de nieuwe beheerde schijf als OS-schijf te bevestigen. |
|**Opslag beheren**||
| [Een beheerde schijf maken vanaf een VHD in hetzelfde of een ander abonnement](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een beheerde schijf van een gespecialiseerde VHD als osschijf of van een gegevensVHD als gegevensschijf, in hetzelfde of een ander abonnement.  |
| [Een beheerde schijf maken op basis van een momentopname](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een beheerde schijf op basis van een momentopname. |
| [Een beheerde schijf naar hetzelfde of een ander abonnement kopiëren](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Kopieert een beheerde schijf naar hetzelfde of een ander abonnement dat zich in dezelfde regio bevindt als de bovenliggende beheerde schijf.
| [Een momentopname als VHD exporteren naar een opslagaccount](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee exporteert u een beheerde momentopname als VHD naar een opslagaccount in een andere regio. |
| [De VHD van een beheerde schijf exporteren naar een opslagaccount](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporteert de onderliggende VHD van een beheerde schijf naar een opslagaccount in een andere regio. |
| [Een momentopname maken van een VHD](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een momentopname van een VHD en gebruikt u die momentopname om snel meerdere identieke beheerde schijven te maken.  |
| [Een momentopname naar hetzelfde of een ander abonnement kopiëren](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee kopieert u momentopname naar hetzelfde of een ander abonnement dat zich in dezelfde regio bevindt als de bovenliggende momentopname. |
|**Virtuele machines beveiligen**||
| [Een virtuele machine en de bijbehorende gegevensschijven versleutelen](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Hiermee maakt u een Azure-sleutelkluis, een versleutelingssleutel en een serviceprincipal en versleutelt u vervolgens een vm. |
|**Virtuele machines bewaken**||
| [Een VM bewaken met Azure Monitor](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een virtuele machine, installeert u de Azure Log Analytics-agent en schrijft u de VM in een loganalytics-werkruimte in.  |
| [Gegevens verzamelen over alle VM's in een abonnement met PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een csv die de VM-naam, resourcegroepnaam, regio, virtueel netwerk, subnet, privé-IP-adres, OS-type en openbaar IP-adres van de VM's in het opgegeven abonnement bevat.
| | |
