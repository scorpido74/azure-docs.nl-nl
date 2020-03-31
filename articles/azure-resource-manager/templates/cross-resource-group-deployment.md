---
title: Resources cross-subscription & resourcegroep implementeren
description: Hier ziet u hoe u tijdens de implementatie meer dan één Azure-abonnement en resourcegroep targeten.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 70868f5a3598c26ffff81f0ad3536a6c5c0a7e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460344"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Azure-resources implementeren in meer dan één abonnementsgroep of brongroep

Doorgaans implementeert u alle resources in uw sjabloon in één [resourcegroep.](../management/overview.md) Er zijn echter scenario's waarin u een set resources samen wilt implementeren, maar deze in verschillende resourcegroepen of -abonnementen wilt plaatsen. U bijvoorbeeld de virtuele back-upmachine voor Azure Site Recovery implementeren in een afzonderlijke brongroep en -locatie. Met Resource Manager u geneste sjablonen gebruiken om meer dan één abonnements- en resourcegroep te targeten.

> [!NOTE]
> U slechts vijf resourcegroepen in één implementatie implementeren. Deze beperking betekent doorgaans dat u implementeren in één resourcegroep die is opgegeven voor de bovenliggende sjabloon en maximaal vier resourcegroepen in geneste of gekoppelde implementaties. Als uw bovenliggende sjabloon echter alleen geneste of gekoppelde sjablonen bevat en zelf geen resources implementeert, u maximaal vijf resourcegroepen opnemen in geneste of gekoppelde implementaties.

## <a name="specify-subscription-and-resource-group"></a>Abonnements- en resourcegroep opgeven

Als u een andere resourcegroep of -abonnement wilt targeten, gebruikt u een [geneste of gekoppelde sjabloon](linked-templates.md). Het `Microsoft.Resources/deployments` resourcetype bevat `subscriptionId` `resourceGroup`parameters voor en, waarmee u de abonnements- en resourcegroep voor de geneste implementatie opgeven. Als u de abonnements-id of resourcegroep niet opgeeft, wordt de abonnements- en brongroep van de bovenliggende sjabloon gebruikt. Alle resourcegroepen moeten bestaan voordat de implementatie wordt uitgevoerd.

Het account dat u gebruikt om de sjabloon te implementeren, moet machtigingen hebben om te implementeren op de opgegeven abonnements-id. Als het opgegeven abonnement bestaat in een andere Azure Active Directory-tenant, moet u [gastgebruikers toevoegen uit een andere map.](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)

Gebruik het volgende om een andere brongroep en -abonnement op te geven:

```json
"resources": [
  {
    "apiVersion": "2017-05-10",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
    "resourceGroup": "[parameters('secondResourceGroup')]",
    "subscriptionId": "[parameters('secondSubscriptionID')]",
    ...
  }
]
```

Als uw brongroepen in hetzelfde abonnement zitten, u de waarde van de **abonnementid** verwijderen.

In het volgende voorbeeld worden twee opslagaccounts geïmplementeerd. Het eerste opslagaccount wordt geïmplementeerd in de resourcegroep die tijdens de implementatie is opgegeven. Het tweede opslagaccount wordt geïmplementeerd in de `secondResourceGroup` `secondSubscriptionID` resourcegroep die is opgegeven in de parameters en parameters:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    },
    "secondResourceGroup": {
      "type": "string"
    },
    "secondSubscriptionID": {
      "type": "string",
      "defaultValue": ""
    },
    "secondStorageLocation": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
    "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2017-06-01",
      "name": "[variables('firstStorageName')]",
      "location": "[resourceGroup().location]",
      "sku":{
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "nestedTemplate",
      "resourceGroup": "[parameters('secondResourceGroup')]",
      "subscriptionId": "[parameters('secondSubscriptionID')]",
      "properties": {
      "mode": "Incremental",
      "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2017-06-01",
            "name": "[variables('secondStorageName')]",
            "location": "[parameters('secondStorageLocation')]",
            "sku":{
              "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
          }
          ]
      },
      "parameters": {}
      }
    }
  ]
}
```

Als u `resourceGroup` de naam instelt van een resourcegroep die niet bestaat, mislukt de implementatie.

Als u de voorgaande sjabloon wilt testen en de resultaten wilt bekijken, gebruikt u PowerShell of Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Als u twee opslagaccounts wilt implementeren in twee resourcegroepen in **hetzelfde abonnement,** gebruikt u:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Als u twee opslagaccounts wilt implementeren voor **twee abonnementen,** gebruikt u:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u twee opslagaccounts wilt implementeren in twee resourcegroepen in **hetzelfde abonnement,** gebruikt u:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Als u twee opslagaccounts wilt implementeren voor **twee abonnementen,** gebruikt u:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>Functies gebruiken

De functies [resourceGroup()](template-functions-resource.md#resourcegroup) en [subscription()](template-functions-resource.md#subscription) worden anders opgelost op basis van hoe u de sjabloon opgeeft. Wanneer u een koppeling maakt naar een externe sjabloon, worden de functies altijd opgelost in het bereik van die sjabloon. Wanneer u een sjabloon in een `expressionEvaluationOptions` bovenliggende sjabloon plaatst, gebruikt u de eigenschap om op te geven of de functies zijn opgelost voor de brongroep en het abonnement voor de bovenliggende sjabloon of de geneste sjabloon. Stel de `inner` eigenschap in om op te lossen op het bereik voor de geneste sjabloon. Stel de `outer` eigenschap in op het bereik van de bovenliggende sjabloon.

In de volgende tabel wordt weergegeven of de functies worden opgelost voor de bovenliggende of ingesloten brongroep en het abonnement.

| Sjabloontype | Bereik | Oplossing |
| ------------- | ----- | ---------- |
| Geneste        | buitenste (standaard) | Bovenliggende resourcegroep |
| Geneste        | Innerlijke | Subresourcegroep |
| Gekoppelde        | N.v.t.   | Subresourcegroep |

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) wordt het volgende voorbeeld weergegeven:

* geneste sjabloon met standaardbereik (buitenste) scope
* geneste sjabloon met binnenbereik
* gekoppelde sjabloon

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "defaultScopeTemplate",
      "resourceGroup": "inlineGroup",
      "properties": {
      "mode": "Incremental",
      "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
          ],
          "outputs": {
          "resourceGroupOutput": {
            "type": "string",
            "value": "[resourceGroup().name]"
          }
          }
      },
      "parameters": {}
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "innerScopeTemplate",
      "resourceGroup": "inlineGroup",
      "properties": {
      "expressionEvaluationOptions": {
          "scope": "inner"
      },
      "mode": "Incremental",
      "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
          ],
          "outputs": {
          "resourceGroupOutput": {
            "type": "string",
            "value": "[resourceGroup().name]"
          }
          }
      },
      "parameters": {}
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "resourceGroup": "linkedGroup",
      "properties": {
      "mode": "Incremental",
      "templateLink": {
          "contentVersion": "1.0.0.0",
          "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/resourceGroupName.json"
      },
      "parameters": {}
      }
    }
  ],
  "outputs": {
    "parentRG": {
      "type": "string",
      "value": "[concat('Parent resource group is ', resourceGroup().name)]"
    },
    "defaultScopeRG": {
      "type": "string",
      "value": "[concat('Default scope resource group is ', reference('defaultScopeTemplate').outputs.resourceGroupOutput.value)]"
    },
    "innerScopeRG": {
      "type": "string",
      "value": "[concat('Inner scope resource group is ', reference('innerScopeTemplate').outputs.resourceGroupOutput.value)]"
    },
    "linkedRG": {
      "type": "string",
      "value": "[concat('Linked resource group is ', reference('linkedTemplate').outputs.resourceGroupOutput.value)]"
    }
  }
}
```

Als u de voorgaande sjabloon wilt testen en de resultaten wilt bekijken, gebruikt u PowerShell of Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

De uitvoer uit het voorgaande voorbeeld is:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

De uitvoer uit het voorgaande voorbeeld is:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Volgende stappen

* Zie [De structuur en de syntaxis van Azure Resource Manager-sjablonen begrijpen](template-syntax.md)als u wilt begrijpen hoe u parameters in uw sjabloon definiëren.
* Zie [Veelvoorkomende Azure-implementatiefouten oplossen met Azure Resource Manager](common-deployment-errors.md)voor tips over het oplossen van veelvoorkomende implementatiefouten in Azure.
* Zie [Privésjabloon implementeren met SAS-token voor](secure-template-with-sas-token.md)informatie over het implementeren van een sjabloon waarvoor een SAS-token vereist is.
