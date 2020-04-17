---
title: PowerShell-voorbeelden van Azure Virtual Machine PowerShell
description: PowerShell-voorbeelden van Azure Virtual Machine PowerShell
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: c726678578e3972ae57bdf99b75d0ddd52cd2d38
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460967"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure Virtual Machine PowerShell-voorbeelden

De volgende tabel bevat koppelingen naar PowerShell-scripts voorbeelden die virtuele Linux-machines maken en beheren.

| | |
|---|---|
|**Virtuele machines maken**||
| [Een volledig geconfigureerde virtuele machine maken](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een resourcegroep, virtuele machine en alle bijbehorende resources.|
| [Een VM maken met Docker ingeschakeld](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine, configureert u deze VM als Docker-host en wordt een NGINX-container uitgevoerd. |
| [Een VM maken en configuratiescript uitvoeren](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine en gebruikt u de Azure Custom Script-extensie om NGINX te installeren. |
| [Een VM maken met WordPress geïnstalleerd](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine en gebruikt u de Azure Custom Script-extensie om WordPress te installeren. |
| [Een vm maken vanaf een beheerde osschijf](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine door een bestaande beheerde schijf als OS-schijf te koppelen. |
| [Een VM maken op basis van een momentopname](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine op basis van een momentopname door eerst een beheerde schijf van de momentopname te maken en vervolgens de nieuwe beheerde schijf als OS-schijf te bevestigen. |
|**Opslag beheren**||
| [Een beheerde schijf maken vanaf een VHD in hetzelfde of een ander abonnement](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een beheerde schijf van een gespecialiseerde VHD als osschijf of van een gegevensVHD als gegevensschijf, in hetzelfde of een ander abonnement.  |
| [Een beheerde schijf maken op basis van een momentopname](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een beheerde schijf op basis van een momentopname. |
| [Een momentopname als VHD exporteren naar een opslagaccount](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee exporteert u een beheerde momentopname als VHD naar een opslagaccount in een andere regio. |
| [De VHD van een beheerde schijf exporteren naar een opslagaccount](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporteert de onderliggende VHD van een beheerde schijf naar een opslagaccount in een andere regio. |
| [Een momentopname maken van een VHD](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een momentopname van een VHD en gebruikt u die momentopname om snel meerdere identieke beheerde schijven te maken.  |
| [Een momentopname naar hetzelfde of een ander abonnement kopiëren](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee kopieert u momentopname naar hetzelfde of een ander abonnement dat zich in dezelfde regio bevindt als de bovenliggende momentopname. |
|**Virtuele machines bewaken**||
| [Een VM bewaken met Azure Monitor-logboeken](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een virtuele machine, installeert u de log-analyse-agent en schrijft u de VM in een werkruimte Log Analytics in.  |
| [Een beheerde schijf naar hetzelfde of een ander abonnement kopiëren](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Kopieert een beheerde schijf naar hetzelfde of een ander abonnement dat zich in dezelfde regio bevindt als de bovenliggende beheerde schijf.
| [Gegevens verzamelen over alle VM's in een abonnement met PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Hiermee maakt u een csv die de VM-naam, resourcegroepnaam, regio, virtueel netwerk, subnet, privé-IP-adres, OS-type en openbaar IP-adres van de VM's in het opgegeven abonnement bevat.
| | |
