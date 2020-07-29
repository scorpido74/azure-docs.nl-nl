---
title: Een momentopname kopiëren naar een opslagaccount in een andere regio | Linux CLI-voorbeeld
description: 'Azure CLI-voorbeeldscript: exporteer/kopieer een momentopname als VHD naar een opslagaccount in dezelfde of een andere regio.'
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: 42785cdb6fd866ccf7eafbd802552ea9cb2c93a4
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509750"
---
# <a name="exportcopy-a-snapshot-to-a-storage-account-in-different-region-with-cli"></a>Een momentopname exporteren/kopiëren naar een opslagaccount in een andere regio met CLI

Met dit script wordt een beheerde momentopname geëxporteerd naar een opslagaccount in een andere regio. Eerst wordt de SAS-URI van de momentopname gegenereerd, die vervolgens wordt gekopieerd naar een opslagaccount in een andere regio. Gebruik dit script voor het onderhouden van back-ups van uw beheerde schijven in een andere regio voor herstel na noodgevallen. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het genereren van de SAS-URI voor een beheerde momentopname en kopieert de momentopname naar een opslagaccount met behulp van de SAS-URI. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az snapshot grant-access](/cli/azure/snapshot) | Hiermee genereert u alleen-lezen SAS dat wordt gebruikt om het onderliggende VHD-bestand te kopiëren naar een opslagaccount of om dat bestand lokaal te downloaden  |
| [az storage blob copy start](/cli/azure/storage/blob/copy) | Hiermee kopieert u een blob asynchroon vanuit het ene opslagaccount naar het andere |

## <a name="next-steps"></a>Volgende stappen

[Een beheerde schijf maken op basis van een VHD](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Een virtuele machine maken op basis van een beheerde schijf](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-scriptvoorbeelden voor virtuele machines en beheerde schijven vindt u in de [Azure-documentatie voor Linux-VM's](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
