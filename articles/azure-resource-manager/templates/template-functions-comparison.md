---
title: Sjabloonfuncties - vergelijking
description: Beschrijft de functies die u moet gebruiken in een Azure Resource Manager-sjabloon om waarden te vergelijken.
ms.topic: conceptual
ms.date: 09/05/2017
ms.openlocfilehash: 42009e8543e307f2d3e4643ddaa79f492f9bdfee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156358"
---
# <a name="comparison-functions-for-arm-templates"></a>Vergelijkingsfuncties voor ARM-sjablonen

Resource Manager biedt verschillende functies voor het maken van vergelijkingen in uw Azure Resource Manager -sjablonen (ARM).

* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="equals"></a>equals
`equals(arg1, arg2)`

Hiermee wordt gecontroleerd of twee waarden met elkaar overeenkomen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int, tekenreeks, array of object |De eerste waarde om te controleren op gelijkheid. |
| arg2 |Ja |int, tekenreeks, array of object |De tweede waarde om te controleren op gelijkheid. |

### <a name="return-value"></a>Retourwaarde

**Geeft als** resultaat True als de waarden gelijk zijn; anders, **False**.

### <a name="remarks"></a>Opmerkingen

De functie gelijkisten `condition` wordt vaak gebruikt met het element om te testen of een resource wordt geïmplementeerd.

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

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) worden verschillende typen waarden gecontroleerd op gelijkheid. Alle standaardwaarden geven True terug.

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

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| checkInts | Booleaanse waarde | True |
| checkStrings | Booleaanse waarde | True |
| checkArrays | Booleaanse waarde | True |
| controlerenObjecten | Booleaanse waarde | True |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) wordt [niet](template-functions-logical.md#not) met **gelijken gebruikt.**

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

De uitvoer uit het voorgaande voorbeeld is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| checkNotEquals | Booleaanse waarde | True |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

## <a name="greater"></a>greater
`greater(arg1, arg2)`

Hiermee wordt gecontroleerd of de eerste waarde groter is dan de tweede waarde.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int of tekenreeks |De eerste waarde voor de grotere vergelijking. |
| arg2 |Ja |int of tekenreeks |De tweede waarde voor de grotere vergelijking. |

### <a name="return-value"></a>Retourwaarde

Geeft als resultaat **True** als de eerste waarde groter is dan de tweede waarde; anders, **False**.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) wordt gecontroleerd of de ene waarde groter is dan de andere.

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

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| checkInts | Booleaanse waarde | False |
| checkStrings | Booleaanse waarde | True |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

Hiermee wordt gecontroleerd of de eerste waarde groter is dan of gelijk is aan de tweede waarde.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int of tekenreeks |De eerste waarde voor de grotere of gelijke vergelijking. |
| arg2 |Ja |int of tekenreeks |De tweede waarde voor de grotere of gelijke vergelijking. |

### <a name="return-value"></a>Retourwaarde

**Geeft als** resultaat True als de eerste waarde groter is dan of gelijk is aan de tweede waarde; anders, **False**.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) wordt gecontroleerd of de ene waarde groter is dan of gelijk is aan de andere.

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

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| checkInts | Booleaanse waarde | False |
| checkStrings | Booleaanse waarde | True |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

## <a name="less"></a>less
`less(arg1, arg2)`

Hiermee wordt gecontroleerd of de eerste waarde kleiner is dan de tweede waarde.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int of tekenreeks |De eerste waarde voor de minder vergelijking. |
| arg2 |Ja |int of tekenreeks |De tweede waarde voor de minder vergelijking. |

### <a name="return-value"></a>Retourwaarde

**Geeft als** resultaat True als de eerste waarde kleiner is dan de tweede waarde; anders, **False**.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) wordt gecontroleerd of de ene waarde kleiner is dan de andere.

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

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| checkInts | Booleaanse waarde | True |
| checkStrings | Booleaanse waarde | False |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

Hiermee wordt gecontroleerd of de eerste waarde kleiner is dan of gelijk is aan de tweede waarde.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int of tekenreeks |De eerste waarde voor de minder of is gelijk aan vergelijking. |
| arg2 |Ja |int of tekenreeks |De tweede waarde voor de minder of is gelijk aan vergelijking. |

### <a name="return-value"></a>Retourwaarde

**Geeft als** resultaat True als de eerste waarde kleiner is dan of gelijk is aan de tweede waarde; anders, **False**.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) wordt gecontroleerd of de ene waarde kleiner is dan of gelijk is aan de andere.

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

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| checkInts | Booleaanse waarde | True |
| checkStrings | Booleaanse waarde | False |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

## <a name="next-steps"></a>Volgende stappen
* Zie [Sjablonen voor Azure Resource Manager ontwerpen](template-syntax.md)voor een beschrijving van de secties in een Azure Resource Manager-sjabloon.
* Zie [Gekoppelde sjablonen gebruiken met Azure Resource Manager](linked-templates.md)als u meerdere sjablonen wilt samenvoegen.
* Zie Meerdere exemplaren van resources maken [in Azure Resource Manager](copy-resources.md)als u een bepaald aantal keren wilt herhalen bij het maken van een type resource.
* Zie Een toepassing implementeren met Azure [Resource Manager-sjabloon](deploy-powershell.md)als u wilt zien hoe u de sjabloon implementeren die u hebt gemaakt.

