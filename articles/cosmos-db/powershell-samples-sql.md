---
title: Azure PowerShell-voorbeelden voor Azure Cosmos DB SQL (Core) API
description: De Azure PowerShell-voorbeelden verschillende algemene taken laten uitvoeren in de SQL-API-accounts van Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: 82d709567e75540a865758162ac87f55dfa84bea
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126090"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Azure PowerShell-voorbeelden voor Azure Cosmos DB SQL (Core) API

De volgende tabel bevat koppelingen naar veelgebruikte Azure PowerShell-scripts voor Azure Cosmos DB SQL (Core) API. Als u deze PowerShell-voorbeelden voor Cosmos DB wilt vorken vanaf onze GitHub-opslagplaats, gaat u naar [PowerShell-voorbeelden voor Cosmos DB op GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Zie [Azure Cosmos DB SQL API-resources beheren met PowerShell](manage-with-powershell.md) voor meer PowerShell-voorbeelden voor Cosmos DB SQL (Core) API en documentatie. Zie [Cassandra API](powershell-samples-cassandra.md), [MongoDB API](powershell-samples-mongodb.md), [Gremlin API](powershell-samples-gremlin.md) en [Table API](powershell-samples-table.md) voor PowerShell-voorbeelden voor Cosmos DB voor andere API’s.

> [!NOTE]
> De voorbeelden maken gebruik van beheer-cmdlets voor [AZ. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb). Controleer regelmatig of er updates zijn van `Az.CosmosDB`.

| | |
|---|---|
|[Een account, database en container maken](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een Azure Cosmos DB-account, -database en -container. |
|[Een container maken met een grote partitiesleutel](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een container met een grote partitiesleutel. |
|[Databases of containers in een lijst weergeven of ophalen](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Geef de database of container in een lijst weer of haal deze op. |
|[RU/s ophalen](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| RU/s ophalen voor een database of container. |
|[RU/s bijwerken](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Werk RU/s bij voor een database of container. |
|[Een container maken zonder indexbeleid](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Maak een Azure Cosmos DB-container met uitgeschakeld indexbeleid.|
|[Een account bijwerken](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Werk het standaard consistentieniveau van een Cosmos DB-account bij. |
|[De regio’s van een account bijwerken](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Werk de regio’s van een Cosmos DB-account bij. |
|[Failoverprioriteit wijzigen of failover activeren](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wijzig de prioriteit van de regionale failover van een Azure Cosmos-account of activeer een handmatige failover. |
|[Accountsleutels of verbindingsreeksen](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Haal de primaire en secundaire sleutels en verbindingsreeksen op of genereer opnieuw een accountsleutel van een Azure Cosmos DB-account. |
|[Een Cosmos-account maken met een IP-firewall](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een Azure Cosmos DB-account waarvoor IP-firewall is ingeschakeld. |
|[Resources vergrendelen tegen verwijdering](scripts/powershell/sql/powershell-sql-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling. |
|||
