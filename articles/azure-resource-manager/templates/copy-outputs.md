---
title: Meerdere exemplaren van een uitvoerwaarde definiëren
description: Gebruik de kopieerbewerking in een Azure Resource Manager-sjabloon om meerdere keren te herhalen wanneer u een waarde van een implementatie retourt.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3889260d02f438274c80e99e99136515499443e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153383"
---
# <a name="output-iteration-in-arm-templates"></a>Uitvoeriteratie in ARM-sjablonen

In dit artikel ziet u hoe u meer dan één waarde maakt voor een uitvoer in uw Azure Resource Manager-sjabloon (ARM). Door het **kopieerelement** toe te voegen aan het gedeelte uitvoer van uw sjabloon, u een aantal items dynamisch retourneren tijdens de implementatie.

U ook kopiëren met [resources,](copy-resources.md) [eigenschappen in een resource](copy-properties.md)en [variabelen](copy-variables.md)gebruiken.

## <a name="outputs-iteration"></a>Uitvoeriteratie

Het kopieerelement heeft de volgende algemene indeling:

```json
"copy": [
  {
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

De eigenschap **aantal** geeft het gewenste aantal iteraties op voor de uitvoerwaarde.

De **eigenschap invoer** geeft de eigenschappen op die u wilt herhalen. U maakt een array met elementen die zijn opgebouwd uit de waarde in de **eigenschap invoer.** Het kan een enkele eigenschap zijn (zoals een tekenreeks) of een object met meerdere eigenschappen.

In het volgende voorbeeld wordt een variabel aantal opslagaccounts gemaakt en wordt een eindpunt geretourneerd voor elk opslagaccount:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

De voorgaande sjabloon retourneert een array met de volgende waarden:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

In het volgende voorbeeld worden drie eigenschappen van de nieuwe opslagaccounts geretourneerd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

In het voorgaande voorbeeld wordt een array met de volgende waarden geretourneerd:

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Zelfstudie: meerdere resource-exemplaren maken met ARM-sjablonen](template-tutorial-create-multiple-instances.md)voor het doorlopen van een zelfstudie.
* Zie voor andere toepassingen van het kopieerelement:
  * [Resourceiteratie in ARM-sjablonen](copy-resources.md)
  * [Eigenschapiteratie in ARM-sjablonen](copy-properties.md)
  * [Variabele iteratie in ARM-sjablonen](copy-variables.md)
* Zie [ARM-sjablonen ontwerpen](template-syntax.md)als u meer wilt weten over de secties van een sjabloon.
* Zie [Een toepassing implementeren met ARM-sjabloon](deploy-powershell.md)voor meer informatie over het implementeren van uw sjabloon.

