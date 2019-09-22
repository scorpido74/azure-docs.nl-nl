---
title: Informatie over het beheren van databaseaccounts in Azure Cosmos DB
description: Informatie over het beheren van databaseaccounts in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 95e3ee31ea6a700253e372d183944c17c85e327c
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178318"
---
# <a name="manage-an-azure-cosmos-account"></a>Een Azure Cosmos-account beheren

In dit artikel wordt beschreven hoe u verschillende taken beheert in een Azure Cosmos-account met behulp van de Azure Portal-, Azure PowerShell-, Azure CLI-en Azure Resource Manager-sjablonen.

## <a name="create-an-account"></a>Een account maken

### <a id="create-database-account-via-portal"></a>Azure-portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```azurecli-interactive
# Create an account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case and < 31 characters

az cosmosdb create \
   --name $accountName \
   --resource-group $resourceGroupName \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=WestUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=EastUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations true
```

### <a id="create-database-account-via-ps"></a>Azure PowerShell
```azurepowershell-interactive
# Create an Azure Cosmos account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case and < 31 characters

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
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
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="create-database-account-via-arm-template"></a>Azure Resource Manager sjabloon

Met deze Azure Resource Manager sjabloon wordt een Azure Cosmos-account gemaakt voor elke ondersteunde API die is geconfigureerd met twee regio's en opties om consistentie niveau, automatische failover en multi-master te selecteren. Als u deze sjabloon wilt implementeren, klikt u op implementeren in azure op de pagina README, maakt u een [Azure Cosmos-account](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-create-multi-region-account)

## <a name="addremove-regions-from-your-database-account"></a>Regio's toevoegen aan of verwijderen uit uw databaseaccount

### <a id="add-remove-regions-via-portal"></a>Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 

1. Ga naar uw Azure Cosmos-account en open het **wereld wijde menu gegevens repliceren** .

1. Als u regio's wilt toevoegen, selecteert u de zeshoeken op de **+** kaart met het label dat overeenkomt met de gewenste regio (s). Als u een regio wilt toevoegen, selecteert u de optie **+ regio toevoegen** en kiest u een regio in de vervolg keuzelijst.

1. Als u regio's wilt verwijderen, wist u een of meer regio's van de kaart door de blauwe zeshoeken met vinkjes te selecteren. Of selecteer het prullenbakpictogram (🗑) rechts van de regio.

1. Selecteer **OK** om uw wijzigingen op te slaan.

   ![Menu Regio's toevoegen of verwijderen](./media/how-to-manage-database-account/add-region.png)

In een schrijf modus met één regio kunt u de schrijf regio niet verwijderen. U moet een failover naar een andere regio uitvoeren voordat u de huidige schrijf regio kunt verwijderen.

In een schrijf modus met meerdere regio's kunt u een regio toevoegen of verwijderen als u ten minste één regio hebt.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case and <31 characters

# Create an account with 1 region
az cosmosdb create --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False --locations regionName=EastUS failoverPriority=1 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False
```

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Create an account with 1 region
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case and <31 characters

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( 
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East Us"; "failoverPriority"=1 } 
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="configure-multiple-write-regions"></a>Meerdere schrijf regio's configureren

### <a id="configure-multiple-write-regions-portal"></a>Azure-portal

Open het tabblad **gegevens globaal repliceren** en selecteer **inschakelen** om schrijf bewerkingen in meerdere regio's in te scha kelen. Wanneer u meerdere regio's schrijft, worden de regio's die u momenteel op het account hebt gelezen, gelezen en geschreven. U kunt ook de schrijf bewerkingen voor meerdere regio's uitschakelen wanneer dat nodig is.

![Een Azure Cosmos-account configureert een scherm opname van meerdere masters](./media/how-to-manage-database-account/single-to-multi-master.png)

Neem contact op met de askcosmosdb@microsoft.com alias voor aanvullende vragen over deze functie. 

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'
az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-multiple-write-locations true
```

Als u een Azure Cosmos-account wilt maken zonder schrijf bewerkingen voor meerdere regio's, `--enable-multiple-write-locations` kunt u de para meter instellen op false.

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Update an Azure Cosmos account from single to multi-master

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="configure-multiple-write-regions-arm"></a>Resource Manager-sjabloon

Een account kan worden gemigreerd van een Single-Master naar meerdere masters door de Resource Manager-sjabloon te implementeren die wordt gebruikt om `enableMultipleWriteLocations: true`het account en de instelling te maken. De volgende Azure Resource Manager sjabloon is een Maxi maal bare sjabloon waarmee een Azure Cosmos-account voor SQL-API met twee regio's en meerdere schrijf locaties kan worden geïmplementeerd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Automatische failover inschakelen voor uw Azure Cosmos-account

Met de optie voor automatische failover kunnen Azure Cosmos DB een failover naar de regio met de hoogste failover-prioriteit zonder actie van de gebruiker, omdat een regio niet meer beschikbaar is. Als automatische failover is ingeschakeld, kan de prioriteit van de regio worden gewijzigd. Het account moet twee of meer regio's hebben om automatische failover mogelijk te maken.

### <a id="enable-automatic-failover-via-portal"></a>Azure-portal

1. Open in uw Azure Cosmos-account het deel venster **gegevens globaal repliceren** .

2. Selecteer bovenin het deelvenster de knop **Automatische failover**.

   ![Menu Gegevens globaal repliceren](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Controleer in het deelvenster **Automatische failover** of **Automatische failover inschakelen** is ingesteld op **AAN**. 

4. Selecteer **Opslaan**.

   ![Portalmenu Automatische failover](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```azurecli-interactive
# Enable automatic failover on an existing account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-automatic-failover true
```

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Failoverprioriteiten instellen voor uw Azure Cosmos-account

Nadat een Cosmos-account is geconfigureerd voor automatische failover, kunnen de failover-prioriteit voor regio's worden gewijzigd.

> [!IMPORTANT]
> U kunt de schrijf regio (failover-prioriteit nul) niet wijzigen wanneer het account is geconfigureerd voor automatische failover. Als u de schrijf regio wilt wijzigen, moet u automatische failover uitschakelen en een hand matige failover uitvoeren.

### <a id="set-failover-priorities-via-portal"></a>Azure-portal

1. Open in uw Azure Cosmos-account het deel venster **gegevens globaal repliceren** .

2. Selecteer bovenin het deelvenster de knop **Automatische failover**.

   ![Menu Gegevens globaal repliceren](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Controleer in het deelvenster **Automatische failover** of **Automatische failover inschakelen** is ingesteld op **AAN**.

4. Als u de failoverprioriteit wilt wijzigen, sleept u de leesregio's met behulp van de drie puntjes aan de linkerkant van de rij, die worden weergegeven wanneer u de muisaanwijzer erboven houdt.

5. Selecteer **Opslaan**.

   ![Portalmenu Automatische failover](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```azurecli-interactive
# Assume region order is initially eastus=0 westus=1 southeastasia=2 on account creation
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb failover-priority-change --name $accountName --resource-group $resourceGroupName --failover-policies eastus=0 southeastasia=1 westus=2
```

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1, Southeast Asia = 2
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$failoverPolicies = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="Southeast Asia"; "failoverPriority"=1 },
    @{ "locationName"="East US"; "failoverPriority"=2 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a id="manual-failover"></a>Hand matige failover uitvoeren op een Azure Cosmos-account

> [!IMPORTANT]
> Het Azure Cosmos-account moet worden geconfigureerd voor een hand matige failover zodat deze bewerking kan worden voltooid.

Het proces voor het uitvoeren van een hand matige failover omvat het wijzigen van de schrijf regio van het account (failover priority = 0) naar een andere regio die is geconfigureerd voor het account.

> [!NOTE]
> U kunt niet hand matig een failover uitvoeren voor Multi-Master accounts. Voor toepassingen die gebruikmaken van de Azure Cosmos SDK detecteert de SDK wanneer een regio niet meer beschikbaar is en wordt automatisch omgeleid naar de dichtstbijzijnde regio als u gebruikmaakt van de multi-multihoming-API in de SDK.

### <a id="enable-manual-failover-via-portal"></a>Azure-portal

1. Ga naar uw Azure Cosmos-account en open het **wereld wijde menu gegevens repliceren** .

2. Selecteer bovenaan het menu de optie **Handmatige failover**.

   ![Menu Gegevens globaal repliceren](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Selecteer in het menu **Handmatige failover** de nieuwe schrijfregio. Schakel het selectievakje in om aan te geven dat u begrijpt dat met deze optie de schrijfregio wordt gewijzigd.

4. Selecteer **OK** om de failover te activeren.

   ![Portalmenu Handmatige failover](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```azurecli-interactive
# Assume account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False --locations regionName=eastus failoverPriority=1 isZoneRedundant=False
```

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

$account.Properties.locations=$locations;
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen voor meer informatie en voor beelden over het beheren van het Azure Cosmos-account, de data base en containers:

* [Azure Cosmos DB beheren met Azure PowerShell](manage-with-powershell.md)
* [Azure Cosmos DB beheren met behulp van Azure CLI](manage-with-cli.md)
