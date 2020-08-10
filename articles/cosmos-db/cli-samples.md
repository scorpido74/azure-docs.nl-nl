---
title: Voorbeelden van Azure CLI voor Azure Cosmos DB
description: Voorbeelden van Azure CLI voor Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 954215f04525e850151fdad93af6e7272b41b3df
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498460"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Voorbeelden van Azure CLI voor Azure Cosmos DB

De volgende tabel bevat koppelingen naar Azure CLI-voorbeeldscripts voor Azure Cosmos DB. Gebruik de links aan de rechterkant om te navigeren naar voorbeelden voor specifieke API's. Algemene voorbeelden zijn hetzelfde voor alle API's. Referentiepagina's voor alle Azure Cosmos DB CLI-opdrachten zijn beschikbaar in de [naslaginformatie voor Azure CLI](/cli/azure/cosmosdb). CLI-voorbeeldscripts voor Azure Cosmos DB kunt u ook vinden in de [opslagplaats van Azure Cosmos DB CLI in GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Voor deze voorbeelden hebt u Azure CLI versie 2.9.1 of hoger nodig. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI wilt installeren of upgraden, raadpleegt u [Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="common-samples"></a>Algemene voorbeelden

Deze voorbeelden zijn van toepassing op alle API's van Azure Cosmos DB.

|Taak | Beschrijving |
|---|---|
| [Regio's toevoegen of er een failover voor uitvoeren](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Een regio toevoegen, de failoverprioriteit wijzigen, een handmatige failover activeren.|
| [Accountsleutels en verbindingsreeksen](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Accountsleutels en alleen-lezensleutels weergeven, sleutels opnieuw genereren en verbindingsreeksen weergeven.|
| [Beveiligen met IP-firewall](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Een Cosmos-account maken met een geconfigureerde IP-firewall.|
| [Nieuw account beveiligen met service-eindpunten](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Een Cosmos-account maken en beveiligen met service-eindpunten.|
| [Bestaand account beveiligen met service-eind punten](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Een Cosmos-account bijwerken om het te beveiligen met service-eindpunten wanneer het subnet uiteindelijk is geconfigureerd.|
|||

## <a name="core-sql-api-samples"></a>API-voorbeelden Core (SQL)

|Taak | Beschrijving |
|---|---|
| [Een Azure Cosmos DB-account, -database en -container maken](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee maakt u een Azure Cosmos DB-account, -database en -container voor de Core (SQL) API. |
| [Een Azure Cosmos DB-account, -database en -container maken met automatische schaalaanpassing](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee maakt u een Azure Cosmos DB-account, -database en -container met automatische schaalaanpassing voor de Core (SQL) API. |
| [Doorvoer wijzigen](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | RU/s bijwerken voor een database en container.|
| [Resources vergrendelen tegen verwijdering](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling.|
|||

## <a name="mongodb-api-samples"></a>Voorbeelden van MongoDB-API

|Taak | Beschrijving |
|---|---|
| [Een Azure Cosmos-account, -database en -verzameling maken](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee maakt u een Azure Cosmos DB MongoDB-API-account, -database en -verzameling voor MongoDB-API. |
| [Een Azure Cosmos-account, -database met automatische schaalaanpassing en twee verzamelingen maken met gedeelde doorvoer maken](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee maakt u een Azure Cosmos-account, -database met automatische schaalaanpassing en twee verzamelingen met gedeelde doorvoer voor de MongoDB-API. |
| [Doorvoer wijzigen](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | RU/s bijwerken voor een database en verzameling.|
| [Resources vergrendelen tegen verwijdering](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling.|
|||

## <a name="cassandra-api-samples"></a>Voorbeelden van Cassandra-API

|Taak | Beschrijving |
|---|---|
| [Een Azure Cosmos-account, -keyspace en -tabel maken](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee wordt een Azure Cosmos-account, -keyspace en -tabel voor Cassandra API gemaakt. |
| [Een account, keyspace en tabel van Azure Cosmos met automatische schaalaanpassing maken](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee wordt een Azure Cosmos-account, -keyspace en -tabel met automatische schaalaanpassing voor de Cassandra-API gemaakt. |
| [Doorvoer wijzigen](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | RU/s in een keyspace en tabel bijwerken.|
| [Resources vergrendelen tegen verwijdering](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling.|
|||

## <a name="gremlin-api-samples"></a>Voorbeelden van Gremlin-API

|Taak | Beschrijving |
|---|---|
| [Een Azure Cosmos-account, -database en -grafiek maken](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee maakt u een Azure Cosmos DB-account, -database en -grafiek voor de Gremlin-API. |
| [Een Azure Cosmos DB-account, -database en -grafiek maken met automatische schaalaanpassing](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee maakt u een Azure Cosmos DB-account, -database en -grafiek met automatische schaalaanpassing voor de Gremlin-API. |
| [Doorvoer wijzigen](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | RU/s bijwerken voor een database en grafiek.|
| [Resources vergrendelen tegen verwijdering](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling.|
|||

## <a name="table-api-samples"></a>Voorbeelden van Table-API

|Taak | Beschrijving |
|---|---|
| [Een Azure Cosmos-account en -tabel maken](scripts/cli/table/create.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee worden een Azure Cosmos-account en -tabel gemaakt voor Table-API. |
| [Een account en tabel van Azure Cosmos met automatische schaalaanpassing maken](scripts/cli/table/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee maakt u een Azure Cosmos-account en -tabel met automatische schaalaanpassing voor de Table-API. |
| [Doorvoer wijzigen](scripts/cli/table/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | RU/s voor een tabel bijwerken.|
| [Resources vergrendelen tegen verwijdering](scripts/cli/table/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling.|
|||
