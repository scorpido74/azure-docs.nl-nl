---
title: Azure Cosmos DB maken en beheren met Power shell
description: Gebruik Azure Power shell om uw Azure Cosmos DB accounts, data bases, containers en door voer te beheren.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/23/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 978f37d08275de704dd01c0251dde42665fca552
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882110"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Azure Cosmos DB SQL-API-resources beheren met Power shell

In de volgende hand leiding wordt beschreven hoe u Power shell gebruikt voor het uitvoeren van scripts en het automatiseren van het beheer van Azure Cosmos DB resources, waaronder account, Data Base, container en door voer. Het beheer van Azure Cosmos DB wordt rechtstreeks door de AzResource-cmdlet aan de Azure Cosmos DB resource Provider verwerkt. Als u alle eigenschappen wilt weer geven die kunnen worden beheerd met behulp van Power shell voor de resource provider van Azure Cosmos DB, raadpleegt u [Azure Cosmos DB resource provider-schema](/azure/templates/microsoft.documentdb/allversions)

Voor meerdere platform beheer van Azure Cosmos DB kunt u [Azure cli](manage-with-cli.md), de [REST API][rp-rest-api]of de [Azure Portal](create-sql-api-dotnet.md#create-account)gebruiken.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Aan de slag

Volg de instructies in het [installeren en configureren van Azure PowerShell][powershell-install-configure] om uw Azure-account in Power shell te installeren en u aan te melden.

* Als u de volgende opdrachten wilt uitvoeren zonder dat de gebruiker om bevestiging wordt gevraagd, voegt u de vlag `-Force` toe aan de opdracht.
* De volgende opdrachten zijn synchroon.

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

### <a id="create-account"></a>Een Azure Cosmos-account maken

Met deze opdracht maakt u een Azure Cosmos-database account met een [consistentie beleid](consistency-levels.md)met [meerdere regio's][distribute-data-globally], afhankelijk van de veroudering.

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lowercase and < 31 characters .

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="false"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` de naam voor het Azure Cosmos-account. Moet kleine letters, alfanumerieke tekens en het teken '-' en tussen de 3 en 31 tekens bevatten.
* `$location` de locatie voor de resource van het Azure Cosmos-account.
* `$locations` de replica regio's voor het database account. Er moet één schrijf regio per database account zijn met een failover-prioriteits waarde van 0.
* `$consistencyPolicy` het standaard consistentie niveau van het Azure Cosmos-account. Zie [consistentie niveaus in azure Cosmos DB](consistency-levels.md)voor meer informatie.
* `$CosmosDBProperties` de eigenschaps waarden die zijn door gegeven aan de provider van Cosmos DB Azure Resource Manager om het account in te richten.

Azure Cosmos-accounts kunnen worden geconfigureerd met IP-firewall en Virtual Network Service-eind punten. Zie [Configure IP firewall](how-to-configure-firewall.md)(Engelstalig) voor meer informatie over het configureren van de IP-firewall voor Azure Cosmos db.  Zie [toegang vanaf virtuele netwerken configureren](how-to-configure-vnet-service-endpoint.md)voor meer informatie over het inschakelen van service-eind punten voor Azure Cosmos db.

### <a id="list-accounts"></a>Alle Azure Cosmos-accounts in een abonnement weer geven

Met deze opdracht kunt u alle Azure Cosmos-accounts in een abonnement weer geven.

```azurepowershell-interactive
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

### <a id="get-account"></a>De eigenschappen van een Azure Cosmos-account ophalen

Met deze opdracht kunt u de eigenschappen van een bestaand Azure Cosmos-account ophalen.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a>Een Azure Cosmos-account bijwerken

Met deze opdracht kunt u de eigenschappen van uw Azure Cosmos-database account bijwerken. Eigenschappen die kunnen worden bijgewerkt zijn onder andere het volgende:

* Regio's toevoegen of verwijderen
* Standaard consistentie beleid wijzigen
* IP-bereik filter wijzigen
* Virtual Network configuraties wijzigen
* Multi-Master inschakelen

> [!NOTE]
> U kunt niet tegelijkertijd regio's toevoegen of verwijderen `locations` en andere eigenschappen wijzigen voor een Azure Cosmos-account. Het wijzigen van regio's moet worden uitgevoerd als een afzonderlijke bewerking dan een andere wijziging in de account bron.
> [!NOTE]
> Met deze opdracht kunt u regio's toevoegen en verwijderen, maar kunt u geen failover-prioriteiten wijzigen of een hand matige failover starten. Zie [failover-prioriteit wijzigen](#modify-failover-priority) en [hand matige failover activeren](#trigger-manual-failover).

```azurepowershell-interactive
# Create an account with 2 regions
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"
$accountName = "mycosmosaccount" # must be lower case and < 31 characters

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false }
)
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false },
    @{ "locationName"="South Central US"; "failoverPriority"=2, "isZoneRedundant"=false }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```
### <a id="multi-master"></a>Meerdere schrijf regio's inschakelen voor een Azure Cosmos-account

```azurepowershell-interactive
# Update an Azure Cosmos account from single to multi-master
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"

$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a>Een Azure Cosmos-account verwijderen

Met deze opdracht kunt u een bestaand Azure Cosmos-account verwijderen.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a>Tags van een Azure Cosmos-account bijwerken

In het volgende voor beeld wordt beschreven hoe u [Azure-resource Tags][azure-resource-tags] instelt voor een Azure Cosmos-account.

> [!NOTE]
> Deze opdracht kan worden gecombineerd met de opdrachten Create of update door de vlag `-Tags` toe te voegen aan de bijbehorende para meter.

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a id="list-keys"></a>Lijst met account sleutels

Wanneer u een Azure Cosmos DB account maakt, genereert de service twee hoofd toegangs sleutels die kunnen worden gebruikt voor verificatie wanneer het Azure Cosmos DB-account wordt geopend. Met behulp van twee toegangs sleutels kunt Azure Cosmos DB de sleutels opnieuw genereren zonder onderbreking van uw Azure Cosmos DB-account. Alleen-lezen sleutels voor verificatie van alleen-lezen-bewerkingen zijn ook beschikbaar. Er zijn twee sleutels voor lezen/schrijven (primair en secundair) en twee alleen-lezen sleutels (primair en secundair).

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Write-Host "PrimaryKey =" $keys.primaryMasterKey
Write-Host "SecondaryKey =" $keys.secondaryMasterKey
```

### <a id="list-connection-strings"></a>Verbindings reeksen weer geven

Voor MongoDB-accounts kan de connection string voor het verbinden van uw MongoDB-app met het database account worden opgehaald met behulp van de volgende opdracht.

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="regenerate-keys"></a>Account sleutels opnieuw genereren

Toegangs sleutels voor een Azure Cosmos-account moeten periodiek opnieuw worden gegenereerd om de verbindingen veiliger te houden. Een primaire en secundaire toegangs sleutel worden toegewezen aan het account. Hierdoor kunnen clients toegang behouden terwijl de andere opnieuw wordt gegenereerd. Er zijn vier soorten sleutels voor een Azure Cosmos-account (primair, secundair, PrimaryReadonly en SecondaryReadonly)

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a id="enable-automatic-failover"></a>Automatische failover inschakelen

Hiermee kan een Cosmos-account een failover naar de secundaire regio maken, omdat de primaire regio niet meer beschikbaar is.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"

$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="modify-failover-priority"></a>Prioriteit van failover wijzigen

Voor accounts die zijn geconfigureerd met automatische failover, kunt u de volg orde wijzigen waarin Cosmos secundaire replica's naar primair promo veren, anders is de primaire replica niet meer beschikbaar.

In het onderstaande voor beeld wordt ervan uitgegaan dat de huidige failover-prioriteit `West US 2 = 0`, `East US 2 = 1``South Central US = 2`is.

> [!CAUTION]
> Als `locationName` voor `failoverPriority=0` wordt gewijzigd, wordt een hand matige failover geactiveerd voor een Azure Cosmos-account. Bij eventuele andere wijzigingen van de prioriteit wordt geen failover geactiveerd.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0, "East US 2" = 1, "South Central US" = 2

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="South Central US"; "failoverPriority"=1 },
    @{ "locationName"="East US 2"; "failoverPriority"=2 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

### <a id="trigger-manual-failover"></a>Hand matige failover activeren

Voor accounts die zijn geconfigureerd met hand matige failover, kunt u een secundaire replica failover en promo veren naar de primaire replicatie door te wijzigen in `failoverPriority=0`. Deze bewerking kan worden gebruikt om een nood herstel analyse te initiëren om planning voor nood herstel te testen.

In het onderstaande voor beeld wordt ervan uitgegaan dat het account een huidige failover-prioriteit van `West US 2 = 0` heeft en `East US 2 = 1` en de regio's spiegelt.

> [!CAUTION]
> Als `locationName` voor `failoverPriority=0` wordt gewijzigd, wordt een hand matige failover geactiveerd voor een Azure Cosmos-account. Bij elke andere wijziging van de prioriteit wordt een failover niet geactiveerd.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0, "East US 2" = 1, "South Central US" = 2

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="South Central US"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 },
    @{ "locationName"="West US 2"; "failoverPriority"=2 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Azure Cosmos-data base

De volgende secties laten zien hoe u de Azure Cosmos-Data Base kunt beheren, met inbegrip van:

* [Een Azure Cosmos-data base maken](#create-db)
* [Een Azure Cosmos-data base maken met een gedeelde door Voer](#create-db-ru)
* [De door Voer van een Azure Cosmos-data base ophalen](#get-db-ru)
* [Alle Azure Cosmos-data bases in een account weer geven](#list-db)
* [Eén Azure Cosmos-data base ophalen](#get-db)
* [Een Azure Cosmos-data base verwijderen](#delete-db)

### <a id="create-db"></a>Een Azure Cosmos-data base maken

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="create-db-ru"></a>Een Azure Cosmos-data base maken met een gedeelde door Voer

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="get-db-ru"></a>De door Voer van een Azure Cosmos-data base ophalen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$databaseThroughputResourceName = $accountName + "/sql/" + $databaseName + "/throughput"

Get-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName  | Select-Object Properties
```

### <a id="list-db"></a>Alle Azure Cosmos-data bases in een account ophalen

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Eén Azure Cosmos-data base ophalen

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-db"></a>Een Azure Cosmos-data base verwijderen

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Azure Cosmos-container

In de volgende secties wordt beschreven hoe u de Azure Cosmos-container beheert, met inbegrip van:

* [Een Azure Cosmos-container maken](#create-container)
* [Een Azure Cosmos-container maken met een grote partitie sleutel](#create-container-big-pk)
* [De door Voer van een Azure Cosmos-container ophalen](#get-container-ru)
* [Een Azure Cosmos-container maken met gedeelde door Voer](#create-container-ru)
* [Een Azure Cosmos-container maken met aangepaste indexering](#create-container-custom-index)
* [Een Azure Cosmos-container maken waarvoor indexeren is uitgeschakeld](#create-container-no-index)
* [Een Azure Cosmos-container maken met een unieke sleutel en TTL](#create-container-unique-key-ttl)
* [Een Azure Cosmos-container maken met conflict oplossing](#create-container-lww)
* [Alle Azure Cosmos-containers in een Data Base weer geven](#list-containers)
* [Eén Azure Cosmos-container in een Data Base ophalen](#get-container)
* [Een Azure Cosmos-container verwijderen](#delete-container)

### <a id="create-container"></a>Een Azure Cosmos-container maken

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-big-pk"></a>Een Azure Cosmos-container maken met een grote partitie sleutel grootte

```azurepowershell-interactive
# Create an Azure Cosmos container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash";
            "version" = 2
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="get-container-ru"></a>De door Voer van een Azure Cosmos-container ophalen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$containerThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers/settings"
$containerThroughputResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName + "/throughput"

Get-AzResource -ResourceType $containerThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerThroughputResourceName  | Select-Object Properties
```

### <a id="create-container-ru"></a>Een Azure Cosmos-container maken met gedeelde door Voer

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a id="create-container-custom-index"></a>Een Azure Cosmos-container maken met aangepast index beleid

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-no-index"></a>Een Azure Cosmos-container maken waarvoor indexeren is uitgeschakeld

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-unique-key-ttl"></a>Een Azure Cosmos-container maken met uniek sleutel beleid en een TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 86400;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>Een Azure Cosmos-container maken met conflict oplossing

Als u een beleid voor conflict oplossing wilt maken voor het gebruik van een opgeslagen procedure, stelt u `"mode"="custom"` in en stelt u het pad voor de oplossing in als de naam van de opgeslagen procedure, `"conflictResolutionPath"="myResolverStoredProcedure"`. Als u alle conflicten naar de ConflictsFeed wilt schrijven en afzonderlijk wilt afhandelen, stelt u `"mode"="custom"` en `"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins";
            "conflictResolutionPath"="/myResolutionPath"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="list-containers"></a>Alle Azure Cosmos-containers in een Data Base weer geven

```azurepowershell-interactive
# List all Azure Cosmos containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="get-container"></a>Eén Azure Cosmos-container in een Data Base ophalen

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-container"></a>Een Azure Cosmos-container verwijderen

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>Volgende stappen

* [Alle Power shell-voor beelden](powershell-samples.md)
* [Azure Cosmos-account beheren](how-to-manage-database-account.md)
* [Een Azure Cosmos-container maken](how-to-create-container.md)
* [Time-to-Live configureren in Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
