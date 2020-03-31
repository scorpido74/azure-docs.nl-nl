---
title: Het bewaken van uw Azure Kubernetes Service-cluster stoppen | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u de bewaking van uw Azure AKS-cluster met Azure Monitor voor containers stopzetten.
ms.topic: conceptual
ms.date: 08/19/2019
ms.openlocfilehash: 618a4d7e10212dd2b042724b1ea11c97920dad57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275254"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Stoppen met controleren van Azure Kubernetes Service (AKS) met Azure Monitor voor containers

Nadat u de bewaking van uw AKS-cluster hebt ingeschakeld, u stoppen met het bewaken van het cluster als u besluit dat u het niet langer wilt controleren. In dit artikel ziet u hoe u dit uitvoeren met de Azure CLI of met de meegeleverde Azure Resource Manager-sjablonen.  


## <a name="azure-cli"></a>Azure-CLI

Gebruik de opdracht [az aks disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) om Azure Monitor uit te schakelen voor containers. De opdracht verwijdert de agent uit de clusterknooppunten, hiermee wordt de oplossing of de gegevens die al zijn verzameld en opgeslagen in uw Azure Monitor-bron niet verwijderd.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Zie Bewaking inschakelen met Azure [CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli)als u de bewaking voor uw cluster opnieuw wilt inschakelen.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

Provided are two Azure Resource Manager template to support removing the solution resources consistent and repeatedly in your resource group. Een daarvan is een JSON-sjabloon waarin de configuratie wordt opgegeven om de bewaking te stoppen en de andere parameterwaarden die u configureert om de AKS-clusterbron-id en de brongroep op te geven waarin het cluster is geïmplementeerd.

Zie als u niet bekend bent met het concept van het implementeren van resources met behulp van een sjabloon:
* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Resources implementeren met Resource Manager-sjablonen en de Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>De sjabloon moet worden geïmplementeerd in dezelfde resourcegroep van het cluster. Als u andere eigenschappen of invoegtoepassingen weglaat wanneer u deze sjabloon gebruikt, kan dit ertoe leiden dat deze als volgt wordt verwijderd uit het cluster. Schakel bijvoorbeeld *RBAC* in voor RBAC-beleid dat in uw cluster is geïmplementeerd of *aksResourceTagValues* als tags zijn opgegeven voor het AKS-cluster.  
>

Als u ervoor kiest de Azure CLI te gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.27 of hoger uitvoeren. Voer uit om `az --version`uw versie te identificeren. Zie [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)installeren als u de Azure CLI moet installeren of upgraden.

### <a name="create-template"></a>Sjabloon maken

1. Kopieer en plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       },
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
        }
      }
     },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Sla dit bestand op als **OptOutTemplate.json** in een lokale map.

3. Plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterRegion>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Bewerk de waarden voor **aksResourceId** en **aksResourceLocation** met behulp van de waarden van het AKS-cluster, die u vinden op de pagina **Eigenschappen** voor het geselecteerde cluster.

    ![Pagina Containereigenschappen](media/container-insights-optout/container-properties-page.png)

    Kopieer ook de **werkruimtebron-id**op de pagina **Eigenschappen** . Deze waarde is vereist als u besluit dat u de werkruimte Log Analytics later wilt verwijderen. Het verwijderen van de werkruimte Log Analytics wordt niet uitgevoerd als onderdeel van dit proces.

    Bewerk de waarden voor **aksResourceTagValues** die overeenkomen met de bestaande tagwaarden die zijn opgegeven voor het AKS-cluster.

5. Sla dit bestand op als **OptOutParam.json** in een lokale map.

6. U kunt deze sjabloon nu implementeren.

### <a name="remove-the-solution-using-azure-cli"></a>De oplossing verwijderen met Azure CLI

Voer de volgende opdracht uit met Azure CLI op Linux om de oplossing te verwijderen en de configuratie op uw AKS-cluster op te schonen.

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Het kan enkele minuten duren voordat de configuratiewijziging is voltooid. Wanneer het is voltooid, wordt een bericht dat vergelijkbaar is met het volgende dat het resultaat bevat, geretourneerd:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>De oplossing verwijderen met PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Voer de volgende PowerShell-opdrachten uit in de map met de sjabloon om de oplossing te verwijderen en de configuratie uit uw AKS-cluster op te schonen.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Het kan enkele minuten duren voordat de configuratiewijziging is voltooid. Wanneer het is voltooid, wordt een bericht dat vergelijkbaar is met het volgende dat het resultaat bevat, geretourneerd:

```output
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>Volgende stappen

Als de werkruimte alleen is gemaakt ter ondersteuning van het bewaken van het cluster en het is niet langer nodig, moet u het handmatig verwijderen. Zie [Een Azure Log Analytics-werkruimte verwijderen met de Azure-portal](../../log-analytics/log-analytics-manage-del-workspace.md)als u niet bekend bent met het verwijderen van een werkruimte. Vergeet niet de **Workspace Resource ID** die eerder in stap 4 is gekopieerd, dat heb je nodig.
