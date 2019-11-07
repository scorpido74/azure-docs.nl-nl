---
title: Een Azure Data Explorer-cluster en-data base maken met behulp van een Azure Resource Manager sjabloon
description: Meer informatie over het maken van een Azure Data Explorer-cluster en-data base met behulp van een Azure Resource Manager sjabloon
author: orspod
ms.author: orspodek
ms.reviewer: oflipman
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 49c55b580abdaea6c876a0fac4e7dd4e73d496af
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643833"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Een Azure Data Explorer-cluster en-data base maken met behulp van een Azure Resource Manager sjabloon

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM-sjabloon](create-cluster-database-resource-manager.md)

Azure Data Explorer is een snelle en zeer schaalbare service om gegevens in logboeken en telemetrie te verkennen. Als u Azure Data Explorer wilt gebruiken, maakt u eerst een cluster. Daarna maakt u een of meer databases in het cluster. De volgende stap is het opnemen (laden) van gegevens in een database, zodat u er query's op kunt uitvoeren. 

In dit artikel maakt u een Azure Data Explorer-cluster en-data base met behulp van een [Azure Resource Manager sjabloon](../azure-resource-manager/resource-group-overview.md). In het artikel wordt beschreven hoe u definieert welke resources worden geïmplementeerd en hoe u para meters definieert die worden opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen. Zie [Azure Resource Manager sjablonen ontwerpen](/azure/azure-resource-manager/resource-group-authoring-templates)voor meer informatie over het maken van sjablonen. Zie [resource typen micro soft. Kusto](/azure/templates/microsoft.kusto/allversions)voor de JSON-syntaxis en-eigenschappen die in een sjabloon moeten worden gebruikt.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Azure Resource Manager sjabloon voor het maken van clusters en data bases

In dit artikel gebruikt u een [bestaande Quick](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json) start-sjabloon

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
          "apiVersion": "2019-05-15",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-05-15",
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

Zie [Azure Quick](https://azure.microsoft.com/resources/templates/)start-sjablonen voor meer voor beelden van sjablonen.

## <a name="deploy-the-template-and-verify-template-deployment"></a>De sjabloon implementeren en de implementatie van de sjabloon controleren

U kunt de Azure Resource Manager-sjabloon implementeren met [behulp van de Azure Portal](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) of [met behulp van Power shell](#use-powershell-to-deploy-the-template-and-verify-template-deployment).

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>De Azure Portal gebruiken om de sjabloon te implementeren en de sjabloon implementatie te verifiëren

1. Als u een cluster en Data Base wilt maken, gebruikt u de volgende knop om de implementatie te starten. Klik met de rechtermuisknop en selecteer **In nieuw venster openen**, zodat u de rest van de stappen in dit artikel kunt volgen.

    [![Implementeren in Azure](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    Als u op de knop **Implementeren in Azure** klikt, wordt u naar de Azure-portal geleid om een implementatieformulier in te vullen.

    ![Implementeren in Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    U kunt [de sjabloon in de Azure Portal bewerken en implementeren](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) met behulp van het formulier.

1. De secties **basis beginselen** en **instellingen** volt ooien. Selecteer unieke namen voor clusters en data bases.
Het duurt enkele minuten om een Azure Data Explorer-cluster en-Data Base te maken.

1. Als u de implementatie wilt controleren, opent u de resource groep in het [Azure Portal](https://portal.azure.com) om het nieuwe cluster en de data base te vinden. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>Power shell gebruiken om de sjabloon te implementeren en de sjabloon implementatie te controleren

#### <a name="deploy-the-template-using-powershell"></a>De sjabloon implementeren met behulp van Power shell

1. Selecteer **Probeer het** uit het volgende code blok en volg de instructies om u aan te melden bij de Azure Cloud shell.

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
1. Klik met de rechter muisknop op de shell-console en selecteer vervolgens **Plakken**.
Het duurt enkele minuten om een Azure Data Explorer-cluster en-Data Base te maken.

#### <a name="verify-the-deployment-using-powershell"></a>De implementatie controleren met Power shell

Gebruik het volgende Azure PowerShell script om de implementatie te controleren.  Als de Cloud Shell nog steeds is geopend, hoeft u de eerste regel niet te kopiëren/uit te voeren (Read-host). Lees [AZ. Kusto](/powershell/module/az.kusto/?view=azps-2.7.0)voor meer informatie over het beheren van Azure Data Explorer-resources in Power shell. 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Resources opschonen met behulp van de Azure Portal

Verwijder de resources in de Azure Portal door de stappen te volgen in [opschonen van resources](create-cluster-database-portal.md#clean-up-resources).

### <a name="clean-up-resources-using-powershell"></a>Resources opschonen met PowerShell

Als de Cloud Shell nog steeds is geopend, hoeft u de eerste regel niet te kopiëren/uit te voeren (Read-host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Volgende stappen

[Gegevens opnemen in azure Data Explorer cluster en data base](ingest-data-overview.md)
