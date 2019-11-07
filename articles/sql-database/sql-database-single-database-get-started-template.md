---
title: 'Azure Resource Manager: een enkele data base maken-Azure SQL Database '
description: Maak een enkele data base in Azure SQL Database met behulp van de Azure Resource Manager sjabloon.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: 3c46dbe5cac4407eda8b4198613eda1c1742ed35
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687202"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Snelstartgids: een enkele data base in Azure SQL Database maken met behulp van de Azure Resource Manager sjabloon

Het maken van [één database](sql-database-single-database.md) is de snelste een eenvoudigste implementatieoptie om een database te maken in Azure SQL Database. In deze Quick start ziet u hoe u een enkele data base maakt met behulp van de sjabloon Azure Resource Manager. Zie [Azure Resource Manager-documentatie](/azure/azure-resource-manager/)voor meer informatie.

Als u nog geen abonnement op Azure hebt, [maak dan een gratis account](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Een individuele database maken

Een individuele database bevat een gedefinieerde set reken-, geheugen-, IO- en opslagresources die gebruikmaakt van één van twee [aankoopmodellen](sql-database-purchase-models.md). Wanneer u een individuele database maakt, definieert u ook een [SQL Database-server](sql-database-servers.md) om die te beheren en in een [Azure-resourcegroep](../azure-resource-manager/resource-group-overview.md) in een opgegeven regio te plaatsen.

Het volgende JSON-bestand is de sjabloon die wordt gebruikt in dit artikel. De sjabloon wordt opgeslagen in [github](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/SQLServerAndDatabase/azuredeploy.json). Meer voor beelden van Azure SQL database-sjablonen vindt u in [Azure Quick](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)start-sjablonen.

[!code-json[create-azure-sql-database-server-and-database](~/resourcemanager-templates/SQLServerAndDatabase/azuredeploy.json)]

1. Selecteer **Probeer het** uit het volgende Power shell-code blok om Azure Cloud shell te openen.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
    $adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
    $adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

    $resourceGroupName = "${projectName}rg"


    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" -projectName $projectName -adminUser $adminUser -adminPassword $adminPassword

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

1. Selecteer **kopiëren** om het Power shell-script naar het klem bord te kopiëren.
1. Klik met de rechter muisknop op het deel venster shell en selecteer vervolgens **Plakken**.

    Het duurt enkele minuten om de database server en de data base te maken.

## <a name="query-the-database"></a>Een query uitvoeren op de database

Zie [query uitvoeren op de data](./sql-database-single-database-get-started.md#query-the-database)Base om de data base te doorzoeken.

## <a name="clean-up-resources"></a>Resources opschonen

Behoud deze resourcegroep, databaseserver en individuele database als u naar de [Volgende stappen](#next-steps) wilt gaan. De volgende stappen laten zien hoe u verbinding maakt met en een query uitvoert op uw database met behulp van verschillende methoden.

De resource groep verwijderen door Azure Power shell:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Volgende stappen

- Maak een firewallregel op serverniveau om via on-premises of externe hulpprogramma's verbinding met de individuele database te maken. Zie [Een serverfirewallregel maken](sql-database-server-level-firewall-rule.md) voor meer informatie.
- Nadat u een serverfirewallregel hebt gemaakt, kunt u met verschillende hulpprogramma's en programmeertalen [verbinding maken met uw database en query's uitvoeren](sql-database-connect-query.md) op uw database.
  - [Verbinding maken en query's uitvoeren met behulp van SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Verbinding maken en query's uitvoeren met behulp van Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Zie [Azure cli](sql-database-cli-samples.md)-voor beelden voor het maken van één data base met behulp van Azure cli.
- Zie Azure PowerShell-voor [beelden](sql-database-powershell-samples.md)om een enkele data base te maken met behulp van Azure PowerShell.
