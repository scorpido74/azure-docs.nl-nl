---
title: Een DNS-zone en-record maken voor een domein naam-Azure CLI-Azure DNS
description: In dit voorbeeld van een Azure CLI-script ziet u hoe u een DNS-zone maakt en registreert voor een domeinnaam
services: dns
author: rohinkoul
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: rohink
ms.openlocfilehash: b1db9ffb895470dd9224dc020eb82b0bbf6560c1
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939310"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Voorbeeld van Azure CLI-script: een DNS-zone maken en registreren

In dit voorbeeld van een Azure CLI-script wordt een DNS-zone gemaakt en geregistreerd voor een domeinnaam. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/dns/create-dns-zone-and-record/create-dns-zone-and-record.sh "Create DNS zone and record")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Voer de volgende opdracht uit om de resourcegroep, DNS-zone en alle gerelateerde resources te verwijderen.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het maken van een resourcegroep, een virtuele machine, een beschikbaarheidsset, een load balancer en alle gerelateerde resources. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Hiermee wordt een Azure DNS-zone gemaakt. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Hiermee wordt een *A*-record toegevoegd aan een DNS-zone. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | Hiermee worden alle *A*-recordsets in een DNS-zone weergegeven. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Hiermee verwijdert u een resourcegroep met inbegrip van alle ingesloten resources. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

