---
title: Sjabloon functies-vergelijking
description: Hierin worden de functies beschreven die in een Azure Resource Manager sjabloon kunnen worden gebruikt om waarden te vergelijken.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 01d66f43cf73dcc9228118db5a9b6149b19ee66d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84677828"
---
# <a name="comparison-functions-for-arm-templates"></a>Vergelijkings functies voor ARM-sjablonen

Resource Manager biedt verschillende functies voor het maken van vergelijkingen in uw Azure Resource Manager-sjablonen (ARM).

* [Voeg](#coalesce)
* [is gelijk aan](#equals)
* [groter](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

## <a name="coalesce"></a>Voeg

`coalesce(arg1, arg2, arg3, ...)`

Retourneert de eerste waarde die niet null is van de para meters. Lege teken reeksen, lege matrices en lege objecten zijn niet null.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| Arg1 |Yes |int, String, array of object |De eerste waarde die moet worden getest op null. |
| aanvullende argumenten |No |int, String, array of object |Aanvullende waarden om te testen op null. |

### <a name="return-value"></a>Retourwaarde

De waarde van de eerste niet-null-para meters, die een teken reeks, int, matrix of object kan zijn. Null als alle para meters null zijn.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) ziet u de uitvoer van verschillende manieren van Coalesce.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "null1": null,
                "null2": null,
                "string": "default",
                "int": 1,
                "object": {"first": "default"},
                "array": [1]
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').string)]"
        },
        "intOutput": {
            "type": "int",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').int)]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').object)]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').array)]"
        },
        "emptyOutput": {
            "type": "bool",
            "value": "[empty(coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2))]"
        }
    }
}
```

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| stringOutput | Tekenreeks | standaardinstelling |
| intOutput | Int | 1 |
| objectOutput | Object | {"eerste": "standaard"} |
| arrayOutput | Matrix |  [1] |
| emptyOutput | Booleaanse waarde | True |

## <a name="equals"></a>is gelijk aan

`equals(arg1, arg2)`

Hiermee wordt gecontroleerd of twee waarden gelijk zijn aan elkaar.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| Arg1 |Yes |int, String, array of object |De eerste waarde die moet worden gecontroleerd op gelijkheid. |
| Arg2 |Yes |int, String, array of object |De tweede waarde om te controleren op gelijkheid. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de waarden gelijk zijn. anders **False**.

### <a name="remarks"></a>Opmerkingen

De functie equals wordt vaak gebruikt in combi natie met het `condition` element om te testen of een resource wordt geïmplementeerd.

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
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| Arg1 |Yes |int of String |De eerste waarde voor de grotere vergelijking. |
| Arg2 |Yes |int of String |De tweede waarde voor de grotere vergelijking. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de eerste waarde groter is dan de tweede waarde; anders **False**.

### <a name="example"></a>Voorbeeld

Met de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) wordt gecontroleerd of de ene waarde groter is dan de andere.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| Arg1 |Yes |int of String |De eerste waarde voor de grotere of gelijk zijnde vergelijking. |
| Arg2 |Yes |int of String |De tweede waarde voor de groter of gelijke vergelijking. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de eerste waarde groter is dan of gelijk is aan de tweede waarde; anders **False**.

### <a name="example"></a>Voorbeeld

Met de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) wordt gecontroleerd of de ene waarde groter is dan of gelijk is aan de andere.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| Arg1 |Yes |int of String |De eerste waarde voor de minder vergelijkingen. |
| Arg2 |Yes |int of String |De tweede waarde voor de minder vergelijkingen. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de eerste waarde kleiner is dan de tweede waarde; anders **False**.

### <a name="example"></a>Voorbeeld

Met de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) wordt gecontroleerd of de ene waarde kleiner is dan de andere.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| Arg1 |Yes |int of String |De eerste waarde voor de vergelijking met minder of gelijk aan. |
| Arg2 |Yes |int of String |De tweede waarde voor de vergelijking met minder of gelijk aan. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de eerste waarde kleiner dan of gelijk aan de tweede waarde is; anders **False**.

### <a name="example"></a>Voorbeeld

Met de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) wordt gecontroleerd of de ene waarde kleiner is dan of gelijk is aan de andere.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
