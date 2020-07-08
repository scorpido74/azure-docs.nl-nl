---
title: Power shell-voor beelden voor virtuele Azure-machines
description: Power shell-voor beelden voor virtuele Azure-machines
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 8d7db5fe88890b7f807263e50757e637ad808eb1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81759320"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Power shell-voor beelden voor virtuele Azure-machines

De volgende tabel bevat koppelingen naar Power shell-voorbeeld scripts voor het maken en beheren van virtuele Linux-machines.

| | |
|---|---|
|**Virtuele machines maken**||
| [Een volledig geconfigureerde virtuele machine maken](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een resource groep, een virtuele machine en alle gerelateerde resources.|
| [Een VM maken waarop Docker is ingeschakeld](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine, configureert u deze VM als een docker-host en voert u een NGINX-container uit. |
| [Een VM maken en een configuratiescript uitvoeren](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine en gebruikt u de aangepaste script extensie van Azure om NGINX te installeren. |
| [Een VM maken waarop WordPress is geïnstalleerd](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine en gebruikt u de aangepaste script extensie van Azure om WordPress te installeren. |
| [Een VM maken op basis van een beheerde besturingssysteemschijf](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine door een bestaande beheerde schijf te koppelen als besturingssysteem schijf. |
| [Een VM maken op basis van een momentopname](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine op basis van een moment opname door eerst een beheerde schijf te maken op basis van de moment opname en vervolgens de nieuwe beheerde schijf te koppelen als besturingssysteem schijf. |
|**Opslag beheren**||
| [Een beheerde schijf maken op basis van een VHD in hetzelfde of een ander abonnement](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een beheerde schijf van een speciale VHD als een besturingssysteem schijf, of van een gegevens-VHD als een gegevens schijf, in hetzelfde of een ander abonnement.  |
| [Een beheerde schijf maken op basis van een momentopname](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een beheerde schijf op basis van een moment opname. |
| [Een momentopname als VHD exporteren naar een opslagaccount](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporteert een beheerde moment opname als VHD naar een opslag account in een andere regio. |
| [De VHD van een beheerde schijf exporteren naar een opslagaccount](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporteert de onderliggende VHD van een beheerde schijf naar een opslag account in een andere regio. |
| [Een momentopname maken van een VHD](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een moment opname van een VHD en gebruikt u die moment opname om snel meerdere identieke beheerde schijven te maken.  |
| [Een momentopname kopiëren naar hetzelfde of een ander abonnement](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | De moment opname wordt gekopieerd naar hetzelfde of een ander abonnement dat zich in dezelfde regio bevindt als de bovenliggende moment opname. |
|**Virtuele machines bewaken**||
| [Een VM bewaken met Azure Monitor-logboeken](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine, installeert u de Log Analytics-agent en registreert u de VM in een Log Analytics-werk ruimte.  |
| [Een beheerde schijf kopiëren naar hetzelfde of een ander abonnement](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee wordt een beheerde schijf gekopieerd naar hetzelfde of een ander abonnement dat zich in dezelfde regio bevindt als de bovenliggende beheerde schijf.
| [Details verzamelen over alle virtuele machines in een abonnement met PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een CSV met de VM-naam, de naam van de resource groep, de regio, de Virtual Network, het subnet, het privé-IP-adres, het type besturings systeem en het open bare IP-adres van de virtuele machines in het abonnement.
| | |
