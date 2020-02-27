---
title: Meerdere exemplaren van bronnen implementeren
description: Gebruik kopieer bewerkingen en matrices in een Azure Resource Manager sjabloon om het resource type meermaals te implementeren.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: e90673504ceaccdc25a477e856defa77eed37d86
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620222"
---
# <a name="resource-iteration-in-azure-resource-manager-templates"></a>Resource iteratie in Azure Resource Manager sjablonen

In dit artikel wordt beschreven hoe u meer dan één exemplaar van een resource in uw Azure Resource Manager-sjabloon maakt. Door het element **kopiëren** toe te voegen aan de sectie resources van uw sjabloon, kunt u het aantal resources dat moet worden geïmplementeerd, dynamisch instellen. U hoeft ook geen sjabloon syntaxis te herhalen.

U kunt ook kopiëren met [Eigenschappen](copy-properties.md), [variabelen](copy-variables.md) en [uitvoer](copy-outputs.md)gebruiken.

Zie [voor waarde-element](conditional-resource-deployment.md)als u wilt opgeven of een resource helemaal moet worden geïmplementeerd.

## <a name="resource-iteration"></a>Resource herhaling

Het element Copy heeft de volgende algemene indeling:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

De eigenschap **name** is een wille keurige waarde die de lus identificeert. De eigenschap **Count** geeft het aantal iteraties op dat u voor het resource type wilt.

Gebruik de eigenschappen **mode** en **BatchSize** om op te geven of de resources parallel of sequentieel worden geïmplementeerd. Deze eigenschappen worden beschreven in [serieel of parallel](#serial-or-parallel).

In het volgende voor beeld wordt het aantal opslag accounts gemaakt dat is opgegeven in de para meter **storageCount** .

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
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
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
    "outputs": {}
}
```

U ziet dat de naam van elke resource de `copyIndex()` functie bevat, waarmee de huidige iteratie in de lus wordt geretourneerd. `copyIndex()` is gebaseerd op nul. Het volgende voor beeld:

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

De Kopieer bewerking is handig bij het werken met matrices, omdat u elk element in de matrix kunt door lopen. Gebruik de functie `length` op de matrix om het aantal voor herhalingen op te geven en `copyIndex` om de huidige index in de matrix op te halen.

In het volgende voor beeld wordt één opslag account gemaakt voor elke naam die in de para meter wordt gegeven.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
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
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

Als u waarden van de geïmplementeerde resources wilt retour neren, kunt u [in de sectie outputs de Kopieer versie](copy-outputs.md)gebruiken.

## <a name="serial-or-parallel"></a>Serieel of parallel

Resource Manager maakt standaard de resources parallel. Er geldt geen limiet voor het aantal resources dat parallel is geïmplementeerd, met uitzonde ring van de totale limiet van 800 resources in de sjabloon. De volg orde waarin ze worden gemaakt, is niet gegarandeerd.

Het is echter mogelijk dat u wilt opgeven dat de resources in de juiste volg orde worden geïmplementeerd. Wanneer u bijvoorbeeld een productie omgeving bijwerkt, wilt u mogelijk de updates spreiden zodat alleen een bepaald aantal tegelijk wordt bijgewerkt. Als u meer dan één exemplaar van een resource op een seriële schaal wilt implementeren, stelt u `mode` in op **serie** -en `batchSize` tot het aantal exemplaren dat tegelijk moet worden geïmplementeerd. Met de seriële modus maakt Resource Manager een afhankelijkheid van eerdere instanties in de lus, zodat deze geen batch Start totdat de vorige batch is voltooid.

Als u opslag accounts bijvoorbeeld twee keer tegelijk wilt implementeren, gebruikt u:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

De eigenschap mode kan ook **parallel**worden geaccepteerd. Dit is de standaard waarde.

## <a name="depend-on-resources-in-a-loop"></a>Is afhankelijk van resources in een lus

U geeft aan dat een resource wordt geïmplementeerd na een andere resource met behulp van het `dependsOn`-element. Als u een resource wilt implementeren die afhankelijk is van de verzameling van resources in een lus, geeft u de naam van de Kopieer-lus op in het element dependsOn. In het volgende voor beeld ziet u hoe u drie opslag accounts implementeert voordat u de virtuele machine implementeert. De volledige definitie van de virtuele machine wordt niet weer gegeven. U ziet dat de naam van het element Copy is ingesteld op `storagecopy` en dat het element dependsOn voor de virtuele machine ook is ingesteld op `storagecopy`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

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

## <a name="copy-limits"></a>Limieten kopiëren

De telling mag niet groter zijn dan 800.

De telling kan geen negatief getal zijn. Als u een sjabloon implementeert met Azure PowerShell 2,6 of hoger, Azure CLI 2.0.74 of hoger of REST API versie **2019-05-10** of hoger, kunt u Count instellen op nul. Eerdere versies van Power shell, CLI en de REST API bieden geen ondersteuning voor aantal nul.

Wees voorzichtig met het gebruik van de implementatie van de [volledige modus](deployment-modes.md) met Copy. Als u de volledige modus opnieuw implementeert naar een resource groep, worden alle resources verwijderd die niet zijn opgegeven in de sjabloon na het omzetten van de Kopieer bewerking.

## <a name="example-templates"></a>Voorbeeldsjablonen

In de volgende voor beelden ziet u algemene scenario's voor het maken van meer dan één exemplaar van een resource of eigenschap.

|Template  |Beschrijving  |
|---------|---------|
|[Opslag kopiëren](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Hiermee worden meer dan één opslag account met een index nummer in de naam geïmplementeerd. |
|[Opslag van seriële kopieën](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Hiermee worden verschillende opslag accounts tegelijkertijd geïmplementeerd. De naam bevat het index nummer. |
|[Opslag kopiëren met een matrix](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Hiermee worden verschillende opslag accounts geïmplementeerd. De naam bevat een waarde uit een matrix. |
|[VM-implementatie met een variabele aantal gegevens schijven](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Hiermee worden verschillende gegevens schijven met een virtuele machine geïmplementeerd. |
|[Meerdere beveiligings regels](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Implementeert diverse beveiligings regels voor een netwerk beveiligings groep. Hiermee worden de beveiligings regels van een para meter gemaakt. Zie [meerdere NSG-parameter bestanden](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)voor de para meter. |

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: meerdere resource-instanties maken met behulp van Resource Manager-sjablonen](template-tutorial-create-multiple-instances.md)als u een zelf studie wilt door lopen.
* Zie voor andere toepassingen van het element copy:
  * [Eigenschaps herhaling in Azure Resource Manager sjablonen](copy-properties.md)
  * [Variabele herhaling in Azure Resource Manager sjablonen](copy-variables.md)
  * [Uitvoer herhaling in Azure Resource Manager sjablonen](copy-outputs.md)
* Zie [using Copy](linked-templates.md#using-copy)voor informatie over het gebruik van kopiëren met geneste sjablonen.
* Zie [Azure Resource Manager sjablonen ontwerpen](template-syntax.md)voor meer informatie over de secties van een sjabloon.
* Zie [een toepassing implementeren met Azure Resource Manager sjabloon](deploy-powershell.md)voor meer informatie over het implementeren van uw sjabloon.

