---
title: Meerdere exemplaren van een uitvoer waarde definiëren
description: Gebruik een Kopieer bewerking in een Azure Resource Manager sjabloon om meerdere keren te herhalen wanneer een waarde uit een implementatie wordt geretourneerd.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: db5c548c7bd4c60357d3656b1273b0192c497459
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624772"
---
# <a name="output-iteration-in-azure-resource-manager-templates"></a>Uitvoer herhaling in Azure Resource Manager sjablonen

In dit artikel wordt beschreven hoe u meer dan één waarde voor een uitvoer in uw Azure Resource Manager-sjabloon maakt. Door het element **copy** toe te voegen aan de sectie outputs van uw sjabloon, kunt u tijdens de implementatie dynamisch een aantal items retour neren.

U kunt ook kopiëren met [resources](copy-resources.md), [Eigenschappen in een resource](copy-properties.md)en [variabelen](copy-variables.md)gebruiken.

## <a name="outputs-iteration"></a>Herhalingen van uitvoer

Het element Copy heeft de volgende algemene indeling:

```json
"copy": [
  {
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

De eigenschap **Count** geeft het aantal iteraties op dat u voor de uitvoer waarde wilt.

De eigenschap **input** geeft de eigenschappen aan die u wilt herhalen. U maakt een matrix van elementen die zijn gemaakt op basis van de waarde in de eigenschap **input** . Dit kan één eigenschap zijn (zoals een teken reeks) of een object met verschillende eigenschappen.

In het volgende voor beeld wordt een variabele aantal opslag accounts gemaakt en wordt een eind punt voor elk opslag account geretourneerd:

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

De vorige sjabloon retourneert een matrix met de volgende waarden:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

In het volgende voor beeld worden drie eigenschappen van de nieuwe opslag accounts geretourneerd.

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

In het voor gaande voor beeld wordt een matrix met de volgende waarden geretourneerd:

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

* Zie [zelf studie: meerdere resource-instanties maken met behulp van Resource Manager-sjablonen](template-tutorial-create-multiple-instances.md)als u een zelf studie wilt door lopen.
* Zie voor andere toepassingen van het element copy:
  * [Resource iteratie in Azure Resource Manager sjablonen](copy-resources.md)
  * [Eigenschaps herhaling in Azure Resource Manager sjablonen](copy-properties.md)
  * [Variabele herhaling in Azure Resource Manager sjablonen](copy-variables.md)
* Zie [Azure Resource Manager sjablonen ontwerpen](template-syntax.md)voor meer informatie over de secties van een sjabloon.
* Zie [een toepassing implementeren met Azure Resource Manager sjabloon](deploy-powershell.md)voor meer informatie over het implementeren van uw sjabloon.

