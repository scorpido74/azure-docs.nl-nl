---
title: Meerdere exemplaren van Azure-resources implementeren | Microsoft Docs
description: Gebruik kopieer bewerkingen en matrices in een Azure Resource Manager sjabloon om meerdere keren te herhalen bij het implementeren van resources.
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: b349576f5e9f5410afc29f48e40c38e12168252d
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258901"
---
# <a name="resource-property-or-variable-iteration-in-azure-resource-manager-templates"></a>Herhaling van resources, eigenschappen of variabelen in Azure Resource Manager sjablonen

In dit artikel wordt beschreven hoe u in uw Azure Resource Manager-sjabloon meer dan één exemplaar van een resource, variabele of eigenschap maakt. Als u meerdere exemplaren wilt maken, `copy` voegt u het object toe aan uw sjabloon.

Bij gebruik in combi natie met een resource heeft het object kopiëren de volgende indeling:

```json
"copy": {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "mode": "serial" <or> "parallel",
    "batchSize": <number-to-deploy-serially>
}
```

Bij gebruik met een variabele of eigenschap heeft het object kopiëren de volgende indeling:

```json
"copy": [
  {
      "name": "<name-of-loop>",
      "count": <number-of-iterations>,
      "input": <values-for-the-property-or-variable>
  }
]
```

Beide gebruiken worden uitgebreid beschreven in dit artikel. Zie [zelf studie: meerdere resource-instanties maken met behulp van Resource Manager-sjablonen](./resource-manager-tutorial-create-multiple-instances.md)voor een zelf studie.

Zie [voor waarde-element](conditional-resource-deployment.md)als u wilt opgeven of een resource helemaal moet worden geïmplementeerd.

## <a name="copy-limits"></a>Limieten kopiëren

Als u het aantal iteraties wilt opgeven, geeft u een waarde op voor de eigenschap Count. De telling mag niet groter zijn dan 800.

De telling kan geen negatief getal zijn. Als u een sjabloon implementeert met Azure PowerShell 2,6 of hoger of REST API versie **2019-05-10** of hoger, kunt u Count instellen op nul. Eerdere versies van Power shell en het REST API bieden geen ondersteuning voor aantal nul. Op dit moment ondersteunt Azure CLI geen nul voor Count, maar deze ondersteuning wordt toegevoegd in een toekomstige release.

Wees voorzichtig met het gebruik van de implementatie van de [volledige modus](deployment-modes.md) met Copy. Als u de volledige modus opnieuw implementeert naar een resource groep, worden alle resources verwijderd die niet zijn opgegeven in de sjabloon na het omzetten van de Kopieer bewerking.

De limieten voor het aantal zijn gelijk aan die van een resource, variabele of eigenschap.

## <a name="resource-iteration"></a>Resource herhaling

Als u tijdens de implementatie moet bepalen of u een of meer exemplaren van een resource wilt maken `copy` , voegt u een element toe aan het resource type. Geef in het element copy het aantal iteraties en een naam voor deze lus op.

De resource voor het maken van verschillende tijden heeft de volgende indeling:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ],
  "outputs": {}
}
```

U ziet dat de naam van elke resource de `copyIndex()` functie bevat, die de huidige iteratie in de lus retourneert. `copyIndex()` is gebaseerd op nul. Het volgende voor beeld:

```json
"name": "[concat('storage', copyIndex())]",
```

Hiermee maakt u deze namen:

* storage0
* storage1
* storage2.

Als u de indexwaarde wilt verschuiven, kunt u een waarde doorgeven in de functie copyIndex(). Het aantal iteraties is nog steeds opgegeven in het copy-element, maar de waarde van functie copyindex wordt gecompenseerd door de opgegeven waarde. Het volgende voor beeld:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Hiermee maakt u deze namen:

* storage1
* storage2
* storage3

De Kopieer bewerking is handig bij het werken met matrices, omdat u elk element in de matrix kunt door lopen. Gebruik de `length` functie op de matrix om het aantal voor herhalingen op te geven `copyIndex` en om de huidige index in de matrix op te halen. Het volgende voor beeld:

```json
"parameters": { 
  "org": { 
    "type": "array", 
    "defaultValue": [ 
      "contoso", 
      "fabrikam", 
      "coho" 
    ] 
  }
}, 
"resources": [ 
  { 
    "name": "[concat('storage', parameters('org')[copyIndex()])]", 
    "copy": { 
      "name": "storagecopy", 
      "count": "[length(parameters('org'))]" 
    }, 
    ...
  } 
]
```

Hiermee maakt u deze namen:

* storagecontoso
* storagefabrikam
* storagecoho

Resource Manager maakt standaard de resources parallel. Er geldt geen limiet voor het aantal resources dat parallel is geïmplementeerd, met uitzonde ring van de totale limiet van 800 resources in de sjabloon. De volg orde waarin ze worden gemaakt, is niet gegarandeerd.

Het is echter mogelijk dat u wilt opgeven dat de resources in de juiste volg orde worden geïmplementeerd. Wanneer u bijvoorbeeld een productie omgeving bijwerkt, wilt u mogelijk de updates spreiden zodat alleen een bepaald aantal tegelijk wordt bijgewerkt. Als u meer dan één exemplaar van een bron wilt implementeren, stelt `mode` u in op `batchSize` **serie** en op het aantal exemplaren dat tegelijk moet worden geïmplementeerd. Met de seriële modus maakt Resource Manager een afhankelijkheid van eerdere instanties in de lus, zodat deze geen batch Start totdat de vorige batch is voltooid.

Als u opslag accounts bijvoorbeeld twee keer tegelijk wilt implementeren, gebruikt u:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      }
    }
  ],
  "outputs": {}
}
```

De eigenschap mode kan ook **parallel**worden geaccepteerd. Dit is de standaard waarde.

Zie [using Copy](resource-group-linked-templates.md#using-copy)voor informatie over het gebruik van kopiëren met geneste sjablonen.

## <a name="property-iteration"></a>Eigenschaps herhaling

Als u meer dan één waarde voor een eigenschap voor een resource wilt maken, `copy` voegt u een matrix toe aan het element Properties. Deze matrix bevat objecten en elk object heeft de volgende eigenschappen:

* naam: de naam van de eigenschap voor het maken van verschillende waarden voor
* Count-het aantal waarden dat moet worden gemaakt.
* invoer-een object dat de waarden bevat die aan de eigenschap moeten worden toegewezen  

In het volgende voor beeld ziet u `copy` hoe u kunt Toep assen op de eigenschap data disks op een virtuele machine:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
        "name": "dataDisks",
        "count": 3,
        "input": {
          "lun": "[copyIndex('dataDisks')]",
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      }],
      ...
```

U ziet dat wanneer `copyIndex` u een eigenschaps herhaling gebruikt, de naam van de herhaling moet opgeven. U hoeft de naam niet op te geven wanneer u deze gebruikt met resource iteratie.

Resource Manager breidt de `copy` matrix uit tijdens de implementatie. De naam van de matrix wordt de naam van de eigenschap. De invoer waarden worden de object eigenschappen. De geïmplementeerde sjabloon wordt:

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
          "diskSizeGB": "1023"
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      ],
      ...
```

Het element Copy is een matrix, zodat u meer dan één eigenschap voor de resource kunt opgeven. Voeg een object toe voor elke eigenschap die u wilt maken.

```json
{
  "name": "string",
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
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
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "apiVersion": "2018-04-01",
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

## <a name="variable-iteration"></a>Variabele herhaling

Als u meerdere exemplaren van een variabele wilt maken, `copy` gebruikt u de eigenschap in de sectie variabelen. U maakt een matrix van elementen die zijn gemaakt op basis van `input` de waarde in de eigenschap. U kunt de `copy` eigenschap binnen een variabele of op het hoogste niveau van de sectie Varia bles gebruiken. Wanneer u `copyIndex` binnen een variabele herhaling gebruikt, moet u de naam van de herhaling opgeven.

Zie [matrix sjabloon kopiëren](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json)voor een eenvoudig voor beeld van het maken van een matrix van teken reeks waarden.

In het volgende voor beeld ziet u verschillende manieren om matrix variabelen te maken met dynamisch samengestelde elementen. Hierin ziet u hoe u een kopie binnen een variabele kunt gebruiken om matrices van objecten en teken reeksen te maken. U ziet ook hoe u met behulp van kopiëren op het hoogste niveau matrices van objecten, teken reeksen en gehele getallen maakt.

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

Het type variabele dat wordt gemaakt, is afhankelijk van het invoer object. De variabele met de naam **hoogste niveau-object-Matrix** in het vorige voor beeld resulteert bijvoorbeeld in:

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

En retourneert de variabele met de naam **teken reeks-matrix op het hoogste niveau** :

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

## <a name="depend-on-resources-in-a-loop"></a>Is afhankelijk van resources in een lus

U geeft aan dat een resource wordt geïmplementeerd na een andere resource met `dependsOn` behulp van het-element. Als u een resource wilt implementeren die afhankelijk is van de verzameling van resources in een lus, geeft u de naam van de Kopieer-lus op in het element dependsOn. In het volgende voor beeld ziet u hoe u drie opslag accounts implementeert voordat u de virtuele machine implementeert. De volledige definitie van de virtuele machine wordt niet weer gegeven. U ziet dat de naam van het element Copy `storagecopy` is ingesteld op en het dependsOn-element voor het virtual machines `storagecopy`ook is ingesteld op.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    },
    {
      "apiVersion": "2015-06-15", 
      "type": "Microsoft.Compute/virtualMachines", 
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

<a id="looping-on-a-nested-resource" />

## <a name="iteration-for-a-child-resource"></a>Herhaling voor een onderliggende resource
U kunt geen kopieer proces voor een onderliggende Resource gebruiken. Als u meer dan één exemplaar van een resource wilt maken die doorgaans wordt gedefinieerd als genest in een andere resource, moet u die resource in plaats daarvan maken als resource op het hoogste niveau. U definieert de relatie met de bovenliggende resource via de eigenschappen type en naam.

Stel bijvoorbeeld dat u een gegevensset definieert als een onderliggende bron in een data factory.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Als u meer dan één gegevensset wilt maken, verplaatst u deze buiten het data factory. De gegevensset moet op hetzelfde niveau zijn als de data factory, maar het is nog steeds een onderliggende resource van de data factory. U behoudt de relatie tussen de gegevensset en data factory via de eigenschappen type en naam. Omdat het type niet meer kan worden afgeleid van de positie in de sjabloon, moet u het volledig gekwalificeerde type opgeven in de notatie: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Als u een bovenliggende/onderliggende relatie met een exemplaar van de data factory wilt instellen, geeft u een naam op voor de gegevensset die de naam van de bovenliggende resource bevat. Gebruik de indeling: `{parent-resource-name}/{child-resource-name}`.  

In het volgende voor beeld ziet u de implementatie:

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

## <a name="example-templates"></a>Voorbeeldsjablonen

In de volgende voor beelden ziet u algemene scenario's voor het maken van meer dan één exemplaar van een resource of eigenschap.

|Template  |Description  |
|---------|---------|
|[Opslag kopiëren](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Hiermee worden meer dan één opslag account met een index nummer in de naam geïmplementeerd. |
|[Opslag van seriële kopieën](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Hiermee worden verschillende opslag accounts tegelijkertijd geïmplementeerd. De naam bevat het index nummer. |
|[Opslag kopiëren met een matrix](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Hiermee worden verschillende opslag accounts geïmplementeerd. De naam bevat een waarde uit een matrix. |
|[VM-implementatie met een variabele aantal gegevens schijven](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Hiermee worden verschillende gegevens schijven met een virtuele machine geïmplementeerd. |
|[Kopieer variabelen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Demonstreert de verschillende manieren om variabelen te herhalen. |
|[Meerdere beveiligings regels](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Implementeert diverse beveiligings regels voor een netwerk beveiligings groep. Hiermee worden de beveiligings regels van een para meter gemaakt. Zie [meerdere NSG-parameter bestanden](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)voor de para meter. |

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: meerdere resource-instanties maken met behulp van Resource Manager-sjablonen](./resource-manager-tutorial-create-multiple-instances.md)als u een zelf studie wilt door lopen.

* Zie [Azure Resource Manager sjablonen ontwerpen](resource-group-authoring-templates.md)voor meer informatie over de secties van een sjabloon.
* Zie [een toepassing implementeren met Azure Resource Manager sjabloon](resource-group-template-deploy.md)voor meer informatie over het implementeren van uw sjabloon.

