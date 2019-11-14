---
title: 'Azure CLI-voor beeld: een moment opname kopiëren naar een opslag account in een andere regio'
description: 'Azure CLI-voorbeeldscript: exporteer/kopieer een momentopname als VHD naar een opslagaccount in dezelfde of een andere regio.'
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: 1e9ba52357703238c35d31823462d9ff3bd04c87
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040047"
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
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot) | Hiermee genereert u alleen-lezen SAS dat wordt gebruikt om het onderliggende VHD-bestand te kopiëren naar een opslagaccount of om dat bestand lokaal te downloaden  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy) | Hiermee kopieert u een blob asynchroon vanuit het ene opslagaccount naar het andere |

## <a name="next-steps"></a>Volgende stappen

[Een beheerde schijf maken op basis van een VHD](virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Meer informatie over de CLI-voorbeeld scripts voor virtuele machines en beheerde schijven vindt u in de documentatie van de [Azure Windows-VM](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
