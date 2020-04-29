---
title: Azure PowerShell-voor beelden voor Azure Cosmos DB-SQL-API (core)
description: De Azure PowerShell-voor beelden ophalen om verschillende algemene taken uit te voeren in Azure Cosmos DB SQL-API-accounts
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: efc0ff8e6c198071d3906a0e7e999510198f73bf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80366183"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Azure PowerShell-voor beelden voor Azure Cosmos DB-SQL-API (core)

De volgende tabel bevat koppelingen naar veelgebruikte Azure PowerShell scripts voor Azure Cosmos DB for SQL-API (core). Als u deze Power shell-voor beelden wilt splitsen voor Cosmos DB in onze GitHub-opslag plaats, gaat u naar [Cosmos DB Power shell-voor beelden op github](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Zie [Azure Cosmos DB SQL API-resources beheren met Power shell](manage-with-powershell.md)voor aanvullende Cosmos DB Power shell-voor beelden voor SQL (kern)-API en-documentatie. Zie [Cassandra-API](powershell-samples-cassandra.md), [MongoDb API](powershell-samples-mongodb.md), [Gremlin API](powershell-samples-gremlin.md)en [Table-API](powershell-samples-table.md)voor Cosmos DB Power shell-voor beelden voor andere api's.

> [!NOTE]
> De voor beelden gebruiken [AZ. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) Management-cmdlets. Houd er rekening mee `Az.CosmosDB` dat de cmdlets nog in Preview zijn en kunnen worden gewijzigd voordat de release wordt uitgebracht. Controleer `Az.CosmosDB` regel matig of er updates zijn.

| | |
|---|---|
|[Een account, data base en container maken](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Een Azure Cosmos DB-account,-data base en-container maken. |
|[Een container met een grote partitie sleutel maken](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een container met een grote partitie sleutel. |
|[Data bases of containers weer geven of ophalen](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Data Base of containers weer geven of ophalen. |
|[Ophalen van RU/s](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ophalen van RU/s voor een Data Base of container. |
|[RU/s bijwerken](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Update RU/s voor een Data Base of container. |
|[Een container maken zonder index beleid](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Een Azure Cosmos-container maken waarvoor het index beleid is uitgeschakeld.|
|[Een account bijwerken](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Het standaard consistentie niveau van een Cosmos DB-account bijwerken. |
|[De regio's van een account bijwerken](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| De regio's van een Cosmos DB-account bijwerken. |
|[Failover-prioriteit of trigger-failover wijzigen](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wijzig de prioriteit van de regionale failover van een Azure Cosmos-account of Activeer een hand matige failover. |
|[Account sleutels of verbindings reeksen](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| De primaire en secundaire sleutels, verbindings reeksen ophalen of een account sleutel van een Azure Cosmos DB account opnieuw genereren. |
|[Een Cosmos-account maken met een IP-firewall](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een Azure Cosmos DB-account waarop IP-firewall is ingeschakeld. |
|||
