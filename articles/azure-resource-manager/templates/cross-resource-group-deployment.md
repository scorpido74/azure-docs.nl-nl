---
title: '& resource groep voor het implementeren van resources voor meerdere abonnementen'
description: Laat zien hoe u tijdens de implementatie meer dan één Azure-abonnement en-resource groep kunt bereiken.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 70868f5a3598c26ffff81f0ad3536a6c5c0a7e53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460344"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Azure-resources implementeren voor meer dan één abonnement of resource groep

Doorgaans implementeert u alle resources in uw sjabloon tot één [resource groep](../management/overview.md). Er zijn echter scenario's waarin u een set resources samen wilt implementeren, maar deze wilt plaatsen in verschillende resource groepen of-abonnementen. U kunt bijvoorbeeld de virtuele machine van de back-up voor Azure Site Recovery implementeren in een afzonderlijke resource groep en locatie. Met Resource Manager kunt u geneste sjablonen gebruiken om meer dan één abonnement en resource groep te bereiken.

> [!NOTE]
> U kunt implementeren in slechts vijf resource groepen in één implementatie. Deze beperking betekent meestal dat u kunt implementeren in één resource groep die is opgegeven voor de bovenliggende sjabloon en Maxi maal vier resource groepen in geneste of gekoppelde implementaties. Als uw bovenliggende sjabloon alleen geneste of gekoppelde sjablonen bevat en zelf geen resources implementeert, kunt u Maxi maal vijf resource groepen toevoegen in geneste of gekoppelde implementaties.

## <a name="specify-subscription-and-resource-group"></a>Abonnement en resource groep opgeven

Als u een andere resource groep of een ander abonnement wilt instellen, gebruikt u een [geneste of gekoppelde sjabloon](linked-templates.md). Het `Microsoft.Resources/deployments` resource type bevat para `subscriptionId` meters `resourceGroup`voor en, waarmee u het abonnement en de resource groep voor de geneste implementatie kunt opgeven. Als u de abonnements-ID of de resource groep niet opgeeft, wordt het abonnement en de resource groep van de bovenliggende sjabloon gebruikt. Alle resource groepen moeten bestaan voordat u de implementatie uitvoert.

Het account dat u gebruikt om de sjabloon te implementeren, moet machtigingen hebben om te implementeren naar de opgegeven abonnements-ID. Als het opgegeven abonnement bestaat in een andere Azure Active Directory Tenant, moet u [gast gebruikers toevoegen vanuit een andere map](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Als u een andere resource groep en een ander abonnement wilt opgeven, gebruikt u:

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

Als uw resource groepen zich in hetzelfde abonnement bevinden, kunt u de **abonnements** waarde verwijderen.

In het volgende voor beeld worden twee opslag accounts geïmplementeerd. Het eerste opslag account wordt geïmplementeerd naar de resource groep die tijdens de implementatie is opgegeven. Het tweede opslag account wordt geïmplementeerd naar de resource groep die is opgegeven `secondResourceGroup` in `secondSubscriptionID` de para meters en:

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

Als u de `resourceGroup` naam instelt van een resource groep die niet bestaat, mislukt de implementatie.

Als u de voor gaande sjabloon wilt testen en de resultaten wilt weer geven, gebruikt u Power shell of Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u twee opslag accounts wilt implementeren voor twee resource groepen in **hetzelfde abonnement**, gebruikt u:

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

Als u twee opslag accounts wilt implementeren op **twee abonnementen**, gebruikt u:

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

Als u twee opslag accounts wilt implementeren voor twee resource groepen in **hetzelfde abonnement**, gebruikt u:

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

Als u twee opslag accounts wilt implementeren op **twee abonnementen**, gebruikt u:

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

De functies [resourceGroup ()](template-functions-resource.md#resourcegroup) en [Subscription ()](template-functions-resource.md#subscription) worden op verschillende manieren opgelost, afhankelijk van hoe u de sjabloon opgeeft. Wanneer u een koppeling naar een externe sjabloon maakt, worden de functies altijd omgezet in het bereik voor die sjabloon. Wanneer u een sjabloon in een bovenliggende sjabloon nest, gebruikt u `expressionEvaluationOptions` de eigenschap om op te geven of de functies worden omgezet in de resource groep en het abonnement voor de bovenliggende sjabloon of de geneste sjabloon. Stel de eigenschap in `inner` op om om te zetten in het bereik voor de geneste sjabloon. Stel de eigenschap in `outer` op om om te zetten naar het bereik van de bovenliggende sjabloon.

In de volgende tabel wordt aangegeven of de functies worden omgezet naar de bovenliggende of Inge sloten resource groep en het-abonnement.

| Sjabloon type | Bereik | Oplossing |
| ------------- | ----- | ---------- |
| nest        | Outer (standaard) | Bovenliggende resource groep |
| nest        | wend | Subresourcegroep |
| Btrieve        | N.v.t.   | Subresourcegroep |

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) ziet u:

* geneste sjabloon met het standaard bereik (buitenste)
* geneste sjabloon met binnenste bereik
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

Als u de voor gaande sjabloon wilt testen en de resultaten wilt weer geven, gebruikt u Power shell of Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

De uitvoer van het vorige voor beeld is:

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

De uitvoer van het vorige voor beeld is:

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

* Zie [inzicht in de structuur en syntaxis van Azure Resource Manager sjablonen](template-syntax.md)voor meer informatie over het definiëren van para meters in uw sjabloon.
* Zie [problemen met algemene Azure-implementatie fouten oplossen met Azure Resource Manager](common-deployment-errors.md)voor tips over het oplossen van veelvoorkomende implementatie fouten.
* Zie voor meer informatie over het implementeren van een sjabloon waarvoor een SAS-token is vereist een [persoonlijke sjabloon implementeren met SAS-token](secure-template-with-sas-token.md).
