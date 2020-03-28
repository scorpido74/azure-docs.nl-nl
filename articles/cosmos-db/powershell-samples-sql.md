---
title: Azure PowerShell-voorbeelden voor Azure Cosmos DB - SQL (Core) API
description: De Azure PowerShell-voorbeelden downloaden om verschillende veelvoorkomende taken uit te voeren in Azure Cosmos DB SQL API-accounts
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: efc0ff8e6c198071d3906a0e7e999510198f73bf
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366183"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Azure PowerShell-voorbeelden voor Azure Cosmos DB - SQL (Core) API

De volgende tabel bevat koppelingen naar veelgebruikte Azure PowerShell-scripts voor Azure Cosmos DB voor SQL(Core)-API. Als je deze PowerShell-samples wilt vorken voor Cosmos DB uit onze GitHub-repository, ga je naar [Cosmos DB PowerShell Samples op GitHub.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)

Zie [Azure Cosmos DB SQL API-bronnen beheren met PowerShell](manage-with-powershell.md)voor extra Cosmos DB PowerShell-samples voor SQL-API en -documentatie . Zie [Cassandra API](powershell-samples-cassandra.md), [MongoDB API](powershell-samples-mongodb.md), [Gremlin API](powershell-samples-gremlin.md)en Table API voor Cosmos DB PowerShell-samples voor andere API's . [Table API](powershell-samples-table.md)

> [!NOTE]
> De monsters maken gebruik van [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) management cmdlets. Houd er `Az.CosmosDB` rekening mee dat de cmdlets nog in de preview staan en voor de release kunnen veranderen. Controleer regelmatig op `Az.CosmosDB` updates.

| | |
|---|---|
|[Een account, database en container maken](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een Azure Cosmos DB-account, database en container. |
|[Een container maken met een grote partitiesleutel](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een container met een grote partitiesleutel. |
|[Databases of containers weergeven of openen](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lijst of ontvang database of containers. |
|[Ontvang RU/s](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ontvang RU/s voor een database of container. |
|[RU/s bijwerken](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| RU/s bijwerken voor een database of container. |
|[Een container maken zonder indexbeleid](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Maak een Azure Cosmos-container met indexbeleid uitgeschakeld.|
|[Een account bijwerken](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Werk het standaardconsistentieniveau van een Cosmos DB-account bij. |
|[Regio's van een account bijwerken](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| De regio's van een Cosmos DB-account bijwerken. |
|[Prioriteit voor failover wijzigen of failover activeren](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wijzig de regionale failoverprioriteit van een Azure Cosmos-account of activeer een handmatige failover. |
|[Accountsleutels of verbindingstekenreeksen](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ontvang primaire en secundaire sleutels, verbindingstekenreeksen of regenereerde een accountsleutel van een Azure Cosmos DB-account. |
|[Een Cosmos-account maken met IP-firewall](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een Azure Cosmos DB-account met IP Firewall ingeschakeld. |
|||
