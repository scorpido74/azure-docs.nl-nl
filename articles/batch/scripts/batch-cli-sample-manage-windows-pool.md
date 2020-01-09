---
title: Voor beeld van Azure CLI-script-Windows-groep in batch
description: Dit script toont enkele van de opdrachten die in de Azure CLI beschikbaar zijn voor het maken en beheren van een groep Windows-rekenknooppunten in Azure Batch.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2019
ms.author: lahugh
ms.openlocfilehash: 662dda0c72cd50a84751ef92236e4e94b45de450
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75390094"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>CLI-voorbeeld: een Windows-groep maken en beheren in Azure Batch

Dit script toont enkele van de opdrachten die in de Azure CLI beschikbaar zijn voor het maken en beheren van een groep Windows-rekenknooppunten in Azure Batch. Een Windows-groep kan op twee manieren worden geconfigureerd: met een Cloud Services-configuratie of een Virtual Machine-configuratie. Dit voorbeeld laat zien hoe u een Windows-groep maakt met de Cloud Services-configuratie.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0.20 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. 

## <a name="example-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Windows Cloud Services Pool")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Hiermee wordt de Batch-account gemaakt. |
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az-batch-account-login) | Hiermee wordt authenticatie uitgevoerd met het opgegeven Batch-account voor verdere interactie met de CLI. |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-create) | Hiermee wordt een groep rekenknooppunten gemaakt.  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-set) | Hiermee worden de eigenschappen van een groep bijgewerkt.  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az-batch-pool-autoscale-enable) | Hiermee schakelt u automatisch schalen van een groep in en wordt een formule toegepast.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-show) | Hiermee worden de eigenschappen van een groep opgehaald.  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az-batch-pool-autoscale-disable) | Hiermee schakelt u automatisch schalen van een groep uit. |
| [az group delete](/cli/azure/group#az-group-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |


## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.
