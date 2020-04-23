---
title: Azure CLI-voorbeelden Windows
description: Azure CLI-voorbeelden Windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4868254ffc0448562ff6115a4e6bb030e4353ee4
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083306"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Azure CLI-voorbeelden voor virtuele Windows-machines

De volgende tabel bevat koppelingen om scripts te bashen die zijn gebouwd met de Azure CLI die virtuele Windows-machines implementeert.

| | |
|---|---|
|**Virtuele machines maken**||
| [Een virtuele machine maken](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een virtuele Windows-machine met minimale configuratie. |
| [Een volledig geconfigureerde virtuele machine maken](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een resourcegroep, virtuele machine en alle bijbehorende resources.|
| [Maak zeer beschikbare virtuele machines](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee worden verschillende virtuele machines gemaakt in een zeer beschikbare en laadgebalanceerde configuratie. |
| [Een VM maken en configuratiescript uitvoeren](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een virtuele machine en gebruikt u de Azure Custom Script-extensie om IIS te installeren. |
| [Een VM maken en DSC-configuratie uitvoeren](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een virtuele machine en gebruikt u de DSC-extensie (Azure Desired State Configuration) om IIS te installeren. |
|**Opslag beheren**||
| [Een beheerde schijf maken op basis van een VHD](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een beheerde schijf van een gespecialiseerde VHD als osschijf of van een gegevensVHD als gegevensschijf.  |
| [Een beheerde schijf maken op basis van een momentopname](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een beheerde schijf op basis van een momentopname. |
| [Beheerde schijf naar hetzelfde of ander abonnement kopiëren](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopieerde beheerde schijf naar hetzelfde of ander abonnement, maar in dezelfde regio als de bovenliggende beheerde schijf. 
| [Een momentopname als VHD exporteren naar een opslagaccount](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee exporteert u een beheerde momentopname als VHD naar een opslagaccount in verschillende regio's. |
| [De VHD van een beheerde schijf exporteren naar een opslagaccount](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporteert de onderliggende VHD van een beheerde schijf naar een opslagaccount in verschillende regio's. |
| [Momentopname naar hetzelfde of ander abonnement kopiëren](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee kopieert u momentopname naar hetzelfde of een ander abonnement, maar in dezelfde regio als de bovenliggende momentopname. |
|**Netwerken voor virtuele machines instellen**||
| [Netwerkverkeer tussen virtuele machines beveiligen](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u twee virtuele machines, alle bijbehorende resources en een interne en externe netwerkbeveiligingsgroepen (NSG). |
|**Virtuele machines beveiligen**||
| [Een virtuele machine en de bijbehorende gegevensschijven versleutelen](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een Azure Key Vault, een versleutelingssleutel en serviceprincipal en versleutelt u vervolgens een vm. |
|**Virtuele machines bewaken**||
| [Een VM bewaken met Azure Monitor](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Hiermee maakt u een virtuele machine, installeert u de log-analyse-agent en schrijft u de VM in een werkruimte Log Analytics in.  |
| | |
