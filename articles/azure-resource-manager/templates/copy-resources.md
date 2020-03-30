---
title: Meerdere exemplaren van resources implementeren
description: Gebruik kopieerbewerking en arrays in een Azure Resource Manager-sjabloon om het type resource vaak te implementeren.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: e65ab93c21daffa0053e53d953fe95fa9f28e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153315"
---
# <a name="resource-iteration-in-arm-templates"></a>Resourceiteratie in ARM-sjablonen

In dit artikel ziet u hoe u meer dan één instantie van een resource maakt in uw Azure Resource Manager-sjabloon (ARM). Door het **kopieerelement** toe te voegen aan het gedeelte resources van uw sjabloon, u dynamisch instellen hoeveel resources moeten worden geïmplementeerd. U hoeft ook niet de syntaxis van de sjabloon te herhalen.

U ook kopiëren met [eigenschappen,](copy-properties.md) [variabelen](copy-variables.md) en [uitvoer gebruiken.](copy-outputs.md)

Zie [voorwaardeelement](conditional-resource-deployment.md)als u wilt opgeven of een resource überhaupt is geïmplementeerd.

## <a name="resource-iteration"></a>Resourceiteratie

Het kopieerelement heeft de volgende algemene indeling:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

De **eigenschap naam** is een waarde die de lus identificeert. De eigenschap **aantal** geeft het gewenste aantal iteraties op voor het resourcetype.

Gebruik de **eigenschappen modus** en **batchGrootte** om op te geven of de resources parallel of in volgorde worden geïmplementeerd. Deze eigenschappen worden beschreven in [Serial of Parallel](#serial-or-parallel).

In het volgende voorbeeld wordt het aantal opslagaccounts geopperd dat is opgegeven in de parameter **StorageCount.**

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

Merk op dat de naam `copyIndex()` van elke resource de functie bevat, die de huidige iteratie in de lus retourneert. `copyIndex()` is gebaseerd op nul. Dus, het volgende voorbeeld:

```json
"name": "[concat('storage', copyIndex())]",
```

Hiermee maakt u de volgende namen:

* opslag0
* opslag1
* opslag2.

Als u de indexwaarde wilt verschuiven, kunt u een waarde doorgeven in de functie copyIndex(). Het aantal iteraties wordt nog steeds opgegeven in het kopieerelement, maar de waarde van copyIndex wordt gecompenseerd door de opgegeven waarde. Dus, het volgende voorbeeld:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Hiermee maakt u de volgende namen:

* opslag1
* opslag2
* opslag3

De kopieerbewerking is handig bij het werken met arrays, omdat u elk element in de array herhalen. Gebruik `length` de functie op de array om het `copyIndex` aantal iteraties op te geven en de huidige index in de array op te halen.

In het volgende voorbeeld wordt één opslagaccount gemaakt voor elke naam in de parameter.

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

Als u waarden uit de geïmplementeerde resources wilt [retourneren,](copy-outputs.md)u kopiëren gebruiken in de sectie uitvoer.

## <a name="serial-or-parallel"></a>Seriële of parallel

Resourcemanager maakt standaard de resources parallel. Het past geen limiet toe op het aantal resources dat parallel wordt geïmplementeerd, met elkaar anders dan de totale limiet van 800 resources in de sjabloon. De volgorde waarin ze zijn gemaakt is niet gegarandeerd.

U echter wel opgeven dat de resources achter elkaar worden geïmplementeerd. Wanneer u bijvoorbeeld een productieomgeving bijwerkt, u de updates spreiden, zodat slechts een bepaald aantal op een bepaald moment wordt bijgewerkt. Als u meer dan één instantie van `mode` een `batchSize` resource op serie wilt implementeren, stelt u in op **serieen** en op het aantal exemplaren dat tegelijkertijd moet worden geïmplementeerd. Met de seriële modus maakt Resource Manager een afhankelijkheid van eerdere exemplaren in de lus, zodat deze niet één batch start totdat de vorige batch is voltooid.

Als u bijvoorbeeld opslagaccounts twee tegelijk implementeren, gebruikt u het als volgt:

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

De eigenschap modus accepteert ook **parallelle**, dat is de standaardwaarde.

## <a name="depend-on-resources-in-a-loop"></a>Afhankelijk van resources in een lus

U geeft op dat een resource na `dependsOn` een andere resource wordt geïmplementeerd met behulp van het element. Als u een resource wilt implementeren die afhankelijk is van het verzamelen van resources in een lus, geeft u de naam van de kopieerlus op in het element dependsOn. In het volgende voorbeeld ziet u hoe u drie opslagaccounts implementeert voordat u de virtuele machine implementeert. De volledige definitie van virtuele machines wordt niet weergegeven. Merk op dat het kopieerelement de naam heeft ingesteld op `storagecopy` en `storagecopy`het element dependsOn voor de virtuele machine is ook ingesteld op .

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

## <a name="iteration-for-a-child-resource"></a>Iteratie voor een onderliggende bron

U geen kopieerlus gebruiken voor een onderliggende bron. Als u meer dan één instantie wilt maken van een resource die u doorgaans definieert als genest binnen een andere resource, moet u die resource in plaats daarvan maken als een bron op het hoogste niveau. U definieert de relatie met de bovenliggende resource via de eigenschappen type en naam.

Stel dat u een gegevensset doorgaans definieert als een onderliggende bron in een gegevensfabriek.

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

Als u meer dan één gegevensset wilt maken, verplaatst u deze buiten de gegevensfabriek. De gegevensset moet op hetzelfde niveau zijn als de gegevensfabriek, maar het is nog steeds een onderliggende bron van de gegevensfabriek. U behoudt de relatie tussen gegevensset en gegevensfabriek via de eigenschappen type en naam. Aangezien tekst niet langer kan worden afgeleid uit de positie in de sjabloon, `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`moet u het volledig gekwalificeerde type in de indeling opgeven: .

Als u een bovenliggende/onderliggende relatie wilt opbouwen met een instantie van de gegevensfabriek, geeft u een naam op voor de gegevensset die de naam van de bovenliggende bron bevat. Gebruik de notatie `{parent-resource-name}/{child-resource-name}`.

In het volgende voorbeeld ziet u de implementatie:

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

## <a name="copy-limits"></a>Kopieerlimieten

De telling mag niet hoger zijn dan 800.

De telling kan geen negatief getal zijn. Als u een sjabloon implementeert met Azure PowerShell 2.6 of hoger, Azure CLI 2.0.74 of hoger of REST **API-versie 2019-05-10** of hoger, u het aantal instellen op nul. Eerdere versies van PowerShell, CLI en de REST API ondersteunen geen nul voor telling.

Wees voorzichtig met het gebruik van [de volledige modus implementatie](deployment-modes.md) met kopiëren. Als u de volledige modus opnieuw implementeert in een resourcegroep, worden alle resources die niet in de sjabloon zijn opgegeven nadat de kopieerlus is opgelost, verwijderd.

## <a name="example-templates"></a>Voorbeeldsjablonen

In de volgende voorbeelden worden veelvoorkomende scenario's weergegeven voor het maken van meer dan één instantie van een resource of eigenschap.

|Template  |Beschrijving  |
|---------|---------|
|[Opslag kopiëren](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Hiermee implementeert u meer dan één opslagaccount met een indexnummer in de naam. |
|[Opslag van seriële kopieën](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Implementeert meerdere opslagaccounts één voor één. De naam bevat het indexnummer. |
|[Opslag kopiëren met array](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Implementeert verschillende opslagaccounts. De naam bevat een waarde van een array. |
|[VM-implementatie met een variabel aantal gegevensschijven](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Implementeert verschillende gegevensschijven met een virtuele machine. |
|[Meerdere beveiligingsregels](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Implementeert verschillende beveiligingsregels naar een netwerkbeveiligingsgroep. Het construeert de beveiligingsregels van een parameter. Zie [meerdere NSG-parameterbestand](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)voor de parameter . |

## <a name="next-steps"></a>Volgende stappen

* Zie [Zelfstudie: meerdere resource-exemplaren maken met ARM-sjablonen](template-tutorial-create-multiple-instances.md)voor het doorlopen van een zelfstudie.
* Zie voor andere toepassingen van het kopieerelement:
  * [Eigenschapiteratie in ARM-sjablonen](copy-properties.md)
  * [Variabele iteratie in ARM-sjablonen](copy-variables.md)
  * [Uitvoeriteratie in ARM-sjablonen](copy-outputs.md)
* Zie [Kopiëren](linked-templates.md#using-copy)gebruiken voor informatie over het gebruik van kopiëren met geneste sjablonen.
* Zie [ARM-sjablonen ontwerpen](template-syntax.md)als u meer wilt weten over de secties van een sjabloon.
* Zie [Een toepassing implementeren met ARM-sjabloon](deploy-powershell.md)voor meer informatie over het implementeren van uw sjabloon.

