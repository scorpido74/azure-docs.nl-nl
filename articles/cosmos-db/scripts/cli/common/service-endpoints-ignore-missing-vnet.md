---
title: Een bestaand Azure Cosmos-account verbinden met virtuele netwerk service-eind punten
description: Een bestaand Azure Cosmos-account verbinden met virtuele netwerk service-eind punten
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 46e93e864034c451e1da1848a318ab176a292b6e
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71275561"
---
# <a name="connect-an-existing-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Een bestaand Azure Cosmos-account verbinden met virtuele netwerk service-eind punten met behulp van Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0.73 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="sample-script"></a>Voorbeeldscript

Dit voor beeld is bedoeld om te laten zien hoe u een bestaand Azure Cosmos-account verbindt met een bestaand nieuw virtueel netwerk, waarbij het subnet nog niet is geconfigureerd voor service `ignore-missing-vnet-service-endpoint` -eind punten met behulp van de para meter. Hierdoor kan de configuratie voor het Cosmos-account zonder fouten worden voltooid voordat de configuratie naar het subnet van het virtuele netwerk is voltooid. Zodra de configuratie van het subnet is voltooid, is het Cosmos-account toegankelijk via het geconfigureerde subnet.

> [!NOTE]
> Dit voor beeld laat zien hoe u een SQL (core) API-account gebruikt. Als u dit voor beeld voor andere api's wilt gebruiken `enable-virtual-network` , `virtual-network-rules` moet u de para meters en in het onderstaande script Toep assen op uw API-specifiek script.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints-ignore-missing-vnet.sh "Create an Azure Cosmos account with service endpoints.")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Hiermee maakt u een virtueel Azure-netwerk. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Hiermee maakt u een subnet voor een virtueel Azure-netwerk. |
| [AZ Network vnet subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show) | Retourneert een subnet voor een virtueel Azure-netwerk. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Hiermee wordt een Azure Cosmos DB-account gemaakt. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) | Hiermee wordt een subnet bijgewerkt voor een virtueel Azure-netwerk. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Cosmos DB cli-documentatie](/cli/azure/cosmosdb)voor meer informatie over de Azure Cosmos DB cli.

Alle Azure Cosmos DB CLI-voorbeeld scripts vindt u in de [Azure Cosmos DB cli github-opslag plaats](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
