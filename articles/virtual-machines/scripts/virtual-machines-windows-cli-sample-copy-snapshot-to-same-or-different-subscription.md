---
title: Een momentopname van een beheerde schijf naar een abonnement kopiëren - CLI-voorbeeld, Windows-VM
description: 'Azure CLI-voorbeeldscript: met CLI een momentopname van een beheerde schijf kopiëren (verplaatsen) naar hetzelfde of een ander abonnement op een Windows-VM'
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
ms.openlocfilehash: 05b73bf62bfce456e65fde153379690bc9823e6a
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056734"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli-on-a-windows-vm"></a>Met CLI een momentopname van een beheerde schijf kopiëren naar hetzelfde of een ander abonnement op een Windows-VM

Met dit script wordt een momentopname van een beheerde schijf gekopieerd naar hetzelfde of een ander abonnement. Gebruik dit script in de volgende scenario's:

1. Migreer een momentopname in Premium-opslag (Premium_LRS) naar de Standard-opslag (Standard_LRS of Standard_ZRS) om uw kosten te verlagen.
1. Migreer een momentopname van lokaal redundante opslag (Premium_LRS, Standard_LRS) naar zone-redundante opslag (Standard_ZRS) om te profiteren van de hogere betrouwbaarheid van ZRS-opslag.
1. Verplaats een momentopname naar een ander abonnement in dezelfde regio voor langere gegevensretentie.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om een momentopname te maken in het doelabonnement met behulp van de id van de bronmomentopname. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | Hiermee haalt u alle eigenschappen van een momentopname op aan de hand van de naam en de eigenschappen van de resourcegroep van de momentopname. De id-eigenschap wordt gebruikt om de momentopname naar een ander abonnement te kopiëren.  |
| [az snapshot create](/cli/azure/snapshot) | Hiermee wordt een momentopname gekopieerd door een momentopname te maken in een ander abonnement met dezelfde id en naam als de bovenliggende momentopname.  |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-scriptvoorbeelden voor virtuele machines en beheerde schijven vindt u in de [Azure-documentatie voor Windows-VM's](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
