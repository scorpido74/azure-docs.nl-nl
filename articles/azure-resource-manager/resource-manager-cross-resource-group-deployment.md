---
title: '& Resource groep Azure-resources voor meerdere abonnementen implementeren'
description: Laat zien hoe u tijdens de implementatie meer dan één Azure-abonnement en-resource groep kunt bereiken.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/02/2018
ms.author: tomfitz
ms.openlocfilehash: c90096043f54eb8db5834fbe83ed1d6ae710d371
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528324"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Azure-resources implementeren voor meer dan één abonnement of resource groep

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Doorgaans implementeert u alle resources in uw sjabloon tot één [resource groep](resource-group-overview.md). Er zijn echter scenario's waarin u een set resources samen wilt implementeren, maar deze wilt plaatsen in verschillende resource groepen of-abonnementen. U kunt bijvoorbeeld de virtuele machine van de back-up voor Azure Site Recovery implementeren in een afzonderlijke resource groep en locatie. Met Resource Manager kunt u geneste sjablonen gebruiken om verschillende abonnementen en resource groepen te richten dan het abonnement en de resource groep die worden gebruikt voor de bovenliggende sjabloon.

> [!NOTE]
> U kunt implementeren in slechts vijf resource groepen in één implementatie. Deze beperking betekent meestal dat u kunt implementeren in één resource groep die is opgegeven voor de bovenliggende sjabloon en Maxi maal vier resource groepen in geneste of gekoppelde implementaties. Als uw bovenliggende sjabloon alleen geneste of gekoppelde sjablonen bevat en zelf geen resources implementeert, kunt u Maxi maal vijf resource groepen toevoegen in geneste of gekoppelde implementaties.

## <a name="specify-a-subscription-and-resource-group"></a>Een abonnement en een resource groep opgeven

Als u een andere resource wilt instellen, gebruikt u een geneste of gekoppelde sjabloon. Het resource type `Microsoft.Resources/deployments` bevat para meters voor `subscriptionId` en `resourceGroup`. Met deze eigenschappen kunt u een ander abonnement en een andere resource groep voor de geneste implementatie opgeven. Alle resource groepen moeten bestaan voordat u de implementatie uitvoert. Als u de abonnements-ID of de resource groep niet opgeeft, wordt het abonnement en de resource groep van de bovenliggende sjabloon gebruikt.

Het account dat u gebruikt om de sjabloon te implementeren, moet machtigingen hebben om te implementeren naar de opgegeven abonnements-ID. Als het opgegeven abonnement bestaat in een andere Azure Active Directory Tenant, moet u [gast gebruikers toevoegen vanuit een andere map](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

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

In het volgende voor beeld worden twee opslag accounts geïmplementeerd: één in de resource groep die is opgegeven tijdens de implementatie en één in een resource groep die is opgegeven in de para meter `secondResourceGroup`:

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
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
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
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
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
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        }
    ]
}
```

Als u `resourceGroup` instelt op de naam van een resource groep die niet bestaat, mislukt de implementatie.

## <a name="use-the-resourcegroup-and-subscription-functions"></a>De functies resourceGroup () en Subscription () gebruiken

Voor implementaties van meerdere resource groepen worden de functies [resourceGroup ()](resource-group-template-functions-resource.md#resourcegroup) en [Subscription ()](resource-group-template-functions-resource.md#subscription) verschillend omgezet op basis van de manier waarop u de geneste sjabloon opgeeft. 

Als u één sjabloon in een andere sjabloon insluit, worden de functies in de geneste sjabloon omgezet in de bovenliggende resource groep en het-abonnement. Een Inge sloten sjabloon maakt gebruik van de volgende indeling:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() and subscription() refer to parent resource group/subscription
    }
}
```

Als u een koppeling naar een afzonderlijke sjabloon maakt, worden de functies in de gekoppelde sjabloon omgezet in de geneste resource groep en het gegroepeerde abonnement. Een gekoppelde sjabloon maakt gebruik van de volgende indeling:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() and subscription() in linked template refer to linked resource group/subscription
    }
}
```

## <a name="example-templates"></a>Voorbeeld sjablonen

De volgende sjablonen illustreren meerdere implementaties van resource groepen. Scripts voor het implementeren van de sjablonen worden weer gegeven na de tabel.

|Sjabloon  |Beschrijving  |
|---------|---------|
|[Sjabloon voor meerdere abonnementen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Hiermee wordt één opslag account geïmplementeerd in één resource groep en één opslag account naar een tweede resource groep. Neem een waarde op voor de abonnements-ID wanneer de tweede resource groep zich in een ander abonnement bevindt. |
|[Sjabloon voor eigenschappen van meerdere bron groepen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Laat zien hoe de `resourceGroup()` functie wordt opgelost. Er worden geen resources geïmplementeerd. |

### <a name="powershell"></a>PowerShell

Voor Power shell, voor het implementeren van twee opslag accounts voor twee resource groepen in **hetzelfde abonnement**, gebruikt u:

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

Voor Power shell, voor het implementeren van twee opslag accounts op **twee abonnementen**, gebruikt u:

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

Gebruik voor Power shell om te testen hoe het **resource groeps object** wordt omgezet voor de bovenliggende sjabloon, inline sjabloon en gekoppelde sjabloon:

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

In het vorige voor beeld **parentRG** en **InlineRG** omgezet in **parentGroup**. **linkedRG** wordt omgezet in **linkedGroup**. De uitvoer van het vorige voor beeld is:

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 inlineRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 linkedRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
                                               "name": "linkedGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
```

### <a name="azure-cli"></a>Azure CLI

Voor Azure CLI, voor het implementeren van twee opslag accounts voor twee resource groepen in **hetzelfde abonnement**, gebruikt u:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Voor Azure CLI, voor het implementeren van twee opslag accounts op **twee abonnementen**, gebruikt u:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

Voor Azure CLI, om te testen hoe het **resource groeps object** wordt omgezet voor de bovenliggende sjabloon, inline sjabloon en gekoppelde sjabloon, gebruikt u:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

In het vorige voor beeld **parentRG** en **InlineRG** omgezet in **parentGroup**. **linkedRG** wordt omgezet in **linkedGroup**. De uitvoer van het vorige voor beeld is:

```azurecli
...
"outputs": {
  "inlineRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "linkedRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
      "location": "southcentralus",
      "name": "linkedGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "parentRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  }
},
...
```

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht in de structuur en syntaxis van Azure Resource Manager sjablonen](resource-group-authoring-templates.md)voor meer informatie over het definiëren van para meters in uw sjabloon.
* Zie [problemen met algemene Azure-implementatie fouten oplossen met Azure Resource Manager](resource-manager-common-deployment-errors.md)voor tips over het oplossen van veelvoorkomende implementatie fouten.
* Zie voor meer informatie over het implementeren van een sjabloon waarvoor een SAS-token is vereist een [persoonlijke sjabloon implementeren met SAS-token](resource-manager-powershell-sas-token.md).
