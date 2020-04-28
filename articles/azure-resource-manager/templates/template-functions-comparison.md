---
title: Sjabloon functies-vergelijking
description: Hierin worden de functies beschreven die in een Azure Resource Manager sjabloon kunnen worden gebruikt om waarden te vergelijken.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a9b7b32475695e5222b87c8fe75e8982f34ebb21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192328"
---
# <a name="comparison-functions-for-arm-templates"></a>Vergelijkings functies voor ARM-sjablonen

Resource Manager biedt verschillende functies voor het maken van vergelijkingen in uw Azure Resource Manager-sjablonen (ARM).

* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

## <a name="equals"></a>equals

`equals(arg1, arg2)`

Hiermee wordt gecontroleerd of twee waarden gelijk zijn aan elkaar.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |int, String, array of object |De eerste waarde die moet worden gecontroleerd op gelijkheid. |
| Arg2 |Ja |int, String, array of object |De tweede waarde om te controleren op gelijkheid. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de waarden gelijk zijn. anders **False**.

### <a name="remarks"></a>Opmerkingen

De functie equals wordt vaak gebruikt in combi `condition` natie met het element om te testen of een resource wordt geïmplementeerd.

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

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| checkInts | Booleaanse waarde | True |
| checkStrings | Booleaanse waarde | True |
| checkArrays | Booleaanse waarde | True |
| checkObjects | Booleaanse waarde | True |

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

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| checkNotEquals | Booleaanse waarde | True |

## <a name="greater"></a>greater

`greater(arg1, arg2)`

Controleert of de eerste waarde groter is dan de tweede waarde.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |int of String |De eerste waarde voor de grotere vergelijking. |
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

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| checkInts | Booleaanse waarde | False |
| checkStrings | Booleaanse waarde | True |

## <a name="greaterorequals"></a>greaterOrEquals

`greaterOrEquals(arg1, arg2)`

Hiermee wordt gecontroleerd of de eerste waarde groter is dan of gelijk is aan de tweede waarde.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |int of String |De eerste waarde voor de grotere of gelijk zijnde vergelijking. |
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

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| checkInts | Booleaanse waarde | False |
| checkStrings | Booleaanse waarde | True |

## <a name="less"></a>less

`less(arg1, arg2)`

Controleert of de eerste waarde lager is dan de tweede waarde.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |int of String |De eerste waarde voor de minder vergelijkingen. |
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

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| checkInts | Booleaanse waarde | True |
| checkStrings | Booleaanse waarde | False |

## <a name="lessorequals"></a>lessOrEquals

`lessOrEquals(arg1, arg2)`

Hiermee wordt gecontroleerd of de eerste waarde kleiner is dan of gelijk is aan de tweede waarde.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |int of String |De eerste waarde voor de vergelijking met minder of gelijk aan. |
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

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| checkInts | Booleaanse waarde | True |
| checkStrings | Booleaanse waarde | False |

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor een beschrijving van de secties in een Azure Resource Manager sjabloon.
