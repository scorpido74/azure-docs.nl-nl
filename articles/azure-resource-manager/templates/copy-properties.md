---
title: Meerdere exemplaren van een eigenschap definiëren
description: Gebruik een Kopieer bewerking in een Azure Resource Manager sjabloon om meerdere keren te herhalen bij het maken van een eigenschap in een resource.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: b759389cd1065c399658bd8d0c1ddd263054697c
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622867"
---
# <a name="property-iteration-in-azure-resource-manager-templates"></a>Eigenschaps herhaling in Azure Resource Manager sjablonen

In dit artikel wordt beschreven hoe u meer dan één exemplaar van een eigenschap in uw Azure Resource Manager sjabloon maakt. Door het element **copy** toe te voegen aan de sectie eigenschappen van een resource in uw sjabloon, kunt u het aantal items voor een eigenschap tijdens de implementatie dynamisch instellen. U hoeft ook geen sjabloon syntaxis te herhalen.

U kunt ook kopiëren met [resources](copy-resources.md), [variabelen](copy-variables.md)en [uitvoer](copy-outputs.md)gebruiken.

## <a name="property-iteration"></a>Eigenschaps herhaling

Het element Copy heeft de volgende algemene indeling:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Geef bij **naam**de naam op van de resource-eigenschap die u wilt maken. De eigenschap **Count** geeft het aantal iteraties op dat u voor de eigenschap wilt.

De eigenschap **input** geeft de eigenschappen aan die u wilt herhalen. U maakt een matrix van elementen die zijn gemaakt op basis van de waarde in de eigenschap **input** .

In het volgende voor beeld ziet u hoe u `copy` toepast op de eigenschap data disks op een virtuele machine:

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

Wanneer u `copyIndex` in een eigenschaps herhaling gebruikt, moet u de naam van de herhaling opgeven.

> [!NOTE]
> Eigenschaps herhaling ondersteunt ook een argument Offset. De offset moet worden opgegeven na de naam van de iteratie, zoals functie copyindex (' data disks ', 1).
>

Resource Manager breidt de `copy`-matrix uit tijdens de implementatie. De naam van de matrix wordt de naam van de eigenschap. De invoer waarden worden de object eigenschappen. De geïmplementeerde sjabloon wordt:

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

Het element Copy is een matrix, zodat u meer dan één eigenschap voor de resource kunt opgeven.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "examleLB",
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

U kunt resource en eigenschaps herhaling samen gebruiken. Verwijzing naar eigenschaps herhaling op naam.

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

## <a name="copy-limits"></a>Limieten kopiëren

De telling mag niet groter zijn dan 800.

De telling kan geen negatief getal zijn. Als u een sjabloon implementeert met Azure PowerShell 2,6 of hoger, Azure CLI 2.0.74 of hoger of REST API versie **2019-05-10** of hoger, kunt u Count instellen op nul. Eerdere versies van Power shell, CLI en de REST API bieden geen ondersteuning voor aantal nul.

## <a name="example-templates"></a>Voorbeeldsjablonen

In het volgende voor beeld ziet u een veelvoorkomend scenario voor het maken van meer dan één waarde voor een eigenschap.

|Template  |Beschrijving  |
|---------|---------|
|[VM-implementatie met een variabele aantal gegevens schijven](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Hiermee worden verschillende gegevens schijven met een virtuele machine geïmplementeerd. |

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: meerdere resource-instanties maken met behulp van Resource Manager-sjablonen](template-tutorial-create-multiple-instances.md)als u een zelf studie wilt door lopen.
* Zie voor andere toepassingen van het element copy:
  * [Resource iteratie in Azure Resource Manager sjablonen](copy-resources.md)
  * [Variabele herhaling in Azure Resource Manager sjablonen](copy-variables.md)
  * [Uitvoer herhaling in Azure Resource Manager sjablonen](copy-outputs.md)
* Zie [Azure Resource Manager sjablonen ontwerpen](template-syntax.md)voor meer informatie over de secties van een sjabloon.
* Zie [een toepassing implementeren met Azure Resource Manager sjabloon](deploy-powershell.md)voor meer informatie over het implementeren van uw sjabloon.

