---
title: Azure Cosmos DB maken en beheren met Power shell
description: Gebruik Azure Power shell voor het beheren van uw Azure Cosmos-accounts,-data bases,-containers en-door voer.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/29/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: d4473bbfe10fa2d0fc87eed7889a3e06af650b5b
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592142"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Azure Cosmos DB SQL-API-resources beheren met Power shell

In de volgende handleiding wordt beschreven hoe u PowerShell kunt gebruiken voor het uitvoeren van scripts en het automatiseren van het beheer van Azure Cosmos DB-resources, waaronder accounts, databases, containers en doorvoer.

> [!NOTE]
> Voor beelden in dit artikel gebruiken [AZ. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) Management-cmdlets. Deze cmdlets bevinden zich nog in de preview-versie en kunnen worden gewijzigd voordat ze algemeen beschikbaar zijn. Zie de pagina [AZ. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) API Reference (Engelstalig) voor updates van de opdrachten.

`Az` Voor platformoverschrijdende beheer van Azure Cosmos DB kunt u de cmdlets en `Az.CosmosDB` gebruiken met [cross-platform Power shell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell), evenals de [Azure cli](manage-with-cli.md), de [rest API][rp-rest-api]of de [Azure Portal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Aan de slag

Volg de instructies in het [installeren en configureren van Azure PowerShell][powershell-install-configure] om uw Azure-account in Power shell te installeren en u aan te melden.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos-accounts

In de volgende secties ziet u hoe u het Azure Cosmos-account kunt beheren, met inbegrip van:

* [Een Azure Cosmos-account maken](#create-account)
* [Een Azure Cosmos-account bijwerken](#update-account)
* [Alle Azure Cosmos-accounts in een abonnement weer geven](#list-accounts)
* [Een Azure Cosmos-account ophalen](#get-account)
* [Een Azure Cosmos-account verwijderen](#delete-account)
* [Tags voor een Azure Cosmos-account bijwerken](#update-tags)
* [Sleutels voor een Azure Cosmos-account weer geven](#list-keys)
* [Sleutels voor een Azure Cosmos-account opnieuw genereren](#regenerate-keys)
* [Verbindings reeksen weer geven voor een Azure Cosmos-account](#list-connection-strings)
* [De failover-prioriteit voor een Azure Cosmos-account wijzigen](#modify-failover-priority)
* [Een hand matige failover voor een Azure Cosmos-account activeren](#trigger-manual-failover)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Een Azure Cosmos-account maken

Met deze opdracht maakt u een Azure Cosmos DB database account met [meerdere regio's][distribute-data-globally], [automatische failover](how-to-manage-database-account.md#automatic-failover) en het [consistentie beleid](consistency-levels.md)voor gebonden veroudering.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$locations = @("West US 2", "East US 2")
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Location $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName`De Azure-resource groep waarin het Cosmos-account moet worden geïmplementeerd. Deze moet al bestaan.
* `$locations`De regio's voor het database account, beginnend met de schrijf regio en geordend op basis van de failover-prioriteit.
* `$accountName`De naam voor het Azure Cosmos-account. Moet uniek, kleine letters, alleen alfanumerieke tekens en '-' bevatten, en tussen de 3 en 31 tekens lang zijn.
* `$apiKind`Het type Cosmos-account dat moet worden gemaakt. Zie [api's in Cosmos DB](introduction.md#develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis)voor meer informatie.
* `$consistencyPolicy`, `$maxStalenessInterval`en `$maxStalenessPrefix` het standaard consistentie niveau en instellingen van het Azure Cosmos-account. Zie [consistentie niveaus in azure Cosmos DB](consistency-levels.md)voor meer informatie.

Azure Cosmos-accounts kunnen worden geconfigureerd met IP-firewall, Virtual Network Service-eind punten en privé-eind punten. Zie [Configure IP firewall](how-to-configure-firewall.md)(Engelstalig) voor meer informatie over het configureren van de IP-firewall voor Azure Cosmos db. Zie [toegang vanaf virtuele netwerken configureren](how-to-configure-vnet-service-endpoint.md)voor meer informatie over het inschakelen van service-eind punten voor Azure Cosmos db. Zie [toegang vanaf privé-eind punten configureren](how-to-configure-private-endpoints.md)voor meer informatie over het inschakelen van privé-eind punten voor Azure Cosmos db.

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a>Alle Azure Cosmos-accounts in een resource groep weer geven

Met deze opdracht worden alle Azure Cosmos-accounts in een resource groep weer gegeven.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a>De eigenschappen van een Azure Cosmos-account ophalen

Met deze opdracht kunt u de eigenschappen van een bestaand Azure Cosmos-account ophalen.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a>Een Azure Cosmos-account bijwerken

Met deze opdracht kunt u de eigenschappen van uw Azure Cosmos DB-database account bijwerken. Eigenschappen die kunnen worden bijgewerkt zijn onder andere het volgende:

* Regio's toevoegen of verwijderen
* Standaard consistentie beleid wijzigen
* IP-bereik filter wijzigen
* Virtual Network configuraties wijzigen
* Multi-Master inschakelen

> [!NOTE]
> U kunt geen regio's (`locations`) tegelijkertijd toevoegen of verwijderen en andere eigenschappen wijzigen voor een Azure Cosmos-account. Het wijzigen van regio's moet worden uitgevoerd als een afzonderlijke bewerking van een andere wijziging in het account.
> [!NOTE]
> Met deze opdracht kunt u regio's toevoegen en verwijderen, maar kunt u geen failover-prioriteiten wijzigen of een hand matige failover starten. Zie [failover-prioriteit wijzigen](#modify-failover-priority) en [hand matige failover activeren](#trigger-manual-failover).

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$locations = @("West US 2", "East US 2")
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true

# Create the Cosmos DB account
New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Location $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Add a region to the account
$locations2 = @("West US 2", "East US 2", "South Central US")
$locationObjects2 = @()
$i = 0
ForEach ($location in $locations2) {
    $locationObjects2 += @{ locationName = "$location"; failoverPriority = $i++ }
}

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObjects2

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove a region from the account
$locations3 = @("West US 2", "South Central US")
$locationObjects3 = @()
$i = 0
ForEach ($location in $locations3) {
    $locationObjects3 += @{ locationName = "$location"; failoverPriority = $i++ }
}

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObjects3

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```
### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-master"></a>Meerdere schrijf regio's inschakelen voor een Azure Cosmos-account

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-master on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-master
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a>Een Azure Cosmos-account verwijderen

Met deze opdracht wordt een bestaand Azure Cosmos-account verwijderd.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -PassThru:$true
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a>Tags van een Azure Cosmos-account bijwerken

Met deze opdracht worden de [Azure-resource Tags][azure-resource-tags] ingesteld voor een Azure Cosmos-account. Labels kunnen worden ingesteld bij het maken van accounts `New-AzCosmosDBAccount` en met behulp van de account `Update-AzCosmosDBAccount`update met.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a>Lijst met account sleutels

Wanneer u een Azure Cosmos-account maakt, genereert de service twee hoofd toegangs sleutels die kunnen worden gebruikt voor verificatie wanneer het Azure Cosmos-account wordt geopend. Alleen-lezen sleutels voor de verificatie van alleen-lezen bewerkingen worden ook gegenereerd.
Met behulp van twee toegangs sleutels kunt u met Azure Cosmos DB per keer één sleutel genereren en draaien zonder onderbreking van uw Azure Cosmos-account.
Cosmos DB accounts hebben twee lees-schrijf sleutels (primair en secundair) en twee alleen-lezen sleutels (primair en secundair).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a>Verbindings reeksen weer geven

Met de volgende opdracht worden verbindings reeksen opgehaald om apps te verbinden met het Cosmos DB-account.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a>Account sleutels opnieuw genereren

Toegangs sleutels voor een Azure Cosmos-account moeten periodiek opnieuw worden gegenereerd om de verbindingen te beveiligen. Een primaire en secundaire toegangs sleutel worden toegewezen aan het account. Hierdoor kunnen clients de toegang behouden terwijl één sleutel tegelijk opnieuw wordt gegenereerd.
Er zijn vier soorten sleutels voor een Azure Cosmos-account (primair, secundair, PrimaryReadonly en SecondaryReadonly)

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a>Automatische failover inschakelen

Met de volgende opdracht stelt u een Cosmos DB-account in om automatisch een failover naar de secundaire regio uit te stellen, anders wordt de primaire regio niet meer beschikbaar.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-master - cannot have both automatic
# failover and multi-master on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster

# Now enable automatic failover
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a>Prioriteit van failover wijzigen

Voor accounts die zijn geconfigureerd met automatische failover, kunt u de volg orde wijzigen waarin Cosmos secundaire replica's naar primair promo veren, anders is de primaire replica niet meer beschikbaar.

In het onderstaande voor beeld wordt ervan uitgegaan dat de `West US 2 = 0`huidige `East US 2 = 1`failover `South Central US = 2`-prioriteit,,,. De opdracht wordt gewijzigd in `West US 2 = 0`, `South Central US = 1`,. `East US 2 = 2`

> [!CAUTION]
> Als u de locatie `failoverPriority=0` voor wijzigt, wordt een hand matige failover geactiveerd voor een Azure Cosmos-account. Bij eventuele andere wijzigingen van de prioriteit wordt geen failover geactiveerd.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a>Hand matige failover activeren

Voor accounts die zijn geconfigureerd met hand matige failover, kunt u een failover uitvoeren en een secundaire replica promo veren `failoverPriority=0`naar primair door te wijzigen in. Deze bewerking kan worden gebruikt om een nood herstel analyse te initiëren om planning voor nood herstel te testen.

In het onderstaande voor beeld wordt ervan uitgegaan dat het account een huidige `West US 2 = 0` failover `East US 2 = 1` -prioriteit heeft en de regio's spiegelt.

> [!CAUTION]
> Als `locationName` u `failoverPriority=0` wijzigt voor, wordt een hand matige failover geactiveerd voor een Azure Cosmos-account. Bij elke andere wijziging van de prioriteit wordt een failover niet geactiveerd.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB-Data Base

In de volgende secties ziet u hoe u de Azure Cosmos DB-Data Base kunt beheren, met inbegrip van:

* [Een Azure Cosmos DB-Data Base maken](#create-db)
* [Een Azure Cosmos DB-Data Base maken met gedeelde door Voer](#create-db-ru)
* [De door Voer van een Azure Cosmos DB-Data Base ophalen](#get-db-ru)
* [Alle Azure Cosmos DB data bases in een account weer geven](#list-db)
* [Eén Azure Cosmos DB Data Base ophalen](#get-db)
* [Een Azure Cosmos DB-Data Base verwijderen](#delete-db)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Een Azure Cosmos DB-Data Base maken

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Set-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>Een Azure Cosmos DB-Data Base maken met gedeelde door Voer

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

Set-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>De door Voer van een Azure Cosmos DB-Data Base ophalen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>Alle Azure Cosmos DB-data bases in een account ophalen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>Eén Azure Cosmos DB Data Base ophalen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Een Azure Cosmos DB-Data Base verwijderen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB-container

In de volgende secties wordt uitgelegd hoe u de Azure Cosmos DB-container beheert, met inbegrip van:

* [Een Azure Cosmos DB-container maken](#create-container)
* [Een Azure Cosmos DB container maken met een grote partitie sleutel](#create-container-big-pk)
* [De door Voer van een Azure Cosmos DB container ophalen](#get-container-ru)
* [Een Azure Cosmos DB-container maken met aangepaste indexering](#create-container-custom-index)
* [Een Azure Cosmos DB-container maken waarvoor indexeren is uitgeschakeld](#create-container-no-index)
* [Een Azure Cosmos DB-container maken met een unieke sleutel en TTL](#create-container-unique-key-ttl)
* [Een Azure Cosmos DB-container maken met conflict oplossing](#create-container-lww)
* [Alle Azure Cosmos DB containers in een Data Base weer geven](#list-containers)
* [Een enkele Azure Cosmos DB-container in een Data Base ophalen](#get-container)
* [Een Azure Cosmos DB container verwijderen](#delete-container)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Een Azure Cosmos DB-container maken

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Een Azure Cosmos DB container maken met een grotere partitie sleutel grootte

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>De door Voer van een Azure Cosmos DB container ophalen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainerThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>Een Azure Cosmos DB-container maken met aangepast index beleid

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$indexPathIncluded = "/*"
$indexPathExcluded = "/myExcludedPath/*"

$includedPathIndex = New-AzCosmosDBSqlIncludedPathIndex -DataType String -Kind Range
$includedPath = New-AzCosmosDBSqlIncludedPath -Path $indexPathIncluded -Index $includedPathIndex

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IncludedPath $includedPath `
    -ExcludedPath $indexPathExcluded `
    -IndexingMode Consistent `
    -Automatic $true

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>Een Azure Cosmos DB-container maken waarvoor indexeren is uitgeschakeld

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Een Azure Cosmos DB-container maken met uniek sleutel beleid en TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$uniqueKeyPath = "/myUniqueKeyPath"
$ttlInSeconds = 86400 # Set this to -1 (or don't use it at all) to never expire

$uniqueKey = New-AzCosmosDBSqlUniqueKey `
    -Path $uniqueKeyPath

$uniqueKeyPolicy = New-AzCosmosDBSqlUniqueKeyPolicy `
    -UniqueKey $uniqueKey

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>Een Azure Cosmos DB-container maken met conflict oplossing

Als u alle conflicten naar de ConflictsFeed wilt schrijven en afzonderlijk wilt `-Type "Custom" -Path ""`verwerken, geeft u door.

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionPath = "/myResolutionPath"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type LastWriterWins `
    -Path $conflictResolutionPath

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

Voor het maken van een beleid voor conflict oplossing voor het gebruik van `New-AzCosmosDBSqlConflictResolutionPolicy` een opgeslagen procedure `-Type` , `-ConflictResolutionProcedure`para meters voor aanroepen en door geven en.

```azurepowershell-interactive
# Create container with custom conflict resolution policy using a stored procedure
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionSprocName = "mysproc"

$conflictResolutionSproc = "/dbs/$databaseName/colls/$containerName/sprocs/$conflictResolutionSprocName"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type Custom `
    -ConflictResolutionProcedure $conflictResolutionSproc

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```


### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>Alle Azure Cosmos DB containers in een Data Base weer geven

```azurepowershell-interactive
# List all Azure Cosmos DB containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName
```

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>Een enkele Azure Cosmos DB-container in een Data Base ophalen

```azurepowershell-interactive
# Get a single Azure Cosmos DB container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Een Azure Cosmos DB container verwijderen

```azurepowershell-interactive
# Delete an Azure Cosmos DB container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Remove-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

## <a name="next-steps"></a>Volgende stappen

* [Alle Power shell-voor beelden](powershell-samples.md)
* [Azure Cosmos-account beheren](how-to-manage-database-account.md)
* [Een Azure Cosmos DB-container maken](how-to-create-container.md)
* [Time-to-Live configureren in Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
