---
title: Beheerde schijf maken op basis van een momentopname (Windows) - CLI-voorbeeld
description: 'Azure CLI-voorbeeldscript: een beheerde schijf maken op basis van een momentopname'
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 68435af52a9593d6b8c9fef0de66e867048919de
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87028495"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-cli-windows"></a>Een beheerde schijf maken met CLI op basis van een momentopname (Windows)

Met dit script maakt u een beheerde schijf op basis van een momentopname. Gebruik het script om een virtuele machine te herstellen op basis van momentopnamen van het besturingssysteem en gegevensschijven. Maak beheerde OS- en gegevensschijven op basis van de respectieve momentopnamen en maak vervolgens een nieuwe virtuele machine door beheerde schijven te koppelen. U kunt ook gegevensschijven van een bestaande virtuele machine herstellen door het koppelen van gegevensschijven die zijn gemaakt op basis van momentopnamen.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Create managed disk from snapshot")]

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten om een beheerde schijf te maken op basis van een momentopname. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | Hiermee haalt u alle eigenschappen van een momentopname op aan de hand van de naam en de eigenschappen van de resourcegroep van de momentopname. Id-eigenschap wordt gebruikt om de beheerde schijf te maken.  |
| [az disk create](/cli/azure/disk) | Hiermee maakt u een beheerde met behulp van de momentopname-id van een beheerde momentopname |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-scriptvoorbeelden voor virtuele machines en beheerde schijven vindt u in de [Azure-documentatie voor Windows-VM's](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
