---
title: PowerShell-script om Azure Cosmos DB Table API-bewerkingen weer te geven en te krijgen
description: Azure PowerShell-script - Azure Cosmos DB-lijst en bewerkingen voor tabel-API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 166b9c2d6b20fbd6a71ba5df17e049838038617d
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365910"
---
# <a name="list-and-get-tables-for-azure-cosmos-db---table-api"></a>Tabellen voor Azure Cosmos DB - Tabel-API aanbieden en opvragen

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-list-get.ps1 "List or get tables for Table API")]

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
| [Get-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Hiermee worden Cosmos DB-accounts vermeld of wordt een opgegeven Cosmos DB-account weergegeven. |
| [Get-AzCosmosDBTable](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbtable) | Hiermee worden Cosmos DB Table API-tabellen in een account vermeld of wordt een opgegeven Cosmos DB Table API-tabel in een account weergegeven. |
|**Azure-brongroepen**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Meer Azure Cosmos DB PowerShell-voorbeeldscripts vindt u in [Azure Cosmos DB PowerShell-scripts](../../../powershell-samples.md).