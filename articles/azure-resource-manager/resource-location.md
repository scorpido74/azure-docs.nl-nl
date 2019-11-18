---
title: Resource locatie van sjabloon
description: Hierin wordt beschreven hoe u een resource locatie instelt in een Azure Resource Manager sjabloon.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 1e711b2cfeb42c33dbfa68b1fbdabd42cbd46d10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150620"
---
# <a name="set-resource-location-in-resource-manager-template"></a>Resource locatie instellen in Resource Manager-sjabloon

Bij het implementeren van een sjabloon moet u een locatie voor elke resource opgeven. De locatie hoeft niet dezelfde locatie te zijn als de locatie van de resource groep.

## <a name="get-available-locations"></a>Beschik bare locaties ophalen

Verschillende resource typen worden ondersteund op verschillende locaties. Gebruik Azure PowerShell of Azure CLI om de ondersteunde locaties voor een resource type op te halen.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Locatie parameter gebruiken

Gebruik een para meter om de locatie voor resources op te geven om flexibiliteit te bieden bij het implementeren van uw sjabloon. Stel de standaard waarde van de para meter in op `resourceGroup().location`.

In het volgende voor beeld ziet u een opslag account dat is geïmplementeerd op een locatie die is opgegeven als een para meter:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
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
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Resource Manager-sjabloon functies](resource-group-template-functions.md)voor een volledige lijst met sjabloon functies.
* Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager-sjablonen](resource-group-authoring-templates.md)voor meer informatie over sjabloon bestanden.
