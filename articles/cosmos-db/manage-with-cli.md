---
title: Azure Cosmos DB-resources beheren met Azure CLI
description: Gebruik Azure CLI voor het beheren van uw Azure Cosmos DB-account,-data base en-containers.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: f9d8bf9161343e4b36a3c16209873962b69d8af5
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615222"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Azure Cosmos-resources beheren met Azure CLI

In de volgende hand leiding worden algemene opdrachten beschreven voor het automatiseren van het beheer van uw Azure Cosmos DB accounts, data bases en containers met behulp van Azure CLI. Referentiepagina's voor alle Azure Cosmos DB CLI-opdrachten zijn beschikbaar in de [naslaginformatie voor Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb). U kunt ook meer voor beelden vinden in [Azure CLI-voor beelden voor Azure Cosmos DB](cli-samples.md), met inbegrip van het maken en beheren van Cosmos DB accounts, data bases en containers voor MongoDb, Gremlin, Cassandra en Table-API.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-an-azure-cosmos-db-account"></a>Maak een Azure Cosmos DB-account

Als u een Azure Cosmos DB-account wilt maken met SQL API, sessie consistentie in de regio's VS-Oost en VS-West, voert u de volgende opdracht uit:

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount # must be lowercase and < 31 characters \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=EastUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=WestUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> De naam van het Azure Cosmos-account moet kleine letters zijn.

## <a name="create-a-database"></a>Een database maken

Als u een Cosmos-Data Base wilt maken, voert u de volgende opdracht uit:

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>Een container maken

Als u een Cosmos-container met RU/s 400 en een partitie sleutel wilt maken, voert u de volgende opdracht uit:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --partition-key-path /myPartitionKey \
   --throughput 400
```

## <a name="change-the-throughput-of-a-container"></a>De door Voer van een container wijzigen

Als u de door Voer van een Cosmos-container wilt wijzigen in 1000 RU/s, voert u de volgende opdracht uit:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --throughput 1000
```

## <a name="list-account-keys"></a>Lijst met account sleutels

Als u de sleutels voor uw Cosmos-account wilt ophalen, voert u de volgende opdracht uit:

```azurecli-interactive
# List account keys
az cosmosdb keys list \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>Verbindings reeksen weer geven

Als u de verbindings reeksen voor uw Cosmos-account wilt ophalen, voert u de volgende opdracht uit:

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>Account sleutel opnieuw genereren

Als u een nieuwe primaire sleutel voor uw Cosmos-account opnieuw wilt genereren, voert u de volgende opdracht uit:

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure CLI:

- [Azure-CLI installeren](/cli/azure/install-azure-cli)
- [Azure CLI-referentie](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Aanvullende voor beelden van Azure CLI voor Azure Cosmos DB](cli-samples.md)
