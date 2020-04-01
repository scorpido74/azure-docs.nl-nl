---
title: PowerShell-script om Azure Cosmos DB SQL API-database en -container te maken
description: Azure PowerShell-script - Azure Cosmos DB maken SQL API-database en -container
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: 719e2cd831a982c62ab965cd7dc8a37c4cb41265
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365614"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-api"></a>Een database en container maken voor Azure Cosmos DB - SQL API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Voorbeeldscript

Dit script maakt een Cosmos-account voor SQL (Core) API in twee regio's met consistentie op sessieniveau, een database en een container met een partitiesleutel, aangepast indexeringsbeleid, `multipleWriteLocations=true`uniek sleutelbeleid, TTL, speciale doorvoer en laatste schrijver wint conflictoplossingsbeleid met een aangepast conflictoplossingspad dat wordt gebruikt wanneer .

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
| [Nieuw-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Hiermee maakt u een nieuw Cosmos DB-account. |
| [Set-azcosmosDBSqlDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqldatabase) | Hiermee maakt u een nieuwe of worden een bestaande Cosmos DB SQL Database bijgewerkt. |
| [Nieuw-azcosmosDBSqlUniqueKey](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekey) | Hiermee maakt u een nieuw Cosmos DB SQL UniqueKey-object. |
| [Nieuw-azcosmosDBsqluniqueKeyPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekeypolicy) | Hiermee maakt u een nieuw object Cosmos DB SQL UniqueKeyPolicy. |
| [Nieuw-azcosmosdbsqlincludedpathindex](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpathindex) | Hiermee maakt u een nieuw object van het type PSIndexes dat wordt gebruikt als parameter voor Set-AzCosmosDBSqlIncludedPath. |
| [Nieuw-azcosmosdbsqlincludedpath](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpath) | Hiermee maakt u een nieuw object van het type PSIncludedPath dat wordt gebruikt als parameter voor Nieuw-AzCosmosDBSqlIndexingPolicy. |
| [Nieuw-AzCosmosDBSqlIndexingBeleid](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Hiermee maakt u een nieuw object van het type PSSqlIndexingPolicy dat wordt gebruikt als parameter voor Set-AzCosmosDBSqlContainer. |
| [Nieuw-azcosmosdbsqlconflictresolutionpolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlconflictresolutionpolicy) | Hiermee maakt u een nieuw object van het type PSSqlConflictResolutionPolicy dat wordt gebruikt als parameter voor Set-AzCosmosDBSqlContainer. |
| [Set-azcosmosDBSqlContainer](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqlcontainer) | Hiermee maakt u een nieuwe of worden een bestaande Cosmos DB SQL Container bijgewerkt. |
|**Azure-brongroepen**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Meer Azure Cosmos DB PowerShell-voorbeeldscripts vindt u in [Azure Cosmos DB PowerShell-scripts](../../../powershell-samples.md).
