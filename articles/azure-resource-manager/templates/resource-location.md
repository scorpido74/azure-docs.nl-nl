---
title: Locatie van sjabloonbron
description: Beschrijft hoe u de resourcelocatie instelt in een Azure Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: a8324dac1232eecd5624e5f1dc0e6656295c0a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156426"
---
# <a name="set-resource-location-in-arm-template"></a>Resourcelocatie instellen in ARM-sjabloon

Wanneer u een ARM-sjabloon (Azure Resource Manager) implementeert, moet u voor elke resource een locatie opgeven. De locatie hoeft niet dezelfde locatie te hebben als de locatie van de resourcegroep.

## <a name="get-available-locations"></a>Beschikbare locaties optestellen

Verschillende resourcetypen worden op verschillende locaties ondersteund. Als u de ondersteunde locaties voor een resourcetype wilt opvragen, gebruikt u Azure PowerShell of Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Locatieparameter gebruiken

Als u flexibiliteit wilt toestaan bij het implementeren van uw sjabloon, gebruikt u een parameter om de locatie voor resources op te geven. Stel de standaardwaarde van `resourceGroup().location`de parameter in op .

In het volgende voorbeeld wordt een opslagaccount weergegeven dat is geïmplementeerd op een locatie die als parameter is opgegeven:

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
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
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

* Zie [Sjabloonfuncties](template-functions.md)azure resource manager voor de volledige lijst met sjabloonfuncties .
* Zie De structuur en [syntaxis van ARM-sjablonen begrijpen](template-syntax.md)voor meer informatie over sjabloonbestanden.
