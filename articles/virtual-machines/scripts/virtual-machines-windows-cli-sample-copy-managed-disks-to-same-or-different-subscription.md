---
title: Beheerde schijven naar een abonnement kopiëren - CLI-voorbeeld
description: Voorbeeld van Azure CLI-script - Beheerde schijven kopiëren (verplaatsen) naar hetzelfde of een ander abonnement
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
ms.openlocfilehash: 4b1516e492b13426bed6cae8637abd5c713bcd63
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87010118"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Beheerde schijven kopiëren naar hetzelfde of een ander abonnement met CLI

Met dit script wordt een beheerde schijf gekopieerd naar hetzelfde of een ander abonnement, maar wel in dezelfde regio. De kopie werkt alleen als de abonnementen onderdeel uitmaken van dezelfde AAD-tenant.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om een nieuwe beheerde schijf te maken in het doelabonnement met gebruikmaking van de id van de beheerde bronschijf. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az disk show](/cli/azure/disk) | Hiermee haalt u alle eigenschappen van een beheerde schijf op aan de hand van de naam en de eigenschappen van de resourcegroep van de beheerde schijf. De eigenschap Id wordt gebruikt om de beheerde schijf naar een ander abonnement te kopiëren.  |
| [az disk create](/cli/azure/disk) | Hiermee kopieert u een beheerde schijf door een nieuwe beheerde schijf te maken in een ander abonnement aan de hand van de id en de naam van de bovenliggende beheerde schijf.  |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-scriptvoorbeelden voor virtuele machines en beheerde schijven vindt u in de [Azure-documentatie voor Windows-VM's](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
