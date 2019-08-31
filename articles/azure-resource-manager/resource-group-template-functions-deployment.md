---
title: Azure Resource Manager-sjabloon functies-implementatie | Microsoft Docs
description: Hierin worden de functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon om implementatie gegevens op te halen.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: tomfitz
ms.openlocfilehash: 236fbb9e4ed3283ecf9147e6eb5033fb906a127b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194352"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Implementatie functies voor Azure Resource Manager sjablonen 

Resource Manager biedt de volgende functies voor het ophalen van waarden uit secties van de sjabloon en waarden die betrekking hebben op de implementatie:

* [inhoudsdistributiepad](#deployment)
* [parameters](#parameters)
* [variabelen](#variables)

Zie [resource functies](resource-group-template-functions-resource.md)om waarden van resources, resource groepen of abonnementen op te halen.

<a id="deployment" />

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment"></a>deployment
`deployment()`

Retourneert informatie over de huidige implementatie bewerking.

### <a name="return-value"></a>Retourwaarde

Deze functie retourneert het object dat tijdens de implementatie wordt door gegeven. De eigenschappen in het geretourneerde object wijken af van de vraag of het implementatie object wordt door gegeven als een koppeling of als een regel object. Wanneer het implementatie object in-line wordt door gegeven, bijvoorbeeld wanneer u de para meter **-TemplateFile** gebruikt in azure PowerShell om naar een lokaal bestand te verwijzen, heeft het geretourneerde object de volgende indeling:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Wanneer het object als een koppeling wordt door gegeven, bijvoorbeeld wanneer de para meter **-TemplateUri** wordt gebruikt om naar een extern object te verwijzen, wordt het object geretourneerd met de volgende indeling: 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Wanneer u [implementeert in een Azure-abonnement](deploy-to-subscription.md)in plaats van een resource groep, bevat het retour `location` object een eigenschap. De locatie-eigenschap wordt opgenomen bij het implementeren van ofwel een lokale sjabloon of een externe sjabloon.

### <a name="remarks"></a>Opmerkingen

U kunt implementatie () gebruiken om een koppeling naar een andere sjabloon te maken op basis van de URI van de bovenliggende sjabloon.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Als u een sjabloon opnieuw implementeert vanuit de implementatie geschiedenis in de portal, wordt de sjabloon geïmplementeerd als een lokaal bestand. De `templateLink` eigenschap wordt niet geretourneerd in de implementatie functie. Als uw sjabloon afhankelijk is van `templateLink` het samen stellen van een koppeling naar een andere sjabloon, gebruikt u de portal niet om opnieuw te implementeren. Gebruik in plaats daarvan de opdrachten die u hebt gebruikt om de sjabloon oorspronkelijk te implementeren.

### <a name="example"></a>Voorbeeld

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) retourneert het implementatie object:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

In het voor gaande voor beeld wordt het volgende object geretourneerd:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

Zie [implementatie functie voor abonnementen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json)voor een sjabloon op abonnements niveau die gebruikmaakt van de implementatie functie. Het wordt geïmplementeerd met een `az deployment create` of `New-AzDeployment` meer opdrachten.

<a id="parameters" />

## <a name="parameters"></a>parameters
`parameters(parameterName)`

Retourneert een parameter waarde. De opgegeven parameter naam moet worden gedefinieerd in de sectie para meters van de sjabloon.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| parameterName |Ja |string |De naam van de para meter die moet worden geretourneerd. |

### <a name="return-value"></a>Retourwaarde

De waarde van de opgegeven para meter.

### <a name="remarks"></a>Opmerkingen

Normaal gesp roken gebruikt u para meters om resource waarden in te stellen. In het volgende voor beeld wordt de naam van de website ingesteld op de parameter waarde die tijdens de implementatie wordt door gegeven.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>Voorbeeld

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) toont een vereenvoudigd gebruik van de functie para meters.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Value |
| ---- | ---- | ----- |
| stringOutput | Tekenreeks | optie 1 |
| intOutput | Int | 1 |
| objectOutput | Object | {"een": "a", "twee": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | Tekenreeks | optie 1 |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

<a id="variables" />

## <a name="variables"></a>variabelen
`variables(variableName)`

Retourneert de waarde van variable. De opgegeven naam van de variabele moet worden gedefinieerd in de sectie Varia bles van de sjabloon.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| variableName |Ja |Tekenreeks |De naam van de variabele die moet worden geretourneerd. |

### <a name="return-value"></a>Retourwaarde

De waarde van de opgegeven variabele.

### <a name="remarks"></a>Opmerkingen

Normaal gesp roken gebruikt u variabelen om uw sjabloon te vereenvoudigen door complexere waarden slechts één keer te maken. In het volgende voor beeld wordt een unieke naam voor een opslag account gemaakt.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>Voorbeeld

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) retourneert verschillende variabele waarden.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Value |
| ---- | ---- | ----- |
| exampleOutput1 | Tekenreeks | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | Tekenreeks | myVariable |
| exampleOutput4 |  Object | {"property1": "value1", "property2": "value2"} |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

## <a name="next-steps"></a>Volgende stappen
* Zie voor een beschrijving van de secties in een Azure Resource Manager-sjabloon, [Authoring Azure Resource Manager-sjablonen](resource-group-authoring-templates.md).
* Zie [gekoppelde sjablonen gebruiken met Azure Resource Manager](resource-group-linked-templates.md)om meerdere sjablonen samen te voegen.
* Op een opgegeven aantal keren herhalen bij het maken van een type resource, Zie [meerdere exemplaren van resources maken in Azure Resource Manager](resource-group-create-multiple.md).
* Zie voor meer informatie over het implementeren van de sjabloon die u hebt gemaakt, [een toepassing implementeren met Azure Resource Manager-sjabloon](resource-group-template-deploy.md).

