---
title: Een werkruimte maken met azure resourcemanager-sjabloon
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van een Azure Resource Manager-sjabloon om een nieuwe Azure Machine Learning-werkruimte te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.custom: seoapril2019
ms.openlocfilehash: 9403cc05ed5b31f3b76c16c4232506e2ddc5da2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402908"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Een Azure Resource Manager-sjabloon gebruiken om een werkruimte voor Azure Machine Learning te maken

In dit artikel leert u verschillende manieren om een Azure Machine Learning-werkruimte te maken met Azure Resource Manager-sjablonen. Met een resourcemanagersjabloon u eenvoudig resources maken als één gecoördineerde bewerking. Een sjabloon is een JSON-document dat de resources definieert die nodig zijn voor een implementatie. Het kan ook implementatieparameters opgeven. Parameters worden gebruikt om invoerwaarden op te geven bij het gebruik van de sjabloon.

Zie [Een toepassing implementeren met azure resource manager-sjabloon voor](../azure-resource-manager/templates/deploy-powershell.md)meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Als u er geen hebt, probeert u de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)

* Als u een sjabloon uit een CLI wilt gebruiken, hebt u [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) of Azure [CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)nodig.

## <a name="resource-manager-template"></a>Resource Manager-sjabloon

De volgende sjabloon Resourcebeheer kan worden gebruikt om een Azure Machine Learning-werkruimte en bijbehorende Azure-resources te maken:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Met deze sjabloon worden de volgende Azure-services gemaakt:

* Azure-brongroep
* Azure Storage-account
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning-werkruimte

De resourcegroep is de container die de services bevat. De verschillende services zijn vereist door de Azure Machine Learning-werkruimte.

De voorbeeldsjabloon heeft twee parameters:

* De **locatie** waar de resourcegroep en -services worden gemaakt.

    De sjabloon gebruikt de locatie die u voor de meeste bronnen selecteert. De uitzondering is de Application Insights-service, die niet beschikbaar is op alle locaties die de andere services zijn. Als u een locatie selecteert waar deze niet beschikbaar is, wordt de service gemaakt op de locatie Zuid Centraal in de VS.

* De **naam van**de werkruimte , de vriendelijke naam van de Azure Machine Learning-werkruimte.

    > [!NOTE]
    > De naam van de werkruimte is hoofdletters ongevoelig.

    De namen van de andere diensten worden willekeurig gegenereerd.

> [!TIP]
> Terwijl de sjabloon die aan dit document is gekoppeld, een nieuw Azure Container Registry maakt, u ook een nieuwe werkruimte maken zonder een containerregister te maken. Een bewerking wordt gemaakt wanneer u een bewerking uitvoert waarvoor een containerregister vereist is. Bijvoorbeeld het trainen of implementeren van een model.
>
> U ook verwijzen naar een bestaand containerregister of opslagaccount in de azure resource manager-sjabloon, in plaats van een nieuw account te maken.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Zie de volgende artikelen voor meer informatie over sjablonen:

* [Azure Resource Manager-sjablonen maken](../azure-resource-manager/templates/template-syntax.md)
* [Een toepassing implementeren met Azure Resource Manager-sjablonen](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices-brontypen](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Geavanceerde sjabloon

In de volgende voorbeeldsjabloon wordt uitgelegd hoe u een werkruimte met drie instellingen maakt:

* Hoge vertrouwelijkheidsinstellingen voor de werkruimte inschakelen
* Versleuteling inschakelen voor de werkruimte
* Gebruikt een bestaande Azure Key Vault

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

Als u de id van de sleutelkluis en de sleuteluri wilt krijgen die deze sjabloon nodig heeft, u de Azure CLI gebruiken. De volgende opdracht is een voorbeeld van het gebruik van de Azure CLI om de Key Vault-bron-ID en URI op te halen:

```azurecli-interactive
az keyvault show --name mykeyvault --resource-group myresourcegroup --query "[id, properties.vaultUri]"
```

Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met de volgende tekst. De eerste waarde is de ID en de tweede is de URI:

```text
[
  "/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault",
  "https://mykeyvault.vault.azure.net/"
]
```

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Volg de stappen in [Resources implementeren van aangepaste sjabloon](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Plak de sjabloon in dit document in wanneer u bij het scherm __Sjabloon bewerken__ aankomt.
1. Selecteer __Opslaan__ om de sjabloon te gebruiken. Geef de volgende informatie en ga akkoord met de vermelde algemene voorwaarden:

   * Abonnement: selecteer het Azure-abonnement dat u voor deze bronnen wilt gebruiken.
   * Resourcegroep: selecteer of maak een resourcegroep om de services te bevatten.
   * Naam van werkruimte: de naam die moet worden gebruikt voor de Azure Machine Learning-werkruimte die wordt gemaakt. De naam van de werkruimte moet tussen 3 en 33 tekens liggen. Het mag alleen alfanumerieke tekens en '-' bevatten.
   * Locatie: selecteer de locatie waar de resources worden gemaakt.

Zie Resources [implementeren van aangepaste sjabloon voor](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)meer informatie.

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

In dit voorbeeld wordt ervan uitgegaan dat `azuredeploy.json` u de sjabloon hebt opgeslagen in een bestand met de naam in de huidige map:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Zie [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell en](../azure-resource-manager/templates/deploy-powershell.md) Sjabloon [Privéresourcebeheer implementeren met SAS-token en Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md)voor meer informatie.

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

In dit voorbeeld wordt ervan uitgegaan dat `azuredeploy.json` u de sjabloon hebt opgeslagen in een bestand met de naam in de huidige map:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Zie [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../azure-resource-manager/templates/deploy-cli.md) en Sjabloon [Privéresourcebeheer implementeren met SAS-token en Azure CLI](../azure-resource-manager/templates/secure-template-with-sas-token.md)voor meer informatie.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="resource-provider-errors"></a>Fouten in resourceprovider

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault-toegangsbeleid en Azure Resource Manager-sjablonen

Wanneer u een Azure Resource Manager-sjabloon gebruikt om de werkruimte en bijbehorende resources (inclusief Azure Key Vault) meerdere keren te maken. Bijvoorbeeld, het gebruik van de sjabloon meerdere keren met dezelfde parameters als onderdeel van een continue integratie en implementatie pijplijn.

De meeste bewerkingen voor het maken van resources via sjablonen zijn idempotent, maar Key Vault wist het toegangsbeleid telkens wanneer de sjabloon wordt gebruikt. Als u het toegangsbeleid vrijmaakt, wordt de toegang tot de Sleutelkluis afgebroken voor elke bestaande werkruimte die deze gebruikt. Stop/Create-functionaliteiten van De VM voor Azure-laptops kunnen bijvoorbeeld mislukken.  

Om dit probleem te voorkomen, raden we een van de volgende benaderingen aan:

* Implementeer de sjabloon niet meer dan één keer voor dezelfde parameters. Of verwijder de bestaande bronnen voordat u de sjabloon gebruikt om ze opnieuw te maken.

* Bestudeer het toegangsbeleid voor key vault `accessPolicies` en gebruik dit beleid om de eigenschap van de sjabloon in te stellen. Als u het toegangsbeleid wilt weergeven, gebruikt u de volgende opdracht Azure CLI:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Zie de verwijzing `accessPolicies` naar [accesspolicyentry-objecten](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry)voor meer informatie over het gebruik van de sectie van de sjabloon.

* Controleer of de Key Vault-bron al bestaat. Als dit het wel het doet, maak het dan niet opnieuw via de sjabloon. Als u bijvoorbeeld de bestaande sleutelkluis wilt gebruiken in plaats van een nieuwe sleutelte maken, voert u de volgende wijzigingen aan in de sjabloon:

    * **Voeg** een parameter toe die de id van een bestaande Key Vault-bron accepteert:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Verwijder** de sectie waarmee een Key Vault-bron wordt gemaakt:

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

    * **Verwijder** `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` de regel `dependsOn` uit het gedeelte van de werkruimte. **Wijzig** ook `keyVault` de `properties` vermelding in het gedeelte `keyVaultId` van de werkruimte om naar de parameter te verwijzen:

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

    Na deze wijzigingen u de id van de bestaande Key Vault-bron opgeven wanneer u de sjabloon uitvoert. De sjabloon hergebruikt de Sleutelkluis `keyVault` vervolgens door de eigenschap van de werkruimte in te stellen op de id.

    Als u de id van de sleutelkluis wilt krijgen, u verwijzen naar de uitvoer van de oorspronkelijke sjabloon of de Azure CLI gebruiken. De volgende opdracht is een voorbeeld van het gebruik van de Azure CLI om de Key Vault-bron-id op te halen:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met de volgende tekst:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Volgende stappen

* [Resources implementeren met Resource Manager-sjablonen en Rest API voor ResourceBeheer](../azure-resource-manager/templates/deploy-rest.md).
* [Azure-brongroepen maken en implementeren via Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
