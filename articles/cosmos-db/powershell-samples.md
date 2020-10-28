---
title: Azure PowerShell-voorbeelden voor Azure Cosmos DB Core (SQL) API
description: Download de Azure PowerShell-voorbeelden voor het uitvoeren van verschillende algemene taken Azure Cosmos DB for Core (SQL) API
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: 174458d9e5be24de669060ea8264f62017489c7a
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92279320"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-core-sql-api"></a>Azure PowerShell-voorbeelden voor Azure Cosmos DB Core (SQL) API

De volgende tabel bevat koppelingen naar veelgebruikte Azure PowerShell-scripts voor Azure Cosmos DB. Gebruik de links aan de rechterkant om te navigeren naar voorbeelden voor specifieke API's. Algemene voorbeelden zijn hetzelfde voor alle API's. Referentiepagina's voor alle PowerShell-cmdlets voor Azure Cosmos DB zijn beschikbaar in de [naslaginformatie voor Azure PowerShell](/powershell/module/az.cosmosdb). Controleer regelmatig of er updates zijn van `Az.CosmosDB`. U kunt deze PowerShell-voorbeelden ook forken voor Cosmos DB vanaf onze GitHub-opslagplaats, te weten [Cosmos DB PowerShell Samples op GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Zie voor PowerShell-cmdlets voor andere API's [PowerShell Samples for Cassandra](powershell-samples-cassandra.md), [PowerShell Samples for MongoDB API](powershell-samples-mongodb.md), [PowerShell Samples for Gremlin](powershell-samples-gremlin.md), [PowerShell Samples for Table](powershell-samples-table.md)

## <a name="common-samples"></a>Algemene voorbeelden

|Taak | Beschrijving |
|---|---|
|[Een account bijwerken](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Werk het standaard consistentieniveau van een Cosmos DB-account bij. |
|[De regio’s van een account bijwerken](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Werk de regio’s van een Cosmos DB-account bij. |
|[Failoverprioriteit wijzigen of failover activeren](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wijzig de prioriteit van de regionale failover van een Azure Cosmos-account of activeer een handmatige failover. |
|[Accountsleutels of verbindingsreeksen](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Haal de primaire en secundaire sleutels en verbindingsreeksen op of genereer opnieuw een accountsleutel van een Azure Cosmos DB-account. |
|[Een Cosmos-account maken met een IP-firewall](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een Azure Cosmos DB-account waarvoor IP-firewall is ingeschakeld. |
|||

## <a name="core-sql-api-samples"></a>API-voorbeelden Core (SQL)

|Taak | Beschrijving |
|---|---|
|[Een account, database en container maken](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een Azure Cosmos DB-account, -database en -container. |
|[Een account, database en container maken met automatische schaalaanpassing](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een Azure Cosmos DB-account, -database en -container met automatische schaalaanpassing. |
|[Een container maken met een grote partitiesleutel](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een container met een grote partitiesleutel. |
|[Een container maken zonder indexbeleid](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Maak een Azure Cosmos DB-container met uitgeschakeld indexbeleid.|
|[Databases of containers in een lijst weergeven of ophalen](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Geef de database of container in een lijst weer of haal deze op. |
|[Doorvoerbewerkingen](scripts/powershell/sql/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Doorvoerbewerkingen voor een database of container, waaronder ophalen, bijwerken en migreren tussen doorvoer met automatische schaalaanpassing en standaarddoorvoer. |
|[Resources vergrendelen tegen verwijdering](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling. |
|||
