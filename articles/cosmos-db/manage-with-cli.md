---
title: Azure Cosmos DB-resources beheren met Azure CLI
description: Gebruik Azure CLI voor het beheren van uw Azure Cosmos DB-account,-data base en-containers.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mjbrown
ms.openlocfilehash: 325840f8961fac49e599f1aa567ad8d4137820b4
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705799"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Azure Cosmos-resources beheren met Azure CLI

In de volgende hand leiding worden algemene opdrachten beschreven voor het automatiseren van het beheer van uw Azure Cosmos DB accounts, data bases en containers met behulp van Azure CLI. Referentiepagina's voor alle Azure Cosmos DB CLI-opdrachten zijn beschikbaar in de [naslaginformatie voor Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb). U kunt ook meer voor beelden vinden in [Azure CLI-voor beelden voor Azure Cosmos DB](cli-samples.md), met inbegrip van het maken en beheren van Cosmos DB accounts, data bases en containers voor MongoDb, Gremlin, Cassandra en Table-API.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-an-azure-cosmos-db-account"></a>Maak een Azure Cosmos DB-account

Een Azure Cosmos DB-account maken met SQL API, sessie consistentie in de regio's vs-West 2 en VS-Oost 2:

> [!IMPORTANT]
> De naam van het Azure Cosmos-account moet kleine letters en minder dan 31 tekens bevatten.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 31 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

## <a name="add-or-remove-regions"></a>Regio's toevoegen of verwijderen

Maak een Azure Cosmos-account met twee regio's, voeg een regio toe en verwijder een regio.

> [!NOTE]
> U kunt niet tegelijkertijd regio's toevoegen of verwijderen `locations` en andere eigenschappen wijzigen voor een Azure Cosmos-account. Het wijzigen van regio's moet worden uitgevoerd als een afzonderlijke bewerking dan een andere wijziging in de account bron.
> [!NOTE]
> Met deze opdracht kunt u regio's toevoegen en verwijderen, maar kunt u geen failover-prioriteiten wijzigen of een hand matige failover starten. Zie [failover-prioriteit instellen](#set-failover-priority) en [hand matige failover activeren](#trigger-manual-failover).

```azurecli-interactive
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount' # must be lower case and <31 characters

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName= "South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False
```

## <a name="enable-multiple-write-regions"></a>Meerdere schrijf regio's inschakelen

Multi-Master inschakelen voor een Cosmos-account

```azurecli-interactive
# Update an Azure Cosmos account from single to multi-master
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

## <a name="set-failover-priority"></a>Prioriteit van failover instellen

De failover-prioriteit instellen voor een Azure Cosmos-account dat is geconfigureerd voor automatische failover

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2'=0 'South Central US'=1 'East US 2'=2
```

## <a name="enable-automatic-failover"></a>Automatische failover inschakelen

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

## <a name="trigger-manual-failover"></a>Hand matige failover activeren

> [!CAUTION]
> Als u de regio met prioriteit = 0 wijzigt, wordt er een hand matige failover geactiveerd voor een Azure Cosmos-account. Bij elke andere wijziging van de prioriteit wordt een failover niet geactiveerd.

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2'=0 'South Central US'=1 'West US 2'=2
```

## <a id="list-account-keys"></a>Alle account sleutels weer geven

Alle sleutels ophalen voor een Cosmos-account.

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

## <a name="list-read-only-account-keys"></a>Alleen-lezen-account sleutels weer geven

Alleen-lezen sleutels ophalen voor een Cosmos-account.

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

## <a name="list-connection-strings"></a>Verbindings reeksen weer geven

De verbindings reeksen ophalen voor een Cosmos-account.

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

## <a name="regenerate-account-key"></a>Account sleutel opnieuw genereren

Genereer een nieuwe sleutel voor een Cosmos-account.

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="create-a-database"></a>Database maken

Maak een Cosmos-data base.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

## <a name="create-a-database-with-shared-throughput"></a>Een Data Base maken met gedeelde door Voer

Maak een Cosmos-data base met een gedeelde door voer.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

## <a name="change-the-throughput-of-a-database"></a>De door Voer van een Data Base wijzigen

Verhoog de door Voer van een Cosmos-data base met 1000 RU/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

## <a name="create-a-container"></a>Een container maken

Maak een Cosmos-container met het standaard index beleid, de partitie sleutel en RU/s van 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

## <a name="create-a-container-with-ttl"></a>Een container met TTL maken

Maak een Cosmos-container met TTL ingeschakeld.

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'
databaseName = 'database1'
containerName = 'container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl = 86400
```

## <a name="create-a-container-with-a-custom-index-policy"></a>Een container met een aangepast index beleid maken

Maak een Cosmos-container met een aangepast index beleid, een ruimtelijke index, een samengestelde index, een partitie sleutel en RU/s van 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

## <a name="change-the-throughput-of-a-container"></a>De door Voer van een container wijzigen

Verhoog de door Voer van een Cosmos-container met 1000 RU/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure CLI:

- [Azure-CLI installeren](/cli/azure/install-azure-cli)
- [Azure CLI-referentie](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Aanvullende voor beelden van Azure CLI voor Azure Cosmos DB](cli-samples.md)
