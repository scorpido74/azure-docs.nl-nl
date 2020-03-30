---
title: Een bestaand Azure Cosmos-account verbinden met eindpunten voor virtuele netwerkservices
description: Een bestaand Azure Cosmos-account verbinden met eindpunten voor virtuele netwerkservices
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 46e93e864034c451e1da1848a318ab176a292b6e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275561"
---
# <a name="connect-an-existing-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Een bestaand Azure Cosmos-account verbinden met eindpunten voor virtuele netwerkservices met Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest de CLI lokaal te installeren en te gebruiken, moet u in dit onderwerp de Azure CLI-versie 2.0.73 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

Dit voorbeeld is bedoeld om te laten zien hoe u een bestaand Azure Cosmos-account koppelen aan een `ignore-missing-vnet-service-endpoint` bestaand nieuw virtueel netwerk waarbij het subnet nog niet is geconfigureerd voor serviceeindpunten met behulp van de parameter. Hierdoor kan de configuratie voor het Cosmos-account foutloos worden voltooid voordat de configuratie van het subnet van het virtuele netwerk is voltooid. Zodra de subnetconfiguratie is voltooid, is het Cosmos-account toegankelijk via het geconfigureerde subnet.

> [!NOTE]
> Dit voorbeeld toont aan dat u een SQL (Core) API-account gebruikt. Als u dit voorbeeld wilt gebruiken `enable-virtual-network` `virtual-network-rules` voor andere API's, past u de parameters en parameters in het onderstaande script toe op uw API-specifieke script.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints-ignore-missing-vnet.sh "Create an Azure Cosmos account with service endpoints.")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Hiermee maakt u een virtueel Azure-netwerk. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Hiermee maakt u een subnet voor een virtueel Azure-netwerk. |
| [az netwerk vnet subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show) | Retourneert een subnet voor een virtueel Azure-netwerk. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Hiermee wordt een Azure Cosmos DB-account gemaakt. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) | Werkt een subnet bij voor een virtueel Azure-netwerk. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Cosmos DB CLI-documentatie](/cli/azure/cosmosdb)voor meer informatie over de Azure Cosmos DB CLI.

Alle Azure Cosmos DB CLI-scriptvoorbeelden zijn te vinden in de [Azure Cosmos DB CLI GitHub Repository.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
