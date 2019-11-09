---
title: Een Azure Resource Manager sjabloon gebruiken om een werk ruimte te maken
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van een Azure Resource Manager sjabloon voor het maken van een nieuwe Azure Machine Learning-werk ruimte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 11/04/2019
ms.custom: seoapril2019
ms.openlocfilehash: f20c7955856e2e848d331d857d2564649c837bb0
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847463"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Een Azure Resource Manager sjabloon gebruiken om een werk ruimte te maken voor Azure Machine Learning

In dit artikel leert u verschillende manieren om een Azure Machine Learning-werk ruimte te maken met behulp van Azure Resource Manager sjablonen. Met een resource manager-sjabloon kunt u eenvoudig resources maken als één gecoördineerde bewerking. Een sjabloon is een JSON-document waarmee de benodigde resources voor een implementatie worden gedefinieerd. Het kan ook implementatie parameters opgeven. Para meters worden gebruikt om invoer waarden op te geven wanneer u de sjabloon gebruikt.

Zie [een toepassing implementeren met Azure Resource Manager sjabloon](../../azure-resource-manager/resource-group-template-deploy.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Als u er nog geen hebt, probeer [dan de gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

* Als u een sjabloon van een CLI wilt gebruiken, moet u [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) of de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Resource Manager-sjabloon

De volgende Resource Manager-sjabloon kan worden gebruikt om een Azure Machine Learning-werk ruimte en gekoppelde Azure-resources te maken:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus",
      "allowedValues": [
        "eastus",
        "eastus2",
        "southcentralus",
        "southeastasia",
        "westcentralus",
        "westeurope",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
        "allowedValues": [
          "basic",
          "enterprise"
        ],
        "metadata": {
          "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
        }
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2015-05-01",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2017-10-01",
      "name": "[variables('containerRegistryName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "adminUserEnabled": true
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]",
        "[resourceId('Microsoft.ContainerRegistry/registries', variables('containerRegistryName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
        "tier": "[parameters('sku')]",
        "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
      }
    }
  ]
}
```

Met deze sjabloon worden de volgende Azure-Services gemaakt:

* Azure-resourcegroep
* Azure Storage-account
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning-werkruimte

De resource groep is de container waarin de services worden bewaard. De verschillende services zijn vereist voor de Azure Machine Learning-werk ruimte.

De voorbeeld sjabloon heeft twee para meters:

* De **locatie** waar de resource groep en services worden gemaakt.

    De sjabloon maakt gebruik van de locatie die u selecteert voor de meeste resources. De uitzonde ring is de Application Insights-service, die niet beschikbaar is op alle locaties die de andere services zijn. Als u een locatie selecteert waar deze niet beschikbaar is, wordt de service gemaakt op de locatie Zuid-Centraal vs.

* De **naam van de werk ruimte**, de beschrijvende naam van de werk ruimte Azure machine learning.

    De namen van de andere services worden wille keurig gegenereerd.

> [!TIP]
> Terwijl de sjabloon die aan dit document is gekoppeld, een nieuwe Azure Container Registry maakt, kunt u ook een nieuwe werk ruimte maken zonder container register te maken. Als het container register in de werk ruimte aanwezig is, wordt er een gemaakt wanneer u een bewerking uitvoert waarvoor een container register vereist is. Bijvoorbeeld training of implementatie van een model.
>
> U kunt ook verwijzen naar een bestaand container register of opslag account in de Azure Resource Manager-sjabloon in plaats van een nieuwe te maken.

Zie de volgende artikelen voor meer informatie over sjablonen:

* [Azure Resource Manager sjablonen ontwerpen](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Een toepassing implementeren met Azure Resource Manager sjablonen](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resource typen van micro soft. MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Volg de stappen in [resources implementeren vanuit aangepaste sjabloon](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Wanneer u het scherm __sjabloon bewerken__ aankomt, plakt u de sjabloon uit dit document.
1. Selecteer __Opslaan__ om de sjabloon te gebruiken. Geef de volgende informatie op en ga akkoord met de vermelde voor waarden:

   * Abonnement: Selecteer het Azure-abonnement dat u wilt gebruiken voor deze resources.
   * Resource groep: Selecteer of maak een resource groep om de services te bevatten.
   * Werkruimte naam: de naam die moet worden gebruikt voor de Azure Machine Learning werk ruimte die wordt gemaakt. De naam van de werk ruimte moet tussen de 3 en 33 tekens lang zijn. De naam mag alleen alfanumerieke tekens en '-' bevatten.
   * Locatie: Selecteer de locatie waar de resources worden gemaakt.

Zie [resources implementeren vanuit een aangepaste sjabloon](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)voor meer informatie.

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

In dit voor beeld wordt ervan uitgegaan dat u de sjabloon hebt opgeslagen in een bestand met de naam `azuredeploy.json` in de huidige map:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Zie [resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) en een [privé Resource Manager-sjabloon met SAS-token en Azure PowerShell implementeren](../../azure-resource-manager/resource-manager-powershell-sas-token.md)voor meer informatie.

## <a name="use-azure-cli"></a>Azure CLI gebruiken

In dit voor beeld wordt ervan uitgegaan dat u de sjabloon hebt opgeslagen in een bestand met de naam `azuredeploy.json` in de huidige map:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus
```

Zie [resources implementeren met Resource Manager-sjablonen en Azure cli](../../azure-resource-manager/resource-group-template-deploy-cli.md) en een [persoonlijke Resource Manager-sjabloon implementeren met SAS-token en Azure cli](../../azure-resource-manager/resource-manager-cli-sas-token.md)voor meer informatie.

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault toegangs beleid en Azure Resource Manager sjablonen

Wanneer u een Azure Resource Manager sjabloon gebruikt om de werk ruimte en de gekoppelde resources (inclusief Azure Key Vault) meerdere keren te maken. Gebruik bijvoorbeeld de sjabloon meerdere keren met dezelfde para meters als onderdeel van een continue integratie-en implementatie pijplijn.

De meeste bewerkingen voor het maken van resources via sjablonen zijn idempotent, maar Key Vault wist het toegangs beleid telkens wanneer de sjabloon wordt gebruikt. Als u het toegangs beleid wist, wordt de toegang tot de Key Vault verbroken voor een bestaande werk ruimte waarin deze wordt gebruikt. Bijvoorbeeld: stop/Create-functionaliteit van Azure Notebooks VM kan mislukken.  

Om dit probleem te voor komen, raden we u aan een van de volgende benaderingen te volgen:

* Implementeer de sjabloon niet meer dan één keer voor dezelfde para meters. Of verwijder de bestaande resources voordat u de sjabloon opnieuw maakt.
  
* Controleer de Key Vault toegangs beleid en gebruik vervolgens dit beleid om de eigenschap accessPolicies van de sjabloon in te stellen.
* Controleer of de Key Vault resource al bestaat. Als dit het geval is, moet u het niet opnieuw maken via de sjabloon. Voeg bijvoorbeeld een para meter toe waarmee u het maken van de Key Vault resource kunt uitschakelen als deze al bestaat.

## <a name="next-steps"></a>Volgende stappen

* [Resources implementeren met Resource Manager-sjablonen en Resource Manager-rest API](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Azure-resource groepen maken en implementeren via Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
