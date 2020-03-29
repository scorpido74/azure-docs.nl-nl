---
title: Azure CLI-voorbeelden
description: Azure CLI-voorbeelden
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 76803f8c3a703071eb733c5cfde65482ffd07f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970081"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Azure CLI-voorbeelden voor virtuele Linux-machines

De volgende tabel bevat koppelingen naar bash-scripts die zijn gebouwd met behulp van de Azure CLI.

| | |
|---|---|
|**Virtuele machines maken**||
| [Een virtuele machine maken](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Maakt een Linux virtuele machine met minimale configuratie. |
| [Een volledig geconfigureerde virtuele machine maken](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een resourcegroep, virtuele machine en alle bijbehorende resources.|
| [Maak zeer beschikbare virtuele machines](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee worden verschillende virtuele machines gemaakt in een zeer beschikbare en laadgebalanceerde configuratie. |
| [Een VM maken en configuratiescript uitvoeren](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine en gebruikt u de Azure Custom Script-extensie om NGINX te installeren. |
| [Een VM maken met WordPress geïnstalleerd](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine en gebruikt u de Azure Custom Script-extensie om WordPress te installeren. |
| [Een vm maken vanaf een beheerde osschijf](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine door een bestaande beheerde schijf als OS-schijf te koppelen. |
| [Een VM maken op basis van een momentopname](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine op basis van een momentopname door eerst een beheerde schijf te maken vanaf momentopname en vervolgens de nieuwe beheerde schijf als OS-schijf te bevestigen. |
|**Opslag beheren**||
| [Een beheerde schijf maken op basis van een VHD](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een beheerde schijf van een gespecialiseerde VHD als osschijf of van een gegevensVHD als gegevensschijf.  |
| [Een beheerde schijf maken op basis van een momentopname](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een beheerde schijf op basis van een momentopname. |
| [Beheerde schijf naar hetzelfde of ander abonnement kopiëren](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopieerde beheerde schijf naar hetzelfde of ander abonnement, maar in dezelfde regio als de bovenliggende beheerde schijf. 
| [Een momentopname als VHD exporteren naar een opslagaccount](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee exporteert u een beheerde momentopname als VHD naar een opslagaccount in verschillende regio's. |
| [De VHD van een beheerde schijf exporteren naar een opslagaccount](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporteert de onderliggende VHD van een beheerde schijf naar een opslagaccount in verschillende regio's. |
| [Momentopname naar hetzelfde of ander abonnement kopiëren](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee kopieert u momentopname naar hetzelfde of een ander abonnement, maar in dezelfde regio als de bovenliggende momentopname. |
|**Netwerken voor virtuele machines instellen**||
| [Netwerkverkeer tussen virtuele machines beveiligen](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u twee virtuele machines, alle bijbehorende resources en een interne en externe netwerkbeveiligingsgroepen (NSG). |
|**Virtuele machines beveiligen**||
| [Een virtuele machine en de bijbehorende gegevensschijven versleutelen](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een Azure Key Vault, een versleutelingssleutel en serviceprincipal en versleutelt u vervolgens een vm. |
|**Virtuele machines bewaken**||
| [Een VM bewaken met Azure Monitor-logboeken](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine, installeert u de log-analyse-agent en schrijft u de VM in een werkruimte Log Analytics in.  |
|**Virtuele machines oplossen**||
| [Problemen met de besturingssysteemschijf van een virtuele machine oplossen](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Monteert de schijf van het besturingssysteem van de ene VM als een gegevensschijf op een tweede VM. |
| | |
