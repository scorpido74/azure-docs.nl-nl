---
title: Voorbeeld van Azure CLI-script - Schijf van besturingssysteem koppelen
description: Voorbeeld van Azure CLI-script - Schijf van besturingssysteem koppelen
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 292d67dafa768c82041a2cae8e6d888ee5d9050b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037595"
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>Problemen met de besturingssysteemschijf van een virtuele machine oplossen

Met dit script koppelt u de schijf van het besturingssysteem van een uitgevallen of problematische virtuele machine als een gegevensschijf aan een tweede virtuele machine. Dit kan nuttig zijn bij het oplossen van schijfproblemen of het herstellen van gegevens.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het maken van een resourcegroep, een virtuele machine en alle gerelateerde resources. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az vm show](https://docs.microsoft.com/cli/azure/vm) | Hiermee wordt een lijst met virtuele machines weergegeven. In dit geval wordt de query-optie gebruikt om de schijf met het besturingssysteem van de virtuele machine op te vragen. Deze waarde wordt vervolgens toegevoegd aan de naam van de variabele 'uri'. |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm) | Hiermee verwijdert u een virtuele machine. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Hiermee maakt u een virtuele machine.  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk) | Hiermee wordt een schijf gekoppeld aan een virtuele machine. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm) | Hiermee retourneert u de IP-adressen van een virtuele machine. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

U kunt extra CLI-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Linux-VM's](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
