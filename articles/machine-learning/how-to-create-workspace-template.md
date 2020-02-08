---
title: Een werk ruimte met Azure Resource Manager sjabloon maken
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
ms.openlocfilehash: b3e4ff60ab290d25afb003f0753cf852cefffe1a
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069554"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Een Azure Resource Manager sjabloon gebruiken om een werk ruimte te maken voor Azure Machine Learning

In dit artikel leert u verschillende manieren om een Azure Machine Learning-werk ruimte te maken met behulp van Azure Resource Manager sjablonen. Met een resource manager-sjabloon kunt u eenvoudig resources maken als één gecoördineerde bewerking. Een sjabloon is een JSON-document waarmee de benodigde resources voor een implementatie worden gedefinieerd. Het kan ook implementatie parameters opgeven. Para meters worden gebruikt om invoer waarden op te geven wanneer u de sjabloon gebruikt.

Zie [een toepassing implementeren met Azure Resource Manager sjabloon](../azure-resource-manager/templates/deploy-powershell.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Als u er nog geen hebt, probeer [dan de gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

* Als u een sjabloon van een CLI wilt gebruiken, moet u [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) of de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Resource Manager-sjabloon

De volgende Resource Manager-sjabloon kan worden gebruikt om een Azure Machine Learning-werk ruimte en gekoppelde Azure-resources te maken:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Met deze sjabloon worden de volgende Azure-Services gemaakt:

* Azure-resource groep
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

    > [!NOTE]
    > De naam van de werk ruimte is niet hoofdletter gevoelig.

    De namen van de andere services worden wille keurig gegenereerd.

> [!TIP]
> Terwijl de sjabloon die aan dit document is gekoppeld, een nieuwe Azure Container Registry maakt, kunt u ook een nieuwe werk ruimte maken zonder container register te maken. Als het container register in de werk ruimte aanwezig is, wordt er een gemaakt wanneer u een bewerking uitvoert waarvoor een container register vereist is. Bijvoorbeeld training of implementatie van een model.
>
> U kunt ook verwijzen naar een bestaand container register of opslag account in de Azure Resource Manager-sjabloon in plaats van een nieuwe te maken.

Zie de volgende artikelen voor meer informatie over sjablonen:

* [Azure Resource Manager sjablonen ontwerpen](../azure-resource-manager/templates/template-syntax.md)
* [Een toepassing implementeren met Azure Resource Manager sjablonen](../azure-resource-manager/templates/deploy-powershell.md)
* [Resource typen van micro soft. MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Geavanceerde sjabloon

In de volgende voorbeeld sjabloon ziet u hoe u een werk ruimte met drie instellingen maakt:

* Hoog vertrouwelijkheids instellingen voor de werk ruimte inschakelen
* Versleuteling inschakelen voor de werk ruimte
* Maakt gebruik van een bestaande Azure Key Vault

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
        "description": "Specifies the sku, also referred to as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "hbi_workspace":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with the customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault Resource Manager ID."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault key uri."
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
      "apiVersion": "2020-01-01",
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
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbi_workspace": "[parameters('hbi_workspace')]"
      }
    }
  ]
}
```

U kunt de Azure CLI gebruiken om de ID van de Key Vault op te halen en de sleutel-URI die nodig is voor deze sjabloon. De volgende opdracht is een voor beeld van het gebruik van de Azure CLI om de Key Vault Resource-ID en-URI op te halen:

```azurecli-interactive
az keyvault show --name mykeyvault --resource-group myresourcegroup --query "[id, properties.vaultUri]"
```

Met deze opdracht wordt een waarde geretourneerd die overeenkomt met de volgende tekst. De eerste waarde is de ID en de tweede is de URI:

```text
[
  "/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault",
  "https://mykeyvault.vault.azure.net/"
]
```

## <a name="use-the-azure-portal"></a>Azure Portal gebruiken

1. Volg de stappen in [resources implementeren vanuit aangepaste sjabloon](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Wanneer u het scherm __sjabloon bewerken__ aankomt, plakt u de sjabloon uit dit document.
1. Selecteer __Opslaan__ om de sjabloon te gebruiken. Geef de volgende informatie op en ga akkoord met de vermelde voor waarden:

   * Abonnement: Selecteer het Azure-abonnement dat u wilt gebruiken voor deze resources.
   * Resource groep: Selecteer of maak een resource groep om de services te bevatten.
   * Werkruimte naam: de naam die moet worden gebruikt voor de Azure Machine Learning werk ruimte die wordt gemaakt. De naam van de werk ruimte moet tussen de 3 en 33 tekens lang zijn. De naam mag alleen alfanumerieke tekens en '-' bevatten.
   * Locatie: Selecteer de locatie waar de resources worden gemaakt.

Zie [resources implementeren vanuit een aangepaste sjabloon](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)voor meer informatie.

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

In dit voor beeld wordt ervan uitgegaan dat u de sjabloon hebt opgeslagen in een bestand met de naam `azuredeploy.json` in de huidige map:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Zie [resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) en een [privé Resource Manager-sjabloon met SAS-token en Azure PowerShell implementeren](../azure-resource-manager/templates/secure-template-with-sas-token.md)voor meer informatie.

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

In dit voor beeld wordt ervan uitgegaan dat u de sjabloon hebt opgeslagen in een bestand met de naam `azuredeploy.json` in de huidige map:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Zie [resources implementeren met Resource Manager-sjablonen en Azure cli](../azure-resource-manager/templates/deploy-cli.md) en een [persoonlijke Resource Manager-sjabloon implementeren met SAS-token en Azure cli](../azure-resource-manager/templates/secure-template-with-sas-token.md)voor meer informatie.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="resource-provider-errors"></a>Fouten van de resource provider

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault toegangs beleid en Azure Resource Manager sjablonen

Wanneer u een Azure Resource Manager sjabloon gebruikt om de werk ruimte en de gekoppelde resources (inclusief Azure Key Vault) meerdere keren te maken. Gebruik bijvoorbeeld de sjabloon meerdere keren met dezelfde para meters als onderdeel van een continue integratie-en implementatie pijplijn.

De meeste bewerkingen voor het maken van resources via sjablonen zijn idempotent, maar Key Vault wist het toegangs beleid telkens wanneer de sjabloon wordt gebruikt. Als u het toegangs beleid wist, wordt de toegang tot de Key Vault verbroken voor een bestaande werk ruimte waarin deze wordt gebruikt. Bijvoorbeeld: stop/Create-functionaliteit van Azure Notebooks VM kan mislukken.  

Om dit probleem te voor komen, raden we u aan een van de volgende benaderingen te volgen:

* Implementeer de sjabloon niet meer dan één keer voor dezelfde para meters. Of verwijder de bestaande resources voordat u de sjabloon opnieuw maakt.

* Controleer de Key Vault toegangs beleid en gebruik vervolgens dit beleid om de eigenschap `accessPolicies` van de sjabloon in te stellen. Gebruik de volgende Azure CLI-opdracht om het toegangs beleid weer te geven:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Zie de [AccessPolicyEntry-object verwijzing](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry)voor meer informatie over het gebruik van de sectie `accessPolicies` van de sjabloon.

* Controleer of de Key Vault resource al bestaat. Als dit het geval is, moet u het niet opnieuw maken via de sjabloon. Als u bijvoorbeeld de bestaande Key Vault wilt gebruiken in plaats van een nieuwe te maken, moet u de volgende wijzigingen aanbrengen in de sjabloon:

    * **Voeg** een para meter toe die de id van een bestaande Key Vault resource accepteert:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Verwijder** de sectie waarmee een Key Vault resource wordt gemaakt:

        ```json
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
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Verwijder** de `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` regel uit de sectie `dependsOn` van de werk ruimte. **Wijzig** ook de `keyVault` vermelding in de sectie `properties` van de werk ruimte om te verwijzen naar de `keyVaultId`-para meter:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
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
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    Nadat u deze wijzigingen hebt aangebracht, kunt u de ID van de bestaande Key Vault resource opgeven wanneer u de sjabloon uitvoert. De sjabloon zal de Key Vault vervolgens opnieuw gebruiken door de eigenschap `keyVault` van de werk ruimte in te stellen op de bijbehorende ID.

    Als u de ID van de Key Vault wilt ophalen, kunt u verwijzen naar de uitvoer van de oorspronkelijke sjabloon run of de Azure CLI gebruiken. De volgende opdracht is een voor beeld van het gebruik van de Azure CLI om de Key Vault Resource-ID op te halen:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met de volgende tekst:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Volgende stappen

* [Resources implementeren met Resource Manager-sjablonen en Resource Manager-rest API](../azure-resource-manager/templates/deploy-rest.md).
* [Azure-resource groepen maken en implementeren via Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
