---
title: Sjabloonfuncties - implementatie
description: Beschrijft de functies die moeten worden gebruikt in een Azure Resource Manager-sjabloon om implementatiegegevens op te halen.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 86a1d3d7e05fedacd7a3c044ecab241ca9d059c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156324"
---
# <a name="deployment-functions-for-arm-templates"></a>Implementatiefuncties voor ARM-sjablonen 

Resourcebeheer biedt de volgende functies voor het verkrijgen van waarden die verband houden met de huidige implementatie van uw Azure Resource Manager-sjabloon (ARM):

* [implementatie](#deployment)
* [Milieu](#environment)
* [parameters](#parameters)
* [Variabelen](#variables)

Zie [Resourcefuncties](template-functions-resource.md)voor waarden uit resources, resourcegroepen of abonnementen.

## <a name="deployment"></a>implementatie

`deployment()`

Retourneert informatie over de huidige implementatiebewerking.

### <a name="return-value"></a>Retourwaarde

Met deze functie wordt het object geretourneerd dat wordt doorgegeven tijdens de implementatie. De eigenschappen in het geretourneerde object verschillen op basis van de vraag of het implementatieobject wordt doorgegeven als koppeling of als een in-line object. Wanneer het implementatieobject in de regel wordt doorgegeven, bijvoorbeeld wanneer u de parameter **-TemplateFile** in Azure PowerShell gebruikt om naar een lokaal bestand te wijzen, heeft het geretourneerde object de volgende indeling:

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

Wanneer het object als koppeling wordt doorgegeven, bijvoorbeeld wanneer u de parameter **-TemplateUri** gebruikt om naar een extern object te wijzen, wordt het object in de volgende indeling geretourneerd: 

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

Wanneer u [een Azure-abonnement implementeert](deploy-to-subscription.md)in plaats van `location` een resourcegroep, bevat het retourobject een eigenschap. De locatieeigenschap is opgenomen bij het implementeren van een lokale sjabloon of een externe sjabloon.

### <a name="remarks"></a>Opmerkingen

U deployment() gebruiken om een koppeling te maken naar een andere sjabloon op basis van de URI van de bovenliggende sjabloon.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Als u een sjabloon opnieuw implementeert uit de implementatiegeschiedenis in de portal, wordt de sjabloon geïmplementeerd als een lokaal bestand. De `templateLink` eigenschap wordt niet geretourneerd in de implementatiefunctie. Als de sjabloon `templateLink` is ingeschakeld om een koppeling naar een andere sjabloon te maken, gebruikt u de portal niet om opnieuw te worden geïmplementeerd. Gebruik in plaats daarvan de opdrachten die u hebt gebruikt om de sjabloon oorspronkelijk te implementeren.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) wordt het implementatieobject geretourneerd:

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

In het voorgaande voorbeeld wordt het volgende object geretourneerd:

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

Zie [de implementatiefunctie op abonnementsniveau](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json)voor een sjabloon op abonnementsniveau die de implementatiefunctie gebruikt. Het is ingezet met `az deployment create` `New-AzDeployment` een van beide of commando's.

## <a name="environment"></a>omgeving

`environment()`

Retourneert informatie over de Azure-omgeving die wordt gebruikt voor implementatie.

### <a name="return-value"></a>Retourwaarde

Met deze functie worden eigenschappen voor de huidige Azure-omgeving geretourneerd. In het volgende voorbeeld worden de eigenschappen voor globale Azure weergegeven. Soevereine wolken kunnen iets andere eigenschappen teruggeven.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>Voorbeeld

In de volgende voorbeeldsjabloon wordt het omgevingsobject geretourneerd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "environmentOutput": {
            "value": "[environment()]",
            "type" : "object"
        }
    }
}
```

In het voorgaande voorbeeld wordt het volgende object geretourneerd wanneer het wordt geïmplementeerd in het wereldwijde Azure:

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Geeft als resultaat een parameterwaarde. De opgegeven parameternaam moet worden gedefinieerd in de parameterssectie van de sjabloon.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| parameterName |Ja |tekenreeks |De naam van de parameter om terug te keren. |

### <a name="return-value"></a>Retourwaarde

De waarde van de opgegeven parameter.

### <a name="remarks"></a>Opmerkingen

Doorgaans gebruikt u parameters om resourcewaarden in te stellen. In het volgende voorbeeld wordt de naam van de website ingesteld op de parameterwaarde die tijdens de implementatie is doorgegeven.

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

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) wordt een vereenvoudigd gebruik van de functie parameters weergegeven.

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

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| tekenreeksUitvoer | Tekenreeks | optie 1 |
| intOutput | Int | 1 |
| objectOutput | Object | {"een": "a", "twee": "b"} |
| arrayOutput | Matrix | [1, 2, 3] |
| crossOutput | Tekenreeks | optie 1 |

Zie Parameters in de [sjabloon Azure Resource Manager](template-parameters.md)voor meer informatie over het gebruik van parameters.

## <a name="variables"></a>Variabelen

`variables(variableName)`

Geeft als resultaat de waarde van variabele. De opgegeven variabele naam moet worden gedefinieerd in de sectie variabelen van de sjabloon.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| variableName |Ja |Tekenreeks |De naam van de variabele om terug te keren. |

### <a name="return-value"></a>Retourwaarde

De waarde van de opgegeven variabele.

### <a name="remarks"></a>Opmerkingen

Doorgaans gebruikt u variabelen om uw sjabloon te vereenvoudigen door slechts één keer complexe waarden te construeren. In het volgende voorbeeld wordt een unieke naam voor een opslagaccount gemaakt.

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

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) worden verschillende variabele waarden geretourneerd.

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

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| voorbeeldOutput1 | Tekenreeks | myVariable myVariable |
| voorbeeldOutput2 | Matrix | [1, 2, 3, 4] |
| voorbeeldOutput3 | Tekenreeks | myVariable myVariable |
| voorbeeldOutput4 |  Object | {"property1": "value1", "property2": "value2"} |

Zie [Variabelen in de sjabloon Azure Resource Manager](template-variables.md)voor meer informatie over het gebruik van variabelen.

## <a name="next-steps"></a>Volgende stappen
* Zie [Sjablonen voor Azure Resource Manager ontwerpen](template-syntax.md)voor een beschrijving van de secties in een Azure Resource Manager-sjabloon.
* Zie [Gekoppelde sjablonen gebruiken met Azure Resource Manager](linked-templates.md)als u meerdere sjablonen wilt samenvoegen.
* Zie Meerdere exemplaren van resources maken [in Azure Resource Manager](copy-resources.md)als u een bepaald aantal keren wilt herhalen bij het maken van een type resource.
* Zie Een toepassing implementeren [met Azure Resource Manager-sjabloon](deploy-powershell.md)als u wilt zien hoe u de sjabloon implementeren die u hebt gemaakt.

