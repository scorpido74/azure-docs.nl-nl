---
title: 'Azure CLI: een virtuele machine in een lab stoppen en verwijderen'
description: Dit artikel bevat een Azure CLI-script waarmee u een virtuele machine in een lab in Azure DevTest Labs kunt stoppen en verwijderen.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: c4f315593da270155998c3c5bba8def2778f0d41
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84889177"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Azure CLI gebruiken om een virtuele machine in een lab te stoppen en te verwijderen in Azure DevTest Labs

Met dit Azure CLI-script wordt een virtuele machine (VM) in een lab gestopt en verwijderd. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | Hiermee wordt een virtuele machine (VM) in een lab gestopt. Deze bewerking kan enige tijd duren. |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | Hiermee wordt een virtuele machine (VM) in een lab verwijderd. Deze bewerking kan enige tijd duren. |


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Er zijn extra voorbeelden van Azure Lab Services CLI-scripts te vinden in de [voorbeelden van Azure Lab Services CLI](../samples-cli.md).
