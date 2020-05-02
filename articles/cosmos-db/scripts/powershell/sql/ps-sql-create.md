---
title: Power shell-script voor het maken van Azure Cosmos DB SQL API-data base en-container
description: Azure PowerShell script-Azure Cosmos DB SQL API-data base en-container maken
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.openlocfilehash: 434cc6df326a0028f327c6d9e2eda0d19989ca01
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82653166"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-api"></a>Een Data Base en container maken voor Azure Cosmos DB-SQL-API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Voorbeeldscript

Met dit script maakt u een Cosmos-account voor SQL-API (core) in twee regio's met consistentie op sessie niveau, een Data Base en een container met een partitie sleutel, aangepast indexerings beleid, uniek sleutel beleid, TTL, toegewezen door Voer en laatste schrijver WINS conflict oplossings beleid met een aangepast `multipleWriteLocations=true`pad voor conflict oplossing dat wordt gebruikt wanneer.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL API")]

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
| [New-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Hiermee maakt u een nieuw Cosmos DB-account. |
| [Set-AzCosmosDBSqlDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqldatabase) | Hiermee wordt een bestaande Cosmos DB SQL Database gemaakt of bijgewerkt. |
| [New-AzCosmosDBSqlUniqueKey](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekey) | Hiermee maakt u een nieuw Cosmos DB SQL PSSqlUniqueKey-object dat wordt gebruikt als para meter voor New-AzCosmosDBSqlUniqueKeyPolicy. |
| [New-AzCosmosDBSqlUniqueKeyPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekeypolicy) | Hiermee maakt u een nieuw Cosmos DB SQL PSSqlUniqueKeyPolicy-object dat wordt gebruikt als para meter voor set-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlCompositePath](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlcompositepath) | Hiermee maakt u een nieuw Cosmos DB SQL PSCompositePath-object dat wordt gebruikt als para meter voor New-AzCosmosDBSqlIndexingPolicy. |
| [New-AzCosmosDBSqlIncludedPathIndex](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpathindex) | Hiermee wordt een nieuw object van het type PSIndexes gebruikt als para meter voor set-AzCosmosDBSqlIncludedPath. |
| [New-AzCosmosDBSqlIncludedPath](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpath) | Hiermee maakt u een nieuw object van het type PSIncludedPath dat wordt gebruikt als para meter voor New-AzCosmosDBSqlIndexingPolicy. |
| [New-AzCosmosDBSqlIndexingPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Hiermee wordt een nieuw object van het type PSSqlIndexingPolicy gebruikt als para meter voor set-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlConflictResolutionPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlconflictresolutionpolicy) | Hiermee wordt een nieuw object van het type PSSqlConflictResolutionPolicy gebruikt als para meter voor set-AzCosmosDBSqlContainer. |
| [Set-AzCosmosDBSqlContainer](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqlcontainer) | Hiermee wordt een bestaande Cosmos DB SQL-container gemaakt of bijgewerkt. |
|**Azure-resource groepen**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Meer Azure Cosmos DB PowerShell-voorbeeldscripts vindt u in [Azure Cosmos DB PowerShell-scripts](../../../powershell-samples.md).
