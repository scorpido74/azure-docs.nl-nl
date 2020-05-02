---
title: Power shell-script voor het bijwerken van de door Voer (RU/s) voor een Azure Cosmos DB SQL API-data base of-container
description: Power shell-script voor het bijwerken van de door Voer (RU/s) voor een Azure Cosmos DB SQL API-data base of-container
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.openlocfilehash: 19b13db83b2c2533e963a503dc8ea3e362b5b5ed
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652967"
---
# <a name="update-throughput-rus-for-an-azure-cosmos-db-sql-api-database-or-container"></a>De door Voer (RU/s) bijwerken voor een Azure Cosmos DB SQL API-data base of-container

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-update.ps1 "Update throughput (RU/s) for an Azure Cosmos DB SQL API Database or Container")]

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
| [Get-AzCosmosDBSqlContainerThroughput](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainerthroughput) | Hiermee wordt de doorvoer waarde van de SQL-container opgehaald. |
| [Update-AzCosmosDBSqlContainerThroughput](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainer) | Hiermee wordt de doorvoer waarde van de SQL-container bijgewerkt. |
|**Azure-resource groepen**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Meer Azure Cosmos DB PowerShell-voorbeeldscripts vindt u in [Azure Cosmos DB PowerShell-scripts](../../../powershell-samples.md).