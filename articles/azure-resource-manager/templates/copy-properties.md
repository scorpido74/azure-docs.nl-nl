---
title: Meerdere exemplaren van een eigenschap definiëren
description: Gebruik de kopieerbewerking in een Azure Resource Manager-sjabloon om meerdere keren te herhalen bij het maken van een eigenschap op een resource.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 831ae1af202a1cdf52bdd2bdf0d9a042a97ba52f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391336"
---
# <a name="property-iteration-in-arm-templates"></a>Eigenschapiteratie in ARM-sjablonen

In dit artikel ziet u hoe u meer dan één instantie van een eigenschap maakt in uw Azure Resource Manager-sjabloon (ARM). Door het **kopieerelement** toe te voegen aan de sectie eigenschappen van een resource in uw sjabloon, u tijdens de implementatie dynamisch het aantal items voor een eigenschap instellen. U hoeft ook niet de syntaxis van de sjabloon te herhalen.

U ook kopiëren met [resources,](copy-resources.md) [variabelen](copy-variables.md)en [uitvoer](copy-outputs.md)gebruiken.

## <a name="property-iteration"></a>Eigenschapiteratie

Het kopieerelement heeft de volgende algemene indeling:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Geef voor **naam**de naam op van de resourceeigenschap die u wilt maken.

De eigenschap **aantal** geeft het aantal iteraties op dat u voor de eigenschap wilt hebben.

De **eigenschap invoer** geeft de eigenschappen op die u wilt herhalen. U maakt een array met elementen die zijn opgebouwd uit de waarde in de **eigenschap invoer.**

In het volgende voorbeeld `copy` ziet u hoe u deze toepast op de eigenschap dataDisks op een virtuele machine:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

Wanneer u `copyIndex` binnen een eigenschapsiteratie gebruikt, moet u de naam van de iteratie opgeven. Eigenschapiteratie ondersteunt ook een offsetargument. De verschuiving moet na de naam van de iteratie komen, zoals copyIndex('dataDisks', 1).

Resource Manager `copy` breidt de array uit tijdens de implementatie. De naam van de array wordt de naam van de eigenschap. De invoerwaarden worden de objecteigenschappen. De geïmplementeerde sjabloon wordt:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

De kopieerbewerking is handig bij het werken met arrays, omdat u elk element in de array herhalen. Gebruik `length` de functie op de array om het `copyIndex` aantal iteraties op te geven en de huidige index in de array op te halen.

Met de volgende voorbeeldsjabloon wordt een failovergroep gemaakt voor databases die als array worden doorgegeven.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "primaryServerName": {
            "type": "string"
        },
        "secondaryServerName": {
            "type": "string"
        },
        "databaseNames": {
            "type": "array",
            "defaultValue": [
                "mydb1",
                "mydb2",
                "mydb3"
            ]
        }
    },
    "variables": {
        "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers/failoverGroups",
            "apiVersion": "2015-05-01-preview",
            "name": "[variables('failoverName')]",
            "properties": {
                "readWriteEndpoint": {
                    "failoverPolicy": "Automatic",
                    "failoverWithDataLossGracePeriodMinutes": 60
                },
                "readOnlyEndpoint": {
                    "failoverPolicy": "Disabled"
                },
                "partnerServers": [
                    {
                        "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
                    }
                ],
                "copy": [
                    {
                        "name": "databases",
                        "count": "[length(parameters('databaseNames'))]",
                        "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
                    }
                ]
            }
        }
    ],
    "outputs": {
    }
}
```

Het kopieerelement is een array, zodat u meer dan één eigenschap voor de resource opgeven.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

U samen resource- en propertyiteratie gebruiken. Verwijs de eigenschapiteratie op naam.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="copy-limits"></a>Kopieerlimieten

De telling mag niet hoger zijn dan 800.

De telling kan geen negatief getal zijn. Als u een sjabloon implementeert met Azure PowerShell 2.6 of hoger, Azure CLI 2.0.74 of hoger of REST **API-versie 2019-05-10** of hoger, u het aantal instellen op nul. Eerdere versies van PowerShell, CLI en de REST API ondersteunen geen nul voor telling.

## <a name="example-templates"></a>Voorbeeldsjablonen

In het volgende voorbeeld wordt een algemeen scenario weergegeven voor het maken van meer dan één waarde voor een eigenschap.

|Template  |Beschrijving  |
|---------|---------|
|[VM-implementatie met een variabel aantal gegevensschijven](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Implementeert verschillende gegevensschijven met een virtuele machine. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Zelfstudie: meerdere resource-exemplaren maken met ARM-sjablonen](template-tutorial-create-multiple-instances.md)voor het doorlopen van een zelfstudie.
* Zie voor andere toepassingen van het kopieerelement:
  * [Resourceiteratie in ARM-sjablonen](copy-resources.md)
  * [Variabele iteratie in ARM-sjablonen](copy-variables.md)
  * [Uitvoeriteratie in ARM-sjablonen](copy-outputs.md)
* Zie [ARM-sjablonen ontwerpen](template-syntax.md)als u meer wilt weten over de secties van een sjabloon.
* Zie [Een toepassing implementeren met ARM-sjabloon](deploy-powershell.md)voor meer informatie over het implementeren van uw sjabloon.

