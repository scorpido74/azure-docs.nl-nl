---
title: Azure CLI-scriptvoorbeeld - Een virtuele machine starten in een lab | Microsoft Docs
description: Met dit Azure CLI-script wordt een virtuele machine in een lab in Azure DevTest Labs gestart.
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
ms.date: 04/12/2018
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 2159e594f0cc6a43905f3562c0ad6f3e3c9984fe
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84889232"
---
# <a name="use-azure-cli-to-start-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Azure CLI gebruiken om een virtuele machine te starten in een lab in Azure DevTest Labs

Met dit Azure CLI-script wordt een virtuele machine (VM) in een lab gestart. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/start-connect-virtual-machine-in-lab/start-connect-virtual-machine-in-lab.sh "Start a VM")]


## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az lab vm start](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-start) | Hiermee wordt een virtuele machine (VM) in een lab gestart. Deze bewerking kan enige tijd duren. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Er zijn extra voorbeelden van Azure Lab Services CLI-scripts te vinden in de [voorbeelden van Azure Lab Services CLI](../samples-cli.md).
