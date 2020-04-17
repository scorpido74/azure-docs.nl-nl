---
title: Beheerde schijven kopiëren naar het eenabonnement - CLI-voorbeeld
description: Voorbeeld van Azure CLI Script - Beheerde schijven kopiëren (of verplaatsen) naar hetzelfde of een ander abonnement
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
ms.custom: mvc
ms.openlocfilehash: 540f11e2089d6aeac1b6c664695d1dafbf31d65a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460882"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Beheerde schijven kopiëren naar hetzelfde of een ander abonnement met CLI

Met dit script wordt een beheerde schijf gekopieerd naar hetzelfde of een ander abonnement, maar wel in dezelfde regio. De kopie werkt alleen als de abonnementen onderdeel uitmaken van dezelfde AAD-tenant.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om een nieuwe beheerde schijf te maken in het doelabonnement met gebruikmaking van de id van de beheerde bronschijf. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | Hiermee haalt u alle eigenschappen van een beheerde schijf op aan de hand van de naam en de eigenschappen van de resourcegroep van de beheerde schijf. De eigenschap Id wordt gebruikt om de beheerde schijf naar een ander abonnement te kopiëren.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Hiermee kopieert u een beheerde schijf door een nieuwe beheerde schijf te maken in een ander abonnement aan de hand van de id en de naam van de bovenliggende beheerde schijf.  |

## <a name="next-steps"></a>Volgende stappen

[Een virtuele machine maken van een beheerde schijf](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-scriptvoorbeelden voor virtuele machines en beheerde schijven vindt u in de [Azure-documentatie voor Linux-VM's](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
