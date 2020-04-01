---
title: Azure Cosmos DB maken en beheren met PowerShell
description: Gebruik Azure Powershell beheer uw Azure Cosmos-accounts, databases, containers en doorvoer.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: c8e833a4ba18520d8e354398cfd0d00525594d15
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365756"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Azure Cosmos DB SQL API-resources beheren met PowerShell

In de volgende handleiding wordt beschreven hoe u PowerShell kunt gebruiken voor het uitvoeren van scripts en het automatiseren van het beheer van Azure Cosmos DB-resources, waaronder accounts, databases, containers en doorvoer.

> [!NOTE]
> Voorbeelden in dit `Get-AzResource` `Set-AzResource` artikel gebruiken en Powershell-cmdlets voor Azure-resourcebewerkingen en [Az.CosmosDB-beheercmdlets.](https://docs.microsoft.com/powershell/module/az.cosmosdb) `Az.CosmosDB`cmdlets zijn nog steeds in preview en kunnen veranderen voordat ze algemeen beschikbaar zijn. Zie de referentiepagina van de [AZ.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) API voor eventuele updates van de opdrachten.

Zie `Get-Resource` / `Set-AzResource` Het azure [cosmos DB-bronproviderschema](/azure/templates/microsoft.documentdb/allversions) als u alle eigenschappen wilt weergeven die kunnen worden beheerd met PowerShell-cmdlets.

Voor cross-platform beheer van Azure Cosmos `Az` DB `Az.CosmosDB` u de cmdlets en cmdlets gebruiken met [cross-platform Powershell,](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)evenals de [Azure CLI,](manage-with-cli.md)de [REST API][rp-rest-api]of de [Azure-portal.](create-sql-api-dotnet.md#create-account)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Aan de slag

Volg de instructies in [Het installeren en configureren van Azure PowerShell][powershell-install-configure] om uw Azure-account in Powershell te installeren en aan te melden.

* `Set-AzureResource`wordt hieronder gebruikt. Het zal vragen om bevestiging van de gebruiker.  Als u liever uitvoert zonder dat de `-Force` gebruiker een bevestiging nodig heeft, moet u de vlag toevoegen aan de opdracht.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos-accounts

In de volgende secties wordt uitgelegd hoe u het Azure Cosmos-account beheert, waaronder:

* [Een Azure Cosmos-account maken](#create-account)
* [Een Azure Cosmos-account bijwerken](#update-account)
* [Alle Azure Cosmos-accounts in een abonnement weergeven](#list-accounts)
* [Een Azure Cosmos-account openen](#get-account)
* [Een Azure Cosmos-account verwijderen](#delete-account)
* [Tags bijwerken voor een Azure Cosmos-account](#update-tags)
* [Lijstsleutels voor een Azure Cosmos-account](#list-keys)
* [Sleutels regenereren voor een Azure Cosmos-account](#regenerate-keys)
* [Verbindingstekenreeksen voor een Azure Cosmos-account weergeven](#list-connection-strings)
* [Failoverprioriteit voor een Azure Cosmos-account wijzigen](#modify-failover-priority)
* [Een handmatige failover activeren voor een Azure Cosmos-account](#trigger-manual-failover)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Een Azure Cosmos-account maken

Met deze opdracht wordt een Azure Cosmos DB-databaseaccount gemaakt met [meerdere regio's,][distribute-data-globally] [automatisch failover-](how-to-manage-database-account.md#automatic-failover) en begrensd [consistentiebeleid](consistency-levels.md).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$locations = @("West US 2", "East US 2")
$accountName = "mycosmosaccount"
$apiKind = "GlobalDocumentDB"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000

New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
    -Location $locations -Name $accountName `
    -ApiKind $apiKind -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName`De Azure-brongroep waarin het Cosmos-account kan worden geïmplementeerd. Het moet al bestaan.
* `$locations`De regio's voor het databaseaccount, te beginnen met het schrijfgebied en geordend op failoverprioriteit.
* `$accountName`De naam voor het Azure Cosmos-account. Moet uniek zijn, kleine letters, omvatten alleen alfanumerieke en '-' tekens, en tussen 3 en 31 tekens in lengte.
* `$apiKind`Het type Cosmos-account dat u wilt maken. Zie [API's in Cosmos DB voor](introduction.md#develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis)meer informatie.
* `$consistencyPolicy`, `$maxStalenessInterval`en `$maxStalenessPrefix` het standaardconsistentieniveau en de instellingen van het Azure Cosmos-account. Zie [Consistentieniveaus in Azure Cosmos DB](consistency-levels.md)voor meer informatie.

Azure Cosmos-accounts kunnen worden geconfigureerd met IP Firewall, eindpunten van de Virtual Network-service en privéeindpunten. Zie [IP Firewall configureren](how-to-configure-firewall.md)voor informatie over het configureren van de IP Firewall voor Azure Cosmos DB. Zie [Toegang configureren vanuit virtuele netwerken](how-to-configure-vnet-service-endpoint.md)voor informatie over het inschakelen van serviceeindpunten voor Azure Cosmos DB. Zie [Toegang configureren vanaf privéeindpunten](how-to-configure-private-endpoints.md)voor informatie over het inschakelen van privéeindpunten voor Azure Cosmos DB.

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a>Alle Azure Cosmos-accounts in een resourcegroep weergeven

Met deze opdracht worden alle Azure Cosmos-accounts in een resourcegroep weergegeven.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a>De eigenschappen van een Azure Cosmos-account oppakken

Met deze opdracht u de eigenschappen van een bestaand Azure Cosmos-account opdoen.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a>Een Azure Cosmos-account bijwerken

Met deze opdracht u de eigenschappen van uw Azure Cosmos DB-databaseaccount bijwerken. Eigenschappen die kunnen worden bijgewerkt, zijn onder andere:

* Regio's toevoegen of verwijderen
* Standaardconsistentiebeleid wijzigen
* Ip-bereikfilter wijzigen
* Configuraties van virtuele netwerken wijzigen
* Multimaster inschakelen

> [!NOTE]
> U niet tegelijkertijd `locations` regio's toevoegen of verwijderen en andere eigenschappen wijzigen voor een Azure Cosmos-account. Het wijzigen van regio's moet worden uitgevoerd als een afzonderlijke bewerking van elke andere wijziging in het account.
> [!NOTE]
> Met deze opdracht u regio's toevoegen en verwijderen, maar u failoverprioriteiten wijzigen of een handmatige failover activeren. Zie [Failoverprioriteit wijzigen](#modify-failover-priority) en [handmatige failover activeren](#trigger-manual-failover).

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$locations = @("West US 2", "East US 2")
$accountName = "mycosmosaccount"
$apiKind = "GlobalDocumentDB"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Location $locations -Name $accountName `
    -ApiKind $apiKind -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Region operations
$resourceType = "Microsoft.DocumentDb/databaseAccounts"
$apiVersion = "2020-03-01"

# Add a region to the account
$locations2 = @("West US 2", "East US 2", "South Central US")
$locationObjects2 = @()
$i = 0
ForEach ($location in $locations2) { $locationObjects2 += @{ locationName = "$location"; failoverPriority = $i++ } }
$accountProperties = @{
    databaseAccountOfferType = "Standard";
    locations = $locationObjects2;
    enableAutomaticFailover = $enableAutomaticFailover;
}

Set-AzResource -ResourceType $resourceType `
    -ResourceGroupName $resourceGroupName `
    -ApiVersion $apiVersion -Name $accountName `
    -PropertyObject $accountProperties

Write-Host "Set-AzResource returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove a region from the account
$locations3 = @("West US 2", "South Central US")
$locationObjects3 = @()
$i = 0
ForEach ($location in $locations3) { $locationObjects3 += @{ locationName = "$location"; failoverPriority = $i++ } }
$accountProperties = @{
    databaseAccountOfferType = "Standard";
    locations = $locationObjects3;
    enableAutomaticFailover = $enableAutomaticFailover;
}

Set-AzResource -ResourceType $resourceType `
    -ResourceGroupName $resourceGroupName `
    -ApiVersion $apiVersion -Name $accountName `
    -PropertyObject $accountProperties

Write-Host "Set-AzResource returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```
### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-master"></a>Meerdere schrijfregio's inschakelen voor een Azure Cosmos-account

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
    -Name $accountName -PassThru
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a>Tags van een Azure Cosmos-account bijwerken

Met deze opdracht worden de [Azure-brontags][azure-resource-tags] voor een Azure Cosmos-account ingesteld. Tags kunnen zowel bij het `New-AzCosmosDBAccount` aanmaken van een `Update-AzCosmosDBAccount`account worden ingesteld met behulp van als bij accountupdate met behulp van .

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a>Accountsleutels aanbieden

Wanneer u een Azure Cosmos-account maakt, genereert de service twee hoofdtoegangssleutels die kunnen worden gebruikt voor verificatie wanneer het Azure Cosmos-account wordt geopend. Alleen-lezen sleutels voor het verifiëren van alleen-lezen bewerkingen worden ook gegenereerd.
Door twee toegangssleutels te bieden, u met Azure Cosmos DB één sleutel tegelijk regenereren en roteren zonder onderbreking van uw Azure Cosmos-account.
Cosmos DB-accounts hebben twee lees-schrijftoetsen (primair en secundair) en twee alleen-lezen sleutels (primair en secundair).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a>Verbindingstekenreeksen voor lijst

Met de volgende opdracht worden verbindingstekenreeksen opgehaald om apps te verbinden met het Cosmos DB-account.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a>Accountsleutels regenereren

Toegangssleutels voor een Azure Cosmos-account moeten periodiek worden geregenereerd om verbindingen veilig te houden. Er worden primaire en secundaire toegangssleutels aan het account toegewezen. Hierdoor kunnen clients de toegang behouden terwijl één sleutel tegelijk wordt geregenereerd.
Er zijn vier typen sleutels voor een Azure Cosmos-account (Primair, Secundair, PrimaryReadonly en SecondaryReadonly)

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a>Automatische failover inschakelen

Met de volgende opdracht wordt een Cosmos DB-account automatisch naar de secundaire regio mislukt als de primaire regio niet meer beschikbaar is.

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

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a>Prioriteit failover wijzigen

Voor accounts die zijn geconfigureerd met Automatische failover, u de volgorde wijzigen waarin Cosmos secundaire replica's naar primair promoveert als de primaire niet beschikbaar is.

Neem in het onderstaande voorbeeld aan `West US 2 = 0` `East US 2 = 1`dat `South Central US = 2`de huidige prioriteit voor failover , . De opdracht verandert `West US 2 = 0`dit `South Central US = 1` `East US 2 = 2`in , , .

> [!CAUTION]
> Als u `failoverPriority=0` de locatie wijzigt, wordt een handmatige failover voor een Azure Cosmos-account geactiveerd. Andere prioriteitswijzigingen leiden niet tot een failover.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a>Handmatige failover activeren

Voor accounts die zijn geconfigureerd met Handmatige failover, u elke `failoverPriority=0`secundaire replica niet overdoen en promoten naar primaire door te wijzigen in. Deze bewerking kan worden gebruikt om een noodherstelboor te starten om de planning voor noodherstel te testen.

Neem voor het onderstaande voorbeeld aan dat `West US 2 = 0` het `East US 2 = 1` account een huidige failoverprioriteit heeft van en de regio's omdraait.

> [!CAUTION]
> Als `locationName` `failoverPriority=0` u voor gaat, wordt een handmatige failover voor een Azure Cosmos-account geactiveerd. Elke andere prioriteitswijziging leidt niet tot een failover.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB-database

In de volgende secties wordt uitgelegd hoe u de Azure Cosmos DB-database beheert, waaronder:

* [Een Azure Cosmos DB-database maken](#create-db)
* [Een Azure Cosmos DB-database maken met gedeelde doorvoer](#create-db-ru)
* [De doorvoer van een Azure Cosmos DB-database krijgen](#get-db-ru)
* [Alle Azure Cosmos DB-databases in een account weergeven](#list-db)
* [Eén Azure Cosmos DB-database](#get-db)
* [Een Azure Cosmos DB-database verwijderen](#delete-db)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Een Azure Cosmos DB-database maken

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Set-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>Een Azure Cosmos DB-database maken met gedeelde doorvoer

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

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>De doorvoer van een Azure Cosmos DB-database krijgen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>Alle Azure Cosmos DB-databases in een account opvragen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>Eén Azure Cosmos DB-database

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Een Azure Cosmos DB-database verwijderen

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

In de volgende secties wordt uitgelegd hoe u de Azure Cosmos DB-container beheert, waaronder:

* [Een Azure Cosmos DB-container maken](#create-container)
* [Een Azure Cosmos DB-container maken met een grote partitiesleutel](#create-container-big-pk)
* [De doorvoer van een Azure Cosmos DB-container krijgen](#get-container-ru)
* [Een Azure Cosmos DB-container maken met aangepaste indexering](#create-container-custom-index)
* [Een Azure Cosmos DB-container maken waarbij indexering is uitgeschakeld](#create-container-no-index)
* [Een Azure Cosmos DB-container maken met unieke sleutel en TTL](#create-container-unique-key-ttl)
* [Een Azure Cosmos DB-container maken met conflictoplossing](#create-container-lww)
* [Alle Azure Cosmos DB-containers in een database weergeven](#list-containers)
* [Eén Azure Cosmos DB-container in een database](#get-container)
* [Een Azure Cosmos DB-container verwijderen](#delete-container)

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

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Een Azure Cosmos DB-container maken met een grote grootte van de partitiesleutel

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

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>De doorvoer van een Azure Cosmos DB-container krijgen

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

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>Een Azure Cosmos DB-container maken met aangepast indexbeleid

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

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>Een Azure Cosmos DB-container maken waarbij indexering is uitgeschakeld

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

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Een Azure Cosmos DB-container maken met uniek sleutelbeleid en TTL

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

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>Een Azure Cosmos DB-container maken met conflictoplossing

Als u een conflictoplossingsbeleid wilt `"mode"="custom"` maken om een opgeslagen procedure te gebruiken, stelt u het oplossingspad in en stelt u het oplossingspad in als de naam van de opgeslagen procedure. `"conflictResolutionPath"="myResolverStoredProcedure"` Als u alle conflicten naar de ConflictsFeed `"mode"="custom"` wilt schrijven en afzonderlijk wilt verwerken,`"conflictResolutionPath"=""`

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

### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>Alle Azure Cosmos DB-containers in een database weergeven

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

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>Eén Azure Cosmos DB-container in een database

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

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Een Azure Cosmos DB-container verwijderen

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

* [Alle PowerShell-monsters](powershell-samples.md)
* [Azure Cosmos-account beheren](how-to-manage-database-account.md)
* [Een Azure Cosmos DB-container maken](how-to-create-container.md)
* [Time-to-live configureren in Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
