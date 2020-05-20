---
title: Azure CLI-voor beelden-schaalset met één zone
description: Met dit script maakt u een virtuele-machine schaalset van Azure waarop Ubuntu wordt uitgevoerd in één beschikbaarheids zone.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 03/27/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 8883bd099b684b5086554feded88e133764e614f
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701602"
---
# <a name="create-a-single-zone-virtual-machine-scale-set-with-the-azure-cli"></a>Een virtuele-machineschaalset met één zone maken met Azure CLI
Met dit script maakt u een virtuele-machineschaalset met Ubuntu in één beschikbaarheidszone. Nadat het script is uitgevoerd, hebt u via RDP toegang tot de virtuele machine.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh "Create single-zone scale set")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Gebruik de volgende opdracht om de resourcegroep, de schaalset en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script
In dit script worden de volgende opdrachten gebruikt voor het maken van een resourcegroep, een virtuele-machineschaalset en alle gerelateerde resources. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/ad/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az vmss create](/cli/azure/vmss) | Hiermee maakt u de virtuele machine en verbindt u deze met het virtuele netwerk, het subnet en de netwerkbeveiligingsgroep. Om het verkeer te distribueren naar de verschillende VM-exemplaren, wordt er ook een load balancer gemaakt. Met deze opdracht geeft u ook de VM-installatiekopie op die moet worden gebruikt, samen met beheerdersreferenties.  |
| [az group delete](/cli/azure/ad/group) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen
Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure/overview) voor meer informatie over de Azure CLI.
