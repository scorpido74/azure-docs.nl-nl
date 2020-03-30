---
title: Een Azure Data Explorer-cluster en -database maken met behulp van een Azure Resource Manager-sjabloon
description: Meer informatie over het maken van een Azure Data Explorer-cluster en -database met behulp van een Azure Resource Manager-sjabloon
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 56639d8a29ad8eac465845c8d354d04b31ba6093
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911967"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Een Azure Data Explorer-cluster en -database maken met behulp van een Azure Resource Manager-sjabloon

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Azure Resource Manager-sjabloon](create-cluster-database-resource-manager.md)

Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. Als u Azure Data Explorer wilt gebruiken, maakt u eerst een cluster. Daarna maakt u een of meer databases in het cluster. De volgende stap is het opnemen (laden) van gegevens in een database, zodat u er query's op kunt uitvoeren. 

In dit artikel maakt u een Azure Data Explorer-cluster en -database met behulp van een [Azure Resource Manager-sjabloon](../azure-resource-manager/management/overview.md). In het artikel wordt uitgelegd hoe u definiëren welke resources worden geïmplementeerd en hoe u parameters definieert die zijn opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen. Zie [Het ontwerpen van Azure Resource Manager-sjablonen](/azure/azure-resource-manager/resource-group-authoring-templates)voor informatie over het maken van sjablonen . Zie [Microsoft.Kusto-brontypen](/azure/templates/microsoft.kusto/allversions)voor de syntaxis en eigenschappen van JSON in een sjabloon .

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Azure Resource Manager-sjabloon voor het maken van cluster- en databasegegevens

In dit artikel gebruikt u een [bestaande snelstartsjabloon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
          }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources."
        }
      }
  },
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

Zie [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/)voor meer sjabloonvoorbeelden .

## <a name="deploy-the-template-and-verify-template-deployment"></a>De sjabloon implementeren en de implementatie van sjablonen verifiëren

U de sjabloon Azure Resource Manager implementeren [met behulp van de Azure-portal](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) of met [powershell.](#use-powershell-to-deploy-the-template-and-verify-template-deployment)

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>De Azure-portal gebruiken om de sjabloon te implementeren en de implementatie van sjablonen te verifiëren

1. Als u een cluster en database wilt maken, gebruikt u de volgende knop om de implementatie te starten. Klik met de rechtermuisknop en selecteer **In nieuw venster openen**, zodat u de rest van de stappen in dit artikel kunt volgen.

    [![Implementeren naar Azure](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    Als u op de knop **Implementeren in Azure** klikt, wordt u naar de Azure-portal geleid om een implementatieformulier in te vullen.

    ![Implementeren in Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    U [de sjabloon in de Azure-portal bewerken en implementeren](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) met behulp van het formulier.

1. Volledige **secties BASICS** en **INSTELLINGEN.** Selecteer unieke cluster- en databasenamen.
Het maken van een Azure Data Explorer-cluster en -database duurt enkele minuten.

1. Als u de implementatie wilt verifiëren, opent u de brongroep in de [Azure-portal](https://portal.azure.com) om uw nieuwe cluster en database te vinden. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>PowerShell gebruiken om de sjabloon te implementeren en de implementatie van sjablonen te verifiëren

#### <a name="deploy-the-template-using-powershell"></a>De sjabloon implementeren met powershell

1. Selecteer **Probeer het** in het volgende codeblok en volg de instructies om u aan te melden bij de Azure Cloud-shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add("clusters_kustocluster_name", $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Selecteer **Kopiëren** om het PowerShell-script te kopiëren.
1. Klik met de rechtermuisknop op de shell console en selecteer **Plakken**.
Het maken van een Azure Data Explorer-cluster en -database duurt enkele minuten.

#### <a name="verify-the-deployment-using-powershell"></a>De implementatie verifiëren met PowerShell

Als u de implementatie wilt verifiëren, gebruikt u het volgende Azure PowerShell-script.  Als de Cloud Shell nog steeds open is, hoeft u de eerste regel (Read-Host) niet te kopiëren/uitvoeren. Lees [Az.Kusto](/powershell/module/az.kusto/?view=azps-2.7.0)voor meer informatie over het beheer van Azure Data Explorer-bronnen in PowerShell. 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](../../includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

[Gegevens opnemen in Azure Data Explorer-cluster en -database](ingest-data-overview.md)
