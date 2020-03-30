---
title: Meerdere exemplaren van een variabele definiëren
description: Gebruik de kopieerbewerking in een Azure Resource Manager-sjabloon om meerdere keren te herhalen bij het maken van een variabele.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: ed0c2d87c48a18b0a065f6c76e1e69142a9df048
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153298"
---
# <a name="variable-iteration-in-arm-templates"></a>Variabele iteratie in ARM-sjablonen

In dit artikel ziet u hoe u meer dan één waarde maakt voor een variabele in uw Azure Resource Manager-sjabloon (ARM). Door het **kopieerelement** toe te voegen aan het gedeelte variabelen van uw sjabloon, u dynamisch het aantal items voor een variabele instellen tijdens de implementatie. U hoeft ook niet de syntaxis van de sjabloon te herhalen.

U ook kopiëren met [resources,](copy-resources.md) [eigenschappen in een resource](copy-properties.md)en [uitvoer](copy-outputs.md)gebruiken.

## <a name="variable-iteration"></a>Variabele iteratie

Het kopieerelement heeft de volgende algemene indeling:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

De **eigenschap naam** is een waarde die de lus identificeert. De eigenschap **aantal** geeft het aantal iteraties op dat u voor de variabele wilt hebben.

De **eigenschap invoer** geeft de eigenschappen op die u wilt herhalen. U maakt een array met elementen die zijn opgebouwd uit de waarde in de **eigenschap invoer.** Het kan een enkele eigenschap zijn (zoals een tekenreeks) of een object met meerdere eigenschappen.

In het volgende voorbeeld ziet u hoe u een array met tekenreekswaarden maakt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

De voorgaande sjabloon retourneert een array met de volgende waarden:

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

In het volgende voorbeeld ziet u hoe u een array met objecten maakt met drie eigenschappen: naam, diskSizeGB en diskIndex.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

In het voorgaande voorbeeld wordt een array met de volgende waarden geretourneerd:

```json
[
    {
        "name": "myDataDisk1",
        "diskSizeGB": "1",
        "diskIndex": 0
    },
    {
        "name": "myDataDisk2",
        "diskSizeGB": "1",
        "diskIndex": 1
    },
    {
        "name": "myDataDisk3",
        "diskSizeGB": "1",
        "diskIndex": 2
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> Variabele iteratie ondersteunt een offsetargument. De verschuiving moet na de naam van de iteratie komen, zoals copyIndex('diskNames', 1). Als u geen verschuivingswaarde opgeeft, wordt deze standaard ingesteld op 0 voor de eerste instantie.
>

U het kopieerelement ook binnen een variabele gebruiken. In het volgende voorbeeld wordt een object gemaakt dat een array als een van de waarden heeft.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

In het voorgaande voorbeeld wordt een object met de volgende waarden geretourneerd:

```json
{
    "sampleProperty": "sampleValue",
    "disks": [
        {
            "name": "myDataDisk1",
            "diskSizeGB": "1",
            "diskIndex": 0
        },
        {
            "name": "myDataDisk2",
            "diskSizeGB": "1",
            "diskIndex": 1
        },
        {
            "name": "myDataDisk3",
            "diskSizeGB": "1",
            "diskIndex": 2
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

In het volgende voorbeeld worden de verschillende manieren weergegeven waarop u kopiëren met variabelen gebruiken.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="copy-limits"></a>Kopieerlimieten

De telling mag niet hoger zijn dan 800.

De telling kan geen negatief getal zijn. Als u een sjabloon implementeert met Azure PowerShell 2.6 of hoger, Azure CLI 2.0.74 of hoger of REST **API-versie 2019-05-10** of hoger, u het aantal instellen op nul. Eerdere versies van PowerShell, CLI en de REST API ondersteunen geen nul voor telling.

## <a name="example-templates"></a>Voorbeeldsjablonen

In de volgende voorbeelden worden veelvoorkomende scenario's weergegeven voor het maken van meer dan één waarde voor een variabele.

|Template  |Beschrijving  |
|---------|---------|
|[Variabelen kopiëren](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Toont de verschillende manieren van herhalen op variabelen. |
|[Meerdere beveiligingsregels](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Implementeert verschillende beveiligingsregels naar een netwerkbeveiligingsgroep. Het construeert de beveiligingsregels van een parameter. Zie [meerdere NSG-parameterbestand](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)voor de parameter . |

## <a name="next-steps"></a>Volgende stappen

* Zie [Zelfstudie: meerdere resource-exemplaren maken met ARM-sjablonen](template-tutorial-create-multiple-instances.md)voor het doorlopen van een zelfstudie.
* Zie voor andere toepassingen van het kopieerelement:
  * [Resourceiteratie in ARM-sjablonen](copy-resources.md)
  * [Eigenschapiteratie in ARM-sjablonen](copy-properties.md)
  * [Uitvoeriteratie in ARM-sjablonen](copy-outputs.md)
* Zie [ARM-sjablonen ontwerpen](template-syntax.md)als u meer wilt weten over de secties van een sjabloon.
* Zie [Een toepassing implementeren met ARM-sjabloon](deploy-powershell.md)voor meer informatie over het implementeren van uw sjabloon.

