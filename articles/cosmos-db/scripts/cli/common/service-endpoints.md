---
title: Een Azure Cosmos-account maken met Service-eind punten voor virtuele netwerken
description: Een Azure Cosmos-account maken met Service-eind punten voor virtuele netwerken
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: ff700739e2f8c6330ea151dbe489332acea3238c
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71275407"
---
# <a name="create-an-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Een Azure Cosmos-account maken met virtuele netwerk service-eind punten met behulp van Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0.73 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="sample-script"></a>Voorbeeldscript

In dit voor beeld wordt een nieuw virtueel netwerk met een front-en back-end-subnet gemaakt `Microsoft.AzureCosmosDB`en worden service-eind punten ingeschakeld voor. Vervolgens wordt de resource-id voor dit subnet opgehaald en toegepast op het Azure Cosmos-account en worden service-eind punten voor het account ingeschakeld.

> [!NOTE]
> Dit voor beeld laat zien hoe u een SQL (core) API-account gebruikt. Als u dit voor beeld voor andere api's wilt gebruiken `enable-virtual-network` , `virtual-network-rules` moet u de para meters en in het onderstaande script Toep assen op uw API-specifiek script.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints.sh "Create an Azure Cosmos account with service endpoints.")]

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
| [az group delete](/cli/azure/resource#az-resource-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Cosmos DB cli-documentatie](/cli/azure/cosmosdb)voor meer informatie over de Azure Cosmos DB cli.

Alle Azure Cosmos DB CLI-voorbeeld scripts vindt u in de [Azure Cosmos DB cli github-opslag plaats](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
