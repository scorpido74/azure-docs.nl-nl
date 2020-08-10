---
title: Voorbeelden van Azure PowerShell voor Azure Cosmos DB
description: Download de Azure PowerShell-voorbeelden voor het uitvoeren van verschillende algemene taken Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 678e9490fe52a329365174e1a2283a475f3312e5
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505012"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Voorbeelden van Azure PowerShell voor Azure Cosmos DB

De volgende tabel bevat koppelingen naar veelgebruikte Azure PowerShell-scripts voor Azure Cosmos DB. Gebruik de links aan de rechterkant om te navigeren naar voorbeelden voor specifieke API's. Algemene voorbeelden zijn hetzelfde voor alle API's. Referentiepagina's voor alle PowerShell-cmdlets voor Azure Cosmos DB zijn beschikbaar in de [naslaginformatie voor Azure PowerShell](/powershell/module/az.cosmosdb). Controleer regelmatig of er updates zijn van `Az.CosmosDB`. U kunt deze PowerShell-voorbeelden ook forken voor Cosmos DB vanaf onze GitHub-opslagplaats, te weten [Cosmos DB PowerShell Samples op GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

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
|[Doorvoer bepalen](scripts/powershell/sql/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| De doorvoer bepalen voor een database of container. |
|[Doorvoer bijwerken](scripts/powershell/sql/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| De doorvoer voor een database of container bijwerken. |
|[Resources vergrendelen tegen verwijdering](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling. |
|||

## <a name="cassandra-api-samples"></a>Voorbeelden van Cassandra-API

|Taak | Beschrijving |
|---|---|
|[Een account, keyspace en een tabel maken](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hiermee wordt een Azure Cosmos-account, -keyspace en -tabel gemaakt. |
|[Een account, keyspace en tabel maken met automatische schaalaanpassing](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een account, keyspace en tabel van Azure Cosmos met automatische schaalaanpassing. |
|[Keyspaces of tabellen weergeven of ophalen](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Geef keyspaces of tabellen weer of haal deze op. |
|[Doorvoer bepalen](scripts/powershell/cassandra/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| De doorvoer bepalen voor een keyspace of tabel. |
|[Doorvoer bijwerken](scripts/powershell/cassandra/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| De doorvoer voor een keyspace of tabel bijwerken. |
|[Resources vergrendelen tegen verwijdering](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling. |
|||

## <a name="mongo-db-api-samples"></a>Voorbeelden van Mongo DB API

|Taak | Beschrijving |
|---|---|
|[Een account, database en verzameling maken](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hiermee maakt u een Azure Cosmos-account, -database en -verzameling. |
|[Een account, database en verzameling maken met automatische schaalaanpassing](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een account, database en verzameling maken met automatische schaalaanpassing. |
|[Databases of verzamelingen in een lijst weergeven of ophalen](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Geef de database of verzameling in een lijst weer of haal deze op. |
|[Doorvoer bepalen](scripts/powershell/mongodb/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| De doorvoer bepalen voor een database of verzameling. |
|[Doorvoer bijwerken](scripts/powershell/mongodb/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| De doorvoer voor een database of verzameling bijwerken. |
|[Resources vergrendelen tegen verwijdering](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling. |
|||

## <a name="gremlin-api-samples"></a>Voorbeelden van Gremlin-API

|Taak | Beschrijving |
|---|---|
|[Een account, database en grafiek maken](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hiermee maakt u een Azure Cosmos-account, -database en -grafiek. |
|[Een account, database en grafiek maken met automatische schaalaanpassing](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hiermee maakt u een account, database en grafiek met automatische schaalaanpassing. |
|[Databases of grafieken in een lijst weergeven of ophalen](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Geef de databases of grafiek in een lijst weer of haal deze op. |
|[Doorvoer bepalen](scripts/powershell/gremlin/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| De doorvoer bepalen voor een database of grafiek. |
|[Doorvoer bijwerken](scripts/powershell/gremlin/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| De doorvoer voor een database of grafiek bijwerken. |
|[Resources vergrendelen tegen verwijdering](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling. |
|||

## <a name="table-api-samples"></a>Voorbeelden van Table-API

|Taak | Beschrijving |
|---|---|
|[Een account en een tabel maken](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hiermee wordt een Azure Cosmos-account en -tabel gemaakt. |
|[Een account en tabel maken met automatische schaalaanpassing](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hiermee maakt u een Azure Cosmos-account en -tabel met automatische schaalaanpassing. |
|[Tabellen weergeven of ophalen](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Geef tabellen weer of haal deze op. |
|[Doorvoer bepalen](scripts/powershell/table/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| De doorvoer voor een tabel bepalen. |
|[Doorvoer bijwerken](scripts/powershell/table/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| De doorvoer voor een tabel bijwerken. |
|[Resources vergrendelen tegen verwijdering](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling. |
|||
