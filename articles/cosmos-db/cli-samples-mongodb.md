---
title: Azure CLI-samples voor Azure Cosmos DB MongoDB API
description: Azure CLI-samples voor Azure Cosmos DB MongoDB API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.author: mjbrown
ms.openlocfilehash: f7807a4c2024e16f563adbcb46a5c60e5c542dda
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77524560"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Azure CLI-samples voor Azure Cosmos DB MongoDB API

De volgende tabel bevat koppelingen naar voorbeeld van Azure CLI-scripts voor Azure Cosmos DB MongoDB API. Referentiepagina's voor alle Azure Cosmos DB CLI-opdrachten zijn beschikbaar in de [Azure CLI-verwijzing](/cli/azure/cosmosdb). Alle Azure Cosmos DB CLI-scriptvoorbeelden zijn te vinden in de [Azure Cosmos DB CLI GitHub Repository.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)

> [!NOTE]
> Momenteel u alleen 3.2-versie maken (dat wil `*.documents.azure.com`zeggen accounts met behulp van het eindpunt in de indeling) van de API van Azure Cosmos DB voor MongoDB-accounts met behulp van PowerShell-, CLI- en Resource Manager-sjablonen. Als u een 3.6-versie van accounts wilt maken, gebruikt u in plaats daarvan Azure-portal.

| |  |
|---|---|
| [Een Azure Cosmos-account, -database en -verzameling maken](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee maakt u een Azure Cosmos DB-account, -database en -verzameling voor De MongoDB-API. |
| [Doorvoer wijzigen](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Update RU/s in een database en verzameling.|
| [Regio's toevoegen of mislukken](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Voeg een regio toe, wijzig de prioriteit van failover, activeer een handmatige failover.|
| [Accountsleutels en verbindingstekenreeksen](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Lijst accountsleutels, alleen-lezensleutels, regenereren sleutels en lijstverbindingstekenreeksen.|
| [Beveiligen met IP-firewall](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Maak een Cosmos-account met IP-firewall geconfigureerd.|
| [Nieuwe account beveiligen met serviceeindpunten](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Maak een Cosmos-account aan en beveilig met service-endpoints.|
| [Bestaande account beveiligen met serviceeindpunten](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Werk een Cosmos-account bij om te beveiligen met service-endpoints wanneer het subnet uiteindelijk is geconfigureerd.|
|||
