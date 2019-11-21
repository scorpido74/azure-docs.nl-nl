---
title: Create a DNS zone and record for a domain name - Azure CLI - Azure DNS
description: In dit voorbeeld van een Azure CLI-script ziet u hoe u een DNS-zone maakt en registreert voor een domeinnaam
services: dns
author: asudbring
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: allensu
ms.openlocfilehash: 8f5dde0a35f31d2c33ab2857659efc88339c2ead
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74210403"
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

Dit script gebruikt de volgende opdrachten voor het maken van een resourcegroep, een virtuele machine, een beschikbaarheidsset, een load balancer en alle gerelateerde resources. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Hiermee wordt een Azure DNS-zone gemaakt. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Hiermee wordt een *A*-record toegevoegd aan een DNS-zone. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | Hiermee worden alle *A*-recordsets in een DNS-zone weergegeven. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Hiermee verwijdert u een resourcegroep met inbegrip van alle ingesloten resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

