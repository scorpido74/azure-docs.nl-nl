---
title: 'Azure CLI-voorbeelden: een virtuele-machineschaalset maken'
description: Met dit script maakt u in Azure een virtuele-machineschaalset met het besturingssysteem Ubuntu en bijbehorende netwerkresources, inclusief een load balancer.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: c2c1ea09d10ee177a722a56f6825db0f37da7e9b
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85374663"
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli"></a>Een virtuele-machineschaalset maken met Azure CLI
Met dit script maakt u in Azure een virtuele-machineschaalset met het besturingssysteem Ubuntu en bijbehorende netwerkresources, inclusief een load balancer. Nadat het script is uitgevoerd, hebt u via SSH toegang tot de virtuele machine.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/simple-scale-set/simple-scale-set.sh "Create a simple virtual machine scale set")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Gebruik de volgende opdracht om de resourcegroep, de schaalset en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script
In dit script worden de volgende opdrachten gebruikt voor het maken van een resourcegroep, een virtuele-machineschaalset en alle gerelateerde resources. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/ad/group) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az vmss create](/cli/azure/vmss) | Hiermee maakt u de virtuele machine en verbindt u deze met het virtuele netwerk, het subnet en de netwerkbeveiligingsgroep. Om het verkeer te distribueren naar de verschillende VM-exemplaren, wordt er ook een load balancer gemaakt. Met deze opdracht geeft u ook de VM-installatiekopie op die moet worden gebruikt, samen met beheerdersreferenties.  |
| [az group delete](/cli/azure/ad/group) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure/overview) voor meer informatie over de Azure CLI.
