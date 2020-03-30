---
title: Sjabloonfuncties - arrays en objecten
description: Beschrijft de functies die moeten worden gebruikt in een Azure Resource Manager-sjabloon voor het werken met arrays en objecten.
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 0b4bb80f6d7a7cc20a8b2dcc71e890f2ada7c5be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156372"
---
# <a name="array-and-object-functions-for-arm-templates"></a>Array- en objectfuncties voor ARM-sjablonen

Resourcebeheer biedt verschillende functies voor het werken met arrays en objecten in uw Azure Resource Manager -sjabloon (ARM).

* [matrix](#array)
* [samensmelten](#coalesce)
* [Concat](#concat)
* [Bevat](#contains)
* [createArray](#createarray)
* [Lege](#empty)
* [Eerste](#first)
* [Snijpunt](#intersection)
* [Json](#json)
* [Laatste](#last)
* [Lengte](#length)
* [Max](#max)
* [Min](#min)
* [Bereik](#range)
* [Overslaan](#skip)
* [Nemen](#take)
* [Unie](#union)

Zie Splitsen als u een array met tekenreekswaarden wilt laten [afgebaken](template-functions-string.md#split)van een waarde.

## <a name="array"></a>matrix

`array(convertToArray)`

Hiermee converteert u de waarde naar een array.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| convertToArray converteren |Ja |int, tekenreeks, array of object |De waarde die moet worden omgezet in een array. |

### <a name="return-value"></a>Retourwaarde

Een array.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/array.json) ziet u hoe u de arrayfunctie met verschillende typen gebruikt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "intToConvert": {
            "type": "int",
            "defaultValue": 1
        },
        "stringToConvert": {
            "type": "string",
            "defaultValue": "efgh"
        },
        "objectToConvert": {
            "type": "object",
            "defaultValue": {"a": "b", "c": "d"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "intOutput": {
            "type": "array",
            "value": "[array(parameters('intToConvert'))]"
        },
        "stringOutput": {
            "type": "array",
            "value": "[array(parameters('stringToConvert'))]"
        },
        "objectOutput": {
            "type": "array",
            "value": "[array(parameters('objectToConvert'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| intOutput | Matrix | [1] |
| tekenreeksUitvoer | Matrix | [efgh"] |
| objectOutput | Matrix | [{"a": "b", "c": "d"}] |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

## <a name="coalesce"></a>samensmelten

`coalesce(arg1, arg2, arg3, ...)`

Retourneert de eerste niet-null-waarde van de parameters. Lege tekenreeksen, lege arrays en lege objecten zijn niet null.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int, tekenreeks, array of object |De eerste waarde die moet worden getest op null. |
| extra args |Nee |int, tekenreeks, array of object |Aanvullende waarden om te testen op null. |

### <a name="return-value"></a>Retourwaarde

De waarde van de eerste niet-null-parameters, die een tekenreeks, int, array of object kunnen zijn. Null als alle parameters nietig zijn.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) ziet u de output van verschillende toepassingen van samensmelten.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| tekenreeksUitvoer | Tekenreeks | standaardinstelling |
| intOutput | Int | 1 |
| objectOutput | Object | {"eerste": "standaard"} |
| arrayOutput | Matrix | [1] |
| leegUitvoer | Booleaanse waarde | True |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

## <a name="concat"></a>Concat

`concat(arg1, arg2, arg3, ...)`

Combineert meerdere arrays en retourneert de samengevoegde array, of combineert meerdere tekenreekswaarden en retourneert de samengevoegde tekenreeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |array of tekenreeks |De eerste array of tekenreeks voor samenvoeging. |
| aanvullende argumenten |Nee |array of tekenreeks |Extra arrays of tekenreeksen in opeenvolgende volgorde voor samenvoeging. |

Deze functie kan een willekeurig aantal argumenten bevatten en kan tekenreeksen of arrays voor de parameters accepteren. U echter niet zowel arrays als tekenreeksen opgeven voor parameters. Arrays worden alleen gekoppeld aan andere arrays.

### <a name="return-value"></a>Retourwaarde

Een tekenreeks of array met samengevoegde waarden.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) ziet u hoe u twee arrays combineren.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstArray": {
            "type": "array",
            "defaultValue": [
                "1-1",
                "1-2",
                "1-3"
            ]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": [
                "2-1",
                "2-2",
                "2-3"
            ]
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| terugkeren | Matrix | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) ziet u hoe u twee tekenreekswaarden combineert en een samengevoegde tekenreeks retourneert.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| concatOutput | Tekenreeks | prefix-5yj4yjf5mbg72 |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

## <a name="contains"></a>bevat

`contains(container, itemToFind)`

Hiermee wordt gecontroleerd of een array een waarde bevat, een object een sleutel of een tekenreeks een subtekenreeks bevat. De snaarvergelijking is hoofdlettergevoelig. Wanneer u echter test of een object een sleutel bevat, is de vergelijking hoofdletters ongevoelig.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| container |Ja |array, object of tekenreeks |De waarde die de te vinden waarde bevat. |
| itemToFind |Ja |tekenreeks of int |De waarde om te vinden. |

### <a name="return-value"></a>Retourwaarde

**True** als het object is gevonden; anders, **False**.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) ziet u hoe u contains met verschillende typen gebruikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| tekenreeksTrue | Booleaanse waarde | True |
| tekenreeksFalse | Booleaanse waarde | False |
| objectTrue | Booleaanse waarde | True |
| objectFalse objectFalse objectFalse objectFalse | Booleaanse waarde | False |
| arrayTrue | Booleaanse waarde | True |
| arrayFalse (arrayFalse) | Booleaanse waarde | False |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

## <a name="createarray"></a>array maken

`createArray (arg1, arg2, arg3, ...)`

Hiermee maakt u een array op basis van de parameters.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Tekenreeks, geheel getal, array of object |De eerste waarde in de array. |
| aanvullende argumenten |Nee |Tekenreeks, geheel getal, array of object |Extra waarden in de array. |

### <a name="return-value"></a>Retourwaarde

Een array.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/createarray.json) ziet u hoe u createArray met verschillende typen gebruikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringArray": {
            "type": "array",
            "value": "[createArray('a', 'b', 'c')]"
        },
        "intArray": {
            "type": "array",
            "value": "[createArray(1, 2, 3)]"
        },
        "objectArray": {
            "type": "array",
            "value": "[createArray(parameters('objectToTest'))]"
        },
        "arrayArray": {
            "type": "array",
            "value": "[createArray(parameters('arrayToTest'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| tekenreeksArray | Matrix | [a", "b", "c"] |
| intArray | Matrix | [1, 2, 3] |
| objectArray | Matrix | [{"one": "a", "twee": "b", "drie": "c"}] |
| arrayArray | Matrix | [["één", "twee", "drie"]] |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

## <a name="empty"></a>leeg

`empty(itemToTest)`

Hiermee bepaalt u of een array, object of tekenreeks leeg is.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |array, object of tekenreeks |De waarde om te controleren of deze leeg is. |

### <a name="return-value"></a>Retourwaarde

**Geeft als** resultaat True als de waarde leeg is; anders, **False**.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) wordt gecontroleerd of een array, object en tekenreeks leeg zijn.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| arrayLeeg | Booleaanse waarde | True |
| objectLeeg | Booleaanse waarde | True |
| tekenreeksLeeg | Booleaanse waarde | True |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

## <a name="first"></a>Eerste

`first(arg1)`

Geeft als resultaat het eerste element van de array of het eerste teken van de tekenreeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |array of tekenreeks |De waarde om het eerste element of teken op te halen. |

### <a name="return-value"></a>Retourwaarde

Het type (tekenreeks, int, array of object) van het eerste element in een array of het eerste teken van een tekenreeks.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) ziet u hoe u de eerste functie met een array en tekenreeks gebruiken.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Tekenreeks | een |
| tekenreeksUitvoer | Tekenreeks | O |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

## <a name="intersection"></a>Snijpunt

`intersection(arg1, arg2, arg3, ...)`

Geeft als resultaat een enkele array of object met de algemene elementen uit de parameters.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |array of object |De eerste waarde die u moet gebruiken voor het vinden van gemeenschappelijke elementen. |
| arg2 |Ja |array of object |De tweede waarde die moet worden gebruikt voor het vinden van gemeenschappelijke elementen. |
| aanvullende argumenten |Nee |array of object |Aanvullende waarden om te gebruiken voor het vinden van gemeenschappelijke elementen. |

### <a name="return-value"></a>Retourwaarde

Een array of object met de algemene elementen.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) ziet u hoe u kruising en arrays en objecten gebruikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "z", "three": "c"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "drie": "c"} |
| arrayOutput | Matrix | ["twee", "drie"] |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

## <a name="json"></a>json

`json(arg1)`

Retourneert een JSON-object.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |tekenreeks |De waarde die u wilt converteren naar JSON. |

### <a name="return-value"></a>Retourwaarde

Het JSON-object van de opgegeven tekenreeks of een leeg object wanneer **null** is opgegeven.

### <a name="remarks"></a>Opmerkingen

Als u een parameterwaarde of variabele in het JSON-object moet opnemen, gebruikt u de [concatfunctie](template-functions-string.md#concat) om de tekenreeks te maken die u aan de functie doorgeeft.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) ziet u hoe u de functie Json met arrays en objecten gebruikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testValue": {
            "type": "string",
            "defaultValue": "demo value"
        }
    },
    "resources": [
    ],
    "outputs": {
        "jsonOutput": {
            "type": "object",
            "value": "[json('{\"a\": \"b\"}')]"
        },
        "nullOutput": {
            "type": "bool",
            "value": "[empty(json('null'))]"
        },
        "paramOutput": {
            "type": "object",
            "value": "[json(concat('{\"a\": \"', parameters('testValue'), '\"}'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| jsonOutput | Object | {"a": "b"} |
| nullOutput | Booleaans | True |
| paramOutput | Object | {"a": "demowaarde"}

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

## <a name="last"></a>Laatste

`last (arg1)`

Geeft als resultaat het laatste element van de array of het laatste teken van de tekenreeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |array of tekenreeks |De waarde om het laatste element of teken op te halen. |

### <a name="return-value"></a>Retourwaarde

Het type (tekenreeks, int, array of object) van het laatste element in een array of het laatste teken van een tekenreeks.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) ziet u hoe u de laatste functie met een array en tekenreeks gebruiken.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Tekenreeks | drie |
| tekenreeksUitvoer | Tekenreeks | a |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

## <a name="length"></a>lengte

`length(arg1)`

Geeft als resultaat het aantal elementen in een array, tekens in een tekenreeks of eigenschappen op basisniveau in een object.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |array, tekenreeks of object |De array die moet worden gebruikt voor het verkrijgen van het aantal elementen, de tekenreeks die moet worden gebruikt voor het verkrijgen van het aantal tekens of het object dat moet worden gebruikt voor het verkrijgen van het aantal eigenschappen op basisniveau. |

### <a name="return-value"></a>Retourwaarde

Een int.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) ziet u hoe u lengte gebruikt met een array en tekenreeks:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "propA": "one",
                "propB": "two",
                "propC": "three",
                "propD": {
                    "propD-1": "sub",
                    "propD-2": "sub"
                }
            }
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        },
        "objectLength": {
            "type": "int",
            "value": "[length(parameters('objectToTest'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| arrayLengte | Int | 3 |
| tekenreeksLengte | Int | 13 |
| objectLengte | Int | 4 |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

U deze functie met een array gebruiken om het aantal iteraties op te geven bij het maken van resources. In het volgende voorbeeld verwijst de **parametersiteNames** naar een array met namen die moeten worden gebruikt bij het maken van de websites.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Zie Meerdere exemplaren van resources maken [in Azure Resource Manager](copy-resources.md)voor meer informatie over het gebruik van deze functie met een array.

## <a name="max"></a>Max

`max(arg1)`

Geeft als resultaat de maximale waarde van een matrix van gehele getallen of een door komma's gescheiden lijst met gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |array van gehele getallen, of door komma's gescheiden lijst van gehele getallen |De collectie om de maximale waarde te krijgen. |

### <a name="return-value"></a>Retourwaarde

Een int die de maximale waarde vertegenwoordigt.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) ziet u hoe u max gebruikt met een array en een lijst met gehele getallen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

## <a name="min"></a>min.

`min(arg1)`

Geeft als resultaat de minimumwaarde van een matrix van gehele getallen of een door komma's gescheiden lijst met gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |array van gehele getallen, of door komma's gescheiden lijst van gehele getallen |De collectie om de minimale waarde te krijgen. |

### <a name="return-value"></a>Retourwaarde

Een int die de minimumwaarde vertegenwoordigt.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) ziet u hoe u min gebruikt met een array en een lijst met gehele getallen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

## <a name="range"></a>Bereik

`range(startIndex, count)`

Hiermee maakt u een array van gehele getallen van een begingeheel getal en bevat een aantal items.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Startindex |Ja |int |Het eerste gehele getal in de array. De som van startIndex en telling mag niet groter zijn dan 2147483647. |
| count |Ja |int |Het aantal gehele getallen in de array. Moet niet-negatief geheel getal tot 10000. |

### <a name="return-value"></a>Retourwaarde

Een array van gehele getallen.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/range.json) ziet u hoe u de bereikfunctie gebruikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "startingInt": {
            "type": "int",
            "defaultValue": 5
        },
        "numberOfElements": {
            "type": "int",
            "defaultValue": 3
        }
    },
    "resources": [],
    "outputs": {
        "rangeOutput": {
            "type": "array",
            "value": "[range(parameters('startingInt'),parameters('numberOfElements'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| rangeOutput | Matrix | [5, 6, 7] |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Retourneert een array met alle elementen na het opgegeven getal in de array of retourneert een tekenreeks met alle tekens na het opgegeven getal in de tekenreeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Originalvalue |Ja |array of tekenreeks |De array of tekenreeks die u wilt gebruiken om over te slaan. |
| nummerToSkip |Ja |int |Het aantal elementen of tekens dat moet worden overgeslagen. Als deze waarde 0 of minder is, worden alle elementen of tekens in de waarde geretourneerd. Als deze groter is dan de lengte van de array of tekenreeks, wordt een lege array of tekenreeks geretourneerd. |

### <a name="return-value"></a>Retourwaarde

Een array of tekenreeks.

### <a name="example"></a>Voorbeeld

Met de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) wordt het opgegeven aantal elementen in de array en het opgegeven aantal tekens in een tekenreeks overgeslagen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Matrix | ["drie"] |
| tekenreeksUitvoer | Tekenreeks | twee drie |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

## <a name="take"></a>Nemen

`take(originalValue, numberToTake)`

Retourneert een array met het opgegeven aantal elementen vanaf het begin van de array of een tekenreeks met het opgegeven aantal tekens vanaf het begin van de tekenreeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Originalvalue |Ja |array of tekenreeks |De array of tekenreeks om de elementen uit te nemen. |
| nummerToTake |Ja |int |Het aantal elementen of tekens dat u moet nemen. Als deze waarde 0 of minder is, wordt een lege array of tekenreeks geretourneerd. Als deze groter is dan de lengte van de opgegeven array of tekenreeks, worden alle elementen in de array of tekenreeks geretourneerd. |

### <a name="return-value"></a>Retourwaarde

Een array of tekenreeks.

### <a name="example"></a>Voorbeeld

De volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) neemt het opgegeven aantal elementen uit de array en tekens uit een tekenreeks.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Matrix | ["één", "twee"] |
| tekenreeksUitvoer | Tekenreeks | op |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

## <a name="union"></a>Unie

`union(arg1, arg2, arg3, ...)`

Geeft als resultaat een enkele array of object met alle elementen uit de parameters. Dubbele waarden of sleutels worden slechts één keer opgenomen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |array of object |De eerste waarde die u moet gebruiken voor het samenvoegen van elementen. |
| arg2 |Ja |array of object |De tweede waarde die moet worden gebruikt voor het samenvoegen van elementen. |
| aanvullende argumenten |Nee |array of object |Aanvullende waarden om te gebruiken voor het samenvoegen van elementen. |

### <a name="return-value"></a>Retourwaarde

Een array of object.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) ziet u hoe u de unie met arrays en objecten gebruiken:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c1"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"three": "c2", "four": "d", "five": "e"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["three", "four"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "twee": "b", "drie": "c2", "vier": "d", "vijf": "e"} |
| arrayOutput | Matrix | ["één", "twee", "drie", "vier"] |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Sjablonen voor Azure Resource Manager ontwerpen](template-syntax.md)voor een beschrijving van de secties in een Azure Resource Manager-sjabloon.
* Zie [Gekoppelde sjablonen gebruiken met Azure Resource Manager](linked-templates.md)als u meerdere sjablonen wilt samenvoegen.
* Zie Meerdere exemplaren van resources maken [in Azure Resource Manager](copy-resources.md)als u een bepaald aantal keren wilt herhalen bij het maken van een type resource.
* Zie Een toepassing implementeren met Azure [Resource Manager-sjabloon](deploy-powershell.md)als u wilt zien hoe u de sjabloon implementeren die u hebt gemaakt.

