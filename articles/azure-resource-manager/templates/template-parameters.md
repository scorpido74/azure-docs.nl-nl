---
title: Parameters in sjablonen
description: Beschrijft hoe u parameters definieert in een Azure Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 89c6984c587e8dae59c1825a99d4f8da1c06dafb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122420"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Parameters in Azure Resource Manager-sjablonen

In dit artikel wordt beschreven hoe u parameters definieert en gebruikt in uw Azure Resource Manager-sjabloon. Door verschillende waarden voor parameters op te geven, u een sjabloon opnieuw gebruiken voor verschillende omgevingen.

Resourcebeheer lost parameterwaarden op voordat de implementatiebewerkingen worden gestart. Waar de parameter in de sjabloon wordt gebruikt, vervangt Resource Manager deze door de opgeloste waarde.

## <a name="define-parameter"></a>Parameter definiëren

In het volgende voorbeeld ziet u een eenvoudige parameterdefinitie. Het definieert een parameter met de naam **storageSKU**. De parameter is een tekenreekswaarde en accepteert alleen waarden die geldig zijn voor het beoogde gebruik. De parameter gebruikt een standaardwaarde wanneer er geen waarde wordt opgegeven tijdens de implementatie.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }
}
```

## <a name="use-parameter"></a>Parameter gebruiken

In de sjabloon verwijst u naar de waarde voor de parameter met behulp van de functie [Parameters.](template-functions-deployment.md#parameters) In het volgende voorbeeld wordt de parameterwaarde gebruikt om SKU in te stellen voor het opslagaccount.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="template-functions"></a>Sjabloonfuncties

Wanneer u de standaardwaarde voor een parameter opgeeft, u de meeste sjabloonfuncties gebruiken. U een andere parameterwaarde gebruiken om een standaardwaarde te maken. In de volgende sjabloon wordt het gebruik van functies in de standaardwaarde weergegeven. Wanneer er geen naam is opgegeven voor de site, wordt een unieke tekenreekswaarde toegevoegd en wordt deze toegevoegd aan **de site.** Wanneer er geen naam is opgegeven voor het hostplan, wordt de waarde voor de site nodig en wordt **het plan toegevoegd**.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

U de [referentiefunctie](template-functions-resource.md#reference) of een van de [lijstfuncties](template-functions-resource.md#list) in de sectie parameters niet gebruiken. Deze functies krijgen de runtime-status van een resource en kunnen niet worden uitgevoerd voordat de implementatie wordt uitgevoerd wanneer parameters zijn opgelost.

## <a name="objects-as-parameters"></a>Objecten als parameters

Het kan gemakkelijker zijn om gerelateerde waarden te organiseren door ze als object door te geven. Deze aanpak vermindert ook het aantal parameters in de sjabloon.

In het volgende voorbeeld wordt een parameter weergegeven die een object is. De standaardwaarde toont de verwachte eigenschappen voor het object.

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

U verwijst naar de eigenschappen van het object met behulp van de stipoperator.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2015-06-15",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="example-templates"></a>Voorbeeldsjablonen

In de volgende voorbeelden worden scenario's gedemonstreerd voor het gebruik van parameters.

|Template  |Beschrijving  |
|---------|---------|
|[parameters met functies voor standaardwaarden](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Laat zien hoe u sjabloonfuncties gebruikt bij het definiëren van standaardwaarden voor parameters. De sjabloon implementeert geen resources. Het construeert parameterwaarden en retourneert deze waarden. |
|[parameterobject](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Toont het gebruik van een object voor een parameter aan. De sjabloon implementeert geen resources. Het construeert parameterwaarden en retourneert deze waarden. |


## <a name="next-steps"></a>Volgende stappen

* Zie [De structuur en syntaxis van Azure Resource Manager-sjablonen begrijpen](template-syntax.md)voor meer informatie over de beschikbare eigenschappen voor parameters.
* Zie [Resourcebeheer-parameterbestand maken](parameter-files.md)voor meer informatie over het doorgeven van parameterwaarden als bestand.
* Zie [Aanbevolen procedures - parameters](template-best-practices.md#parameters)voor aanbevelingen over het maken van parameters .
