---
title: Stoppen met het bewaken van uw Azure Red Hat open Shift-cluster | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de bewaking van uw Azure Red Hat open Shift-cluster kunt stoppen met Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: e726d2d8254598869f1c6305421c674c870e3d31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404291"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>Het bewaken van uw Azure Red Hat open Shift-cluster stoppen met Azure Monitor voor containers

Nadat u de bewaking van uw Azure Red Hat open Shift-cluster hebt ingeschakeld, kunt u stoppen met het bewaken van het cluster als u besluit dat u het niet meer wilt bewaken. In dit artikel wordt beschreven hoe u dit kunt doen met behulp van de meegeleverde Azure Resource Manager sjablonen.  

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

Gegeven worden twee Azure Resource Manager-sjabloon voor de ondersteuning van de resources van de oplossing consistent en herhaaldelijk in de resourcegroep te verwijderen. Een JSON-sjabloon geeft u de configuratie voor het stoppen van de controle en de andere bevat parameter waarden die u configureert om de open Shift-cluster resource-ID en Azure-regio op te geven waarin het cluster is geïmplementeerd. 

Als u niet bekend met het concept bent van het implementeren van resources met behulp van een sjabloon, Zie:
* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Als u ervoor de Azure CLI gebruiken kiest, moet u eerst installeren en de CLI lokaal gebruikt. U moet de Azure CLI-versie 2.0.65 of hoger uitvoeren. Voor het identificeren van uw versie uitvoeren `az --version`. Als u wilt installeren of upgraden van de Azure CLI, Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-template"></a>Sjabloon maken

1. Kopieer en plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
            }
          }
        }
      ]
    }
    ```

2. Sla dit bestand als **OptOutTemplate.json** naar een lokale map.

3. Plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. Bewerk de waarden voor **aroResourceId** en **aroResourceLocation** met behulp van de waarden van het open Shift-cluster, dat u kunt vinden op de pagina **Eigenschappen** voor het geselecteerde cluster.

    ![De eigenschappenpagina van container](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Sla dit bestand als **OptOutParam.json** naar een lokale map.

6. U kunt deze sjabloon nu implementeren. 

### <a name="remove-the-solution-using-azure-cli"></a>Verwijderen van de oplossing met behulp van Azure CLI

Voer de volgende opdracht uit met Azure CLI in Linux om de oplossing te verwijderen en de configuratie op uw cluster op te schonen.

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Wijzigen van de configuratie kan een paar minuten duren. Wanneer deze voltooid, wordt een bericht dat lijkt op de volgende mogelijkheden van het resultaat geretourneerd:

```azurecli
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Verwijderen van de oplossing met behulp van PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Voer de volgende Power shell-opdrachten uit in de map met de sjabloon om de oplossing te verwijderen en de configuratie op te schonen van uw cluster.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Wijzigen van de configuratie kan een paar minuten duren. Wanneer deze voltooid, wordt een bericht dat lijkt op de volgende mogelijkheden van het resultaat geretourneerd:

```powershell
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>Volgende stappen

Als de werkruimte alleen ter ondersteuning van de bewaking van het cluster is gemaakt en deze niet meer nodig hebt, hebt u deze handmatig te verwijderen. Zie [een Azure log Analytics-werk ruimte verwijderen](../../log-analytics/log-analytics-manage-del-workspace.md)als u niet bekend bent met het verwijderen van een werk ruimte. 
