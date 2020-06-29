---
title: Azure CLI - Een virtuele machine maken en controleren in een lab
description: Met dit Azure CLI-script wordt een virtuele machine in een lab gemaakt en gecontroleerd of deze beschikbaar is.
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
ms.date: 01/16/2020
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 767d1f3a504e91783e37d8ff1c1b97f62816af3b
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84889100"
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Azure CLI gebruiken om een virtuele machine in een lab te maken en de beschikbaarheid te controleren in Azure DevTest Labs

Met dit Azure CLI-script wordt een virtuele machine (VM) in een lab gemaakt. De virtuele machine is gemaakt op basis van een Marketplace-installatiekopie met SSH-verificatie. Via dit script wordt gecontroleerd of de virtuele machine beschikbaar is voor gebruik. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Gebruik de volgende opdracht om de resourcegroep, VM, en alle gerelateerde resources te verwijderen.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az lab vm create ](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-create) | Hiermee wordt een virtuele machine (VM) in een lab gemaakt. |
| [az lab vm show](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-show) | Hiermee wordt de status van de virtuele machine in een lab weergegeven. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Er zijn extra voorbeelden van Azure Lab Services CLI-scripts te vinden in de [voorbeelden van Azure Lab Services CLI](../samples-cli.md).
