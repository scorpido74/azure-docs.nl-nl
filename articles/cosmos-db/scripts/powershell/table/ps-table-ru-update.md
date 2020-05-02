---
title: Power shell-script voor het bijwerken van RU/s voor Azure Cosmos DB Table-API
description: Meer informatie over het gebruik van een Power shell-script voor het bijwerken van de door Voer voor een Data Base of een container in Azure Cosmos DB Table-API-account
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.openlocfilehash: 6d67812274fce72fd0a4bb4ff8338ac90f5df9d3
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652892"
---
# <a name="update-rus-for-a-table-for-azure-cosmos-db---table-api"></a>Update RU/s voor een tabel voor Azure Cosmos DB-Table-API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-update.ps1 "Update throughput on a table for Table API")]

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
| [Get-AzCosmosDBTableThroughput](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbtablethroughput) | Hiermee wordt de doorvoer waarde van de Table-API-tabel opgehaald. |
| [Update-AzCosmosDBMongoDBCollectionThroughput](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbsqldatabasethroughput) | Hiermee wordt de doorvoer waarde van de Table-API-tabel bijgewerkt. |
|**Azure-resource groepen**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Meer Azure Cosmos DB PowerShell-voorbeeldscripts vindt u in [Azure Cosmos DB PowerShell-scripts](../../../powershell-samples.md).