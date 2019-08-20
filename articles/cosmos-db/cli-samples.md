---
title: Voorbeelden van Azure CLI voor Azure Cosmos DB
description: Voorbeelden van Azure CLI - Azure DB Cosmos-accounts, -databases, -containers, -regio's en -firewalls maken en beheren.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: d7ce5e2c12feeee770de8acfd5df81d340e0a7d0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615575"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Voorbeelden van Azure CLI voor Azure Cosmos DB

De volgende tabel bevat koppelingen naar Azure CLI-voorbeeldscripts voor Azure Cosmos DB. Referentiepagina's voor alle Azure Cosmos DB CLI-opdrachten zijn beschikbaar in de [naslaginformatie voor Azure CLI](/cli/azure/cosmosdb).

| |  |
|---|---|
|**Azure Cosmos DB-account, -database en -containers maken**||
| [Een Azure Cosmos DB-account maken met behulp van de SQL-API](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee maakt u één Azure Cosmos DB-account, -database en -container. |
| [Een Azure Cosmos DB-account maken met behulp van de API van Cosmo DB voor MongoDB](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Hiermee maakt u één Azure Cosmos DB-account, -database en -verzameling. |
| [Een Azure Cosmos DB-account maken met behulp van de Gremlin-API](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Hiermee maakt u één Azure Cosmos DB-account, -database en -grafiek. |
| [Een Azure Cosmos DB-account maken met behulp van de Cassandra-API](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Hiermee maakt u één Azure Cosmos DB-account en database. |
| [Een Azure Cosmos DB-account maken met behulp van de Table-API](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Hiermee maakt u één Azure Cosmos DB-account, -database en -tabel. |
|**Azure Cosmos DB schalen**||
| [Doorvoer van container schalen](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Hiermee wordt de ingerichte doorvoer voor een container gewijzigd.|
| [Azure Cosmos-database account in meerdere regio's repliceren en failover-prioriteiten configureren](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Hiermee worden accountgegevens in meerdere regio's met een bepaalde failover-prioriteit globaal gerepliceerd.|
|**Azure Cosmos DB beveiligen**||
| [Accountsleutels ophalen](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Hiermee worden de primaire en secundaire hoofdsleutels voor schrijven en de primaire en secundaire sleutels voor alleen-lezen voor het account opgehaald.|
| [De verbindingsreeks ophalen voor het Cosmos-account dat is geconfigureerd met de API van Azure Cosmos DB voor MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Hiermee wordt de verbindingsreeks opgehaald om de MongoDB-app te verbinden met uw Azure Cosmos DB-account.|
| [Accountsleutels opnieuw genereren](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Genereer de sleutels voor het account opnieuw.|
| [Firewall maken](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee wordt toegangsbeheerbeleid voor inkomende IP's gemaakt om de toegang tot het account vanaf een goedgekeurde set computers en/of cloudservices te beperken.|
|**Hoge beschikbaarheid, herstel na noodgevallen, back-up en herstel**||
| [Failoverbeleid configureren](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Hiermee wordt de prioriteit van failover ingesteld voor elke regio waarin het account wordt gerepliceerd.|
|**Azure Cosmos DB verbinden met resources**||
| [Een web-app verbinden met Azure Cosmos DB](../app-service/scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Maak en koppel een Azure Cosmos-data base en een Azure-web-app.|
|||
