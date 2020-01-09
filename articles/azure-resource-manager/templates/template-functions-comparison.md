---
title: Sjabloon functies-vergelijking
description: Hierin worden de functies beschreven die in een Azure Resource Manager sjabloon kunnen worden gebruikt om waarden te vergelijken.
ms.topic: conceptual
ms.date: 09/05/2017
ms.openlocfilehash: 3f21066ae5882f51ef1e01343752eea725fece1d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484049"
---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>Vergelijkings functies voor Azure Resource Manager sjablonen

Resource Manager biedt verschillende functies voor het maken van vergelijkingen in uw sjablonen.

* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="equals"></a>equals
`equals(arg1, arg2)`

Hiermee wordt gecontroleerd of twee waarden gelijk zijn aan elkaar.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int, string, array of object |De eerste waarde die moet worden gecontroleerd op gelijkheid. |
| Arg2 |Ja |int, string, array of object |De tweede waarde om te controleren op gelijkheid. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de waarden gelijk zijn. anders **False**.

### <a name="remarks"></a>Opmerkingen

De functie equals wordt vaak gebruikt met het `condition`-element om te testen of een resource wordt geïmplementeerd.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

### <a name="example"></a>Voorbeeld

Met de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) worden verschillende soorten waarden voor gelijkheid gecontroleerd. Alle standaard waarden geven waar als resultaat.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| checkInts | Bool | Waar |
| checkStrings | Bool | Waar |
| checkArrays | Bool | Waar |
| checkObjects | Bool | Waar |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json 
```

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) gebruikt [niet](template-functions-logical.md#not) met **gelijk aan**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
}
```

De uitvoer van het vorige voor beeld is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| checkNotEquals | Bool | Waar |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json 
```

## <a name="greater"></a>greater
`greater(arg1, arg2)`

Controleert of de eerste waarde groter is dan de tweede waarde.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int of String |De eerste waarde voor de grotere vergelijking. |
| Arg2 |Ja |int of String |De tweede waarde voor de grotere vergelijking. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de eerste waarde groter is dan de tweede waarde; anders **False**.

### <a name="example"></a>Voorbeeld

Met de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) wordt gecontroleerd of de ene waarde groter is dan de andere.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| checkInts | Bool | Onwaar |
| checkStrings | Bool | Waar |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json 
```

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

Hiermee wordt gecontroleerd of de eerste waarde groter is dan of gelijk is aan de tweede waarde.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int of String |De eerste waarde voor de grotere of gelijk zijnde vergelijking. |
| Arg2 |Ja |int of String |De tweede waarde voor de groter of gelijke vergelijking. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de eerste waarde groter is dan of gelijk is aan de tweede waarde; anders **False**.

### <a name="example"></a>Voorbeeld

Met de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) wordt gecontroleerd of de ene waarde groter is dan of gelijk is aan de andere.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| checkInts | Bool | Onwaar |
| checkStrings | Bool | Waar |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json 
```

## <a name="less"></a>less
`less(arg1, arg2)`

Controleert of de eerste waarde lager is dan de tweede waarde.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int of String |De eerste waarde voor de minder vergelijkingen. |
| Arg2 |Ja |int of String |De tweede waarde voor de minder vergelijkingen. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de eerste waarde kleiner is dan de tweede waarde; anders **False**.

### <a name="example"></a>Voorbeeld

Met de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) wordt gecontroleerd of de ene waarde kleiner is dan de andere.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| checkInts | Bool | Waar |
| checkStrings | Bool | Onwaar |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json 
```

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

Hiermee wordt gecontroleerd of de eerste waarde kleiner is dan of gelijk is aan de tweede waarde.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int of String |De eerste waarde voor de vergelijking met minder of gelijk aan. |
| Arg2 |Ja |int of String |De tweede waarde voor de vergelijking met minder of gelijk aan. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de eerste waarde kleiner dan of gelijk aan de tweede waarde is; anders **False**.

### <a name="example"></a>Voorbeeld

Met de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) wordt gecontroleerd of de ene waarde kleiner is dan of gelijk is aan de andere.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| checkInts | Bool | Waar |
| checkStrings | Bool | Onwaar |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json 
```

## <a name="next-steps"></a>Volgende stappen
* Zie voor een beschrijving van de secties in een Azure Resource Manager-sjabloon, [Authoring Azure Resource Manager-sjablonen](template-syntax.md).
* U kunt meerdere sjablonen samenvoegen, Zie [gekoppelde sjablonen gebruiken met Azure Resource Manager](linked-templates.md).
* Op een opgegeven aantal keren herhalen bij het maken van een type resource, Zie [meerdere exemplaren van resources maken in Azure Resource Manager](create-multiple-instances.md).
* Zie [een toepassing implementeren met Azure Resource Manager sjabloon](deploy-powershell.md)voor meer informatie over het implementeren van de sjabloon die u hebt gemaakt.

