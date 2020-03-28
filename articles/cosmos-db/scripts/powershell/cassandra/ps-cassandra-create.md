---
title: PowerShell-script om Azure Cosmos DB Cassandra API-keyspace en -tabel te maken
description: Azure PowerShell-script - Azure Cosmos DB maakt Cassandra API-keyspace en -tabel
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 1fcded19731d412a4892e231f4445c51b330b1b4
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366137"
---
# <a name="create-a-keyspace-and-table-for-azure-cosmos-db---cassandra-api"></a>Een keyspace en tabel maken voor Azure Cosmos DB - Cassandra API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-create.ps1 "Create a keyspace and table for Cassandra API")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
|**Azure-bronnen**| |
| [Nieuw-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Hiermee maakt u een resource. |
|**Azure Cosmos DB**| |
| [Set-AzCosmosDBCassandraKeyspace](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbcassandrakeyspace) | Hiermee maakt of werkt u een Cosmos DB Cassandra API Keyspace uit. |
| [Nieuwe-AzCosmosDBCassandraClusterKey](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbcassandraclusterkey) | Hiermee maakt u een nieuwe CosmosDB Cassandra Cluster Key. |
| [Nieuw-AzCosmosDBCassandraColumn](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbcassandracolumn) | Hiermee maakt u een nieuwe CosmosDB Cassandra Column. |
| [Nieuw-AzCosmosDBCassandraSchema](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbcassandraschema) | Hiermee maakt u een nieuw CosmosDB Cassandra Schema. |
| [Set-azcosmosDBCassandratable](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbcassandratable) | Hiermee maakt of werkt u een Cosmos DB Cassandra API-tabel uit. |
|**Azure-brongroepen**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Meer Azure Cosmos DB PowerShell-voorbeeldscripts vindt u in [Azure Cosmos DB PowerShell-scripts](../../../powershell-samples.md).