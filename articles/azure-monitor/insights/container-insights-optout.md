---
title: Hoe Stop bewaking Your Azure Kubernetes Service-cluster | Microsoft Docs
description: Dit artikel wordt beschreven hoe u kunt niet meer controleren van uw Azure-AKS-cluster met Azure Monitor voor containers.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 08/19/2019
ms.openlocfilehash: fe0155d6102dac12d5d4c01b78b1ddd45f9bee02
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382249"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Stoppen met het controleren van uw Azure Kubernetes Service (AKS) met Azure Monitor voor containers

Nadat u de bewaking van uw AKS-cluster hebt ingeschakeld, kunt u de bewaking van het cluster als u besluit dat u niet meer om te controleren, wilt stoppen. Dit artikel wordt beschreven hoe u dit doen met de Azure CLI of met de opgegeven Azure Resource Manager-sjablonen.  


## <a name="azure-cli"></a>Azure-CLI

Gebruik de opdracht [AZ AKS Disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) om Azure monitor voor containers uit te scha kelen. Met de opdracht wordt de agent uit de cluster knooppunten verwijderd, wordt de oplossing niet verwijderd of worden de gegevens die al zijn verzameld en opgeslagen in uw Azure Monitor-resource.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Zie [bewaking inschakelen met Azure cli](container-insights-enable-new-cluster.md#enable-using-azure-cli)om de bewaking van uw cluster weer in te scha kelen.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

Gegeven worden twee Azure Resource Manager-sjabloon voor de ondersteuning van de resources van de oplossing consistent en herhaaldelijk in de resourcegroep te verwijderen. Een JSON-sjabloon waarmee de configuratie wordt opgegeven voor het stoppen van de bewaking en de andere bevat parameter waarden die u configureert om de AKS-cluster resource-ID en de resource groep op te geven waarin het cluster is geïmplementeerd. 

Als u niet bekend met het concept bent van het implementeren van resources met behulp van een sjabloon, Zie:
* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

>[!NOTE]
>De sjabloon moet worden geïmplementeerd in dezelfde resource groep van het cluster. Als u andere eigenschappen of invoeg toepassingen weglaat wanneer u deze sjabloon gebruikt, kan dit leiden tot verwijdering van het cluster. Bijvoorbeeld *enableRBAC* voor RBAC-beleid dat is geïmplementeerd in uw cluster of *aksResourceTagValues* als er labels zijn opgegeven voor het AKS-cluster.  
>

Als u ervoor de Azure CLI gebruiken kiest, moet u eerst installeren en de CLI lokaal gebruikt. U moet worden uitgevoerd van Azure CLI versie 2.0.27 of hoger. Voer `az --version`uit om uw versie te identificeren. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u [de Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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
       }
       },
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
      }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]"
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

2. Sla dit bestand op als **OptOutTemplate. json** naar een lokale map.

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

4. Bewerk de waarden voor **aksResourceId** en **aksResourceLocation** met behulp van de waarden van het AKS-cluster, dat u kunt vinden op de pagina **Eigenschappen** voor het geselecteerde cluster.

    ![De eigenschappenpagina van container](media/container-insights-optout/container-properties-page.png)

    Terwijl u zich op de pagina **Eigenschappen** bevindt, kopieert u ook de **resource-id van de werk ruimte**. Deze waarde is vereist als u besluit dat u wilt verwijderen van de Log Analytics-werkruimte later opnieuw. Verwijderen van de Log Analytics-werkruimte wordt niet uitgevoerd als onderdeel van dit proces. 

    Bewerk de waarden voor **aksResourceTagValues** zodat deze overeenkomen met de bestaande label waarden die zijn opgegeven voor het AKS-cluster.

5. Sla dit bestand op als **OptOutParam. json** naar een lokale map.

6. U kunt deze sjabloon nu implementeren. 

### <a name="remove-the-solution-using-azure-cli"></a>Verwijderen van de oplossing met behulp van Azure CLI

De volgende opdracht uit met Azure CLI op Linux te verwijderen van de oplossing en het opschonen van de configuratie van uw AKS-cluster worden uitgevoerd.

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

Voer de volgende PowerShell-opdrachten in de map met de sjabloon voor het verwijderen van de oplossing en het opschonen van de configuratie van uw AKS-cluster.    

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

Als de werkruimte alleen ter ondersteuning van de bewaking van het cluster is gemaakt en deze niet meer nodig hebt, hebt u deze handmatig te verwijderen. Als u niet bekend bent met het verwijderen van een werk ruimte, raadpleegt u [een Azure log Analytics-werk ruimte verwijderen met de Azure Portal](../../log-analytics/log-analytics-manage-del-workspace.md). Vergeet niet om de **resource-id van de werk ruimte** die u eerder in stap 4 hebt gekopieerd, te zien. 

