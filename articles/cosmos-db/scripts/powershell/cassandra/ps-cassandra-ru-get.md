---
title: Power shell-script voor het ophalen van door Voer (RU/s) voor het Azure Cosmos DB van Cassandra-API resources
description: Azure PowerShell script-Azure Cosmos DB door Voer (RU/s) voor Cassandra-API ophalen
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: c5e177aca446d9a81db1a339cb8d81f0486a91e5
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82653065"
---
# <a name="get-throughput-rus-for-a-keyspace-or-table-for-azure-cosmos-db---cassandra-api"></a>Een door Voer (RU/s) voor een spatie of tabel voor Azure Cosmos DB-Cassandra-API ophalen

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-get.ps1 "Get throughput on a keyspace or table for Cassandra API")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBCassandraKeyspaceThroughput](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbcassandrakeyspacethroughput) | Hiermee wordt de doorvoer waarde van de Cassandra-API-spatie opgehaald. |
| [Get-AzCosmosDBCassandraTableThroughput](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbcassandratablethroughput) | Hiermee wordt de doorvoer waarde van de Cassandra-API-tabel opgehaald. |
|**Azure-resource groepen**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Meer Azure Cosmos DB PowerShell-voorbeeldscripts vindt u in [Azure Cosmos DB PowerShell-scripts](../../../powershell-samples.md).