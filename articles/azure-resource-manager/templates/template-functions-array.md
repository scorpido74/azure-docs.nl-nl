---
title: 'Sjabloon functies: matrices en objecten'
description: Hierin worden de functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon voor het werken met matrices en objecten.
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 16b36d290a99da56dffe82c9e4c7265a89350391
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484088"
---
# <a name="array-and-object-functions-for-azure-resource-manager-templates"></a>Matrix-en object functies voor Azure Resource Manager sjablonen

Resource Manager biedt verschillende functies voor het werken met matrices en objecten.

* [array](#array)
* [coalesce](#coalesce)
* [concat](#concat)
* [contains](#contains)
* [createArray](#createarray)
* [empty](#empty)
* [first](#first)
* [intersection](#intersection)
* [json](#json)
* [last](#last)
* [Lengte](#length)
* [max](#max)
* [min](#min)
* [range](#range)
* [skip](#skip)
* [take](#take)
* [union](#union)

Als u een matrix van teken reeks waarden wilt ophalen die door een waarde worden gescheiden, raadpleegt u [splitsen](template-functions-string.md#split).

## <a name="array"></a>matrix

`array(convertToArray)`

Zet de waarde om in een matrix.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| convertToArray |Ja |int, string, array of object |De waarde die moet worden geconverteerd naar een matrix. |

### <a name="return-value"></a>Retourwaarde

Een matrix.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/array.json) ziet u hoe u de matrix functie kunt gebruiken met verschillende typen.

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| intOutput | Matrix | [1] |
| stringOutput | Matrix | ["efgh"] |
| objectOutput | Matrix | [{"a": "b", "c": "d"}] |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

## <a name="coalesce"></a>Voeg

`coalesce(arg1, arg2, arg3, ...)`

Retourneert de eerste waarde die niet null is van de para meters. Lege teken reeksen, lege matrices en lege objecten zijn niet null.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int, string, array of object |De eerste waarde die moet worden getest op null. |
| aanvullende argumenten |Nee |int, string, array of object |Aanvullende waarden om te testen op null. |

### <a name="return-value"></a>Retourwaarde

De waarde van de eerste niet-null-para meters, die een teken reeks, int, matrix of object kan zijn. Null als alle para meters null zijn. 

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) ziet u de uitvoer van verschillende manieren van Coalesce.

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| stringOutput | Tekenreeks | standaardinstelling |
| intOutput | Int | 1 |
| objectOutput | Object | {"eerste": "standaard"} |
| arrayOutput | Matrix | [1] |
| emptyOutput | Bool | Waar |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

Combineert meerdere matrices en retourneert de samengevoegde matrix, of combineert meerdere teken reeks waarden en retourneert de aaneengeschakelde teken reeks. 

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matrix of teken reeks |De eerste matrix of teken reeks voor samen voeging. |
| aanvullende argumenten |Nee |matrix of teken reeks |Extra matrices of teken reeksen in sequentiële volg orde voor samen voeging. |

Deze functie kan elk wille keurig aantal argumenten hebben en kan teken reeksen of matrices voor de para meters accepteren. U kunt echter geen matrixen en teken reeksen opgeven voor para meters. Matrices worden alleen samengevoegd met andere matrices.

### <a name="return-value"></a>Retourwaarde

Een teken reeks of matrix van aaneengeschakelde waarden.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) ziet u hoe u twee matrices kunt combi neren.

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| enter | Matrix | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) ziet u hoe u twee teken reeks waarden combineert en een samengevoegde teken reeks retourneert.

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| concatOutput | Tekenreeks | prefix-5yj4yjf5mbg72 |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

## <a name="contains"></a>bevat

`contains(container, itemToFind)`

Controleert of een matrix een waarde bevat, een object bevat een sleutel of een teken reeks bevat een subtekenreeks. De teken reeks vergelijking is hoofdletter gevoelig. Als er echter wordt getest of een object een sleutel bevat, is de vergelijking niet hoofdletter gevoelig.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| container |Ja |matrix, object of teken reeks |De waarde die de te zoeken waarde bevat. |
| itemToFind |Ja |teken reeks of int |De waarde die moet worden gevonden. |

### <a name="return-value"></a>Retourwaarde

**Waar** als het item is gevonden; anders **False**.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) ziet u hoe u deze kunt gebruiken met verschillende typen:

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| stringTrue | Bool | Waar |
| stringFalse | Bool | Onwaar |
| objectTrue | Bool | Waar |
| objectFalse | Bool | Onwaar |
| arrayTrue | Bool | Waar |
| arrayFalse | Bool | Onwaar |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

## <a name="createarray"></a>createarray

`createArray (arg1, arg2, arg3, ...)`

Hiermee wordt een matrix gemaakt op basis van de para meters.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Teken reeks, geheel getal, matrix of object |De eerste waarde in de matrix. |
| aanvullende argumenten |Nee |Teken reeks, geheel getal, matrix of object |Aanvullende waarden in de matrix. |

### <a name="return-value"></a>Retourwaarde

Een matrix.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/createarray.json) ziet u hoe u createArray gebruikt met verschillende typen:

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| stringArray | Matrix | ["a", "b", "c"] |
| intArray | Matrix | [1, 2, 3] |
| objectArray | Matrix | [{"een": "a", "twee": "b", "drie": "c"}] |
| arrayArray | Matrix | [[' een ', ' twee ', ' drie ']] |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

## <a name="empty"></a>leeg

`empty(itemToTest)`

Bepaalt of een matrix, een object of een teken reeks leeg is.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |matrix, object of teken reeks |De waarde die moet worden gecontroleerd als deze leeg is. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de waarde leeg is; anders **False**.

### <a name="example"></a>Voorbeeld

Met de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) wordt gecontroleerd of een matrix, een object en een teken reeks leeg zijn.

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| arrayEmpty | Bool | Waar |
| objectEmpty | Bool | Waar |
| stringEmpty | Bool | Waar |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

## <a name="first"></a>first

`first(arg1)`

Retourneert het eerste element van de matrix, of het eerste teken van de teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matrix of teken reeks |De waarde voor het ophalen van het eerste element of teken. |

### <a name="return-value"></a>Retourwaarde

Het type (teken reeks, int, matrix of object) van het eerste element in een matrix of het eerste teken van een teken reeks.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) ziet u hoe u de eerste functie gebruikt met een matrix en teken reeks.

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Tekenreeks | één |
| stringOutput | Tekenreeks | O |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

## <a name="intersection"></a>Snij punt

`intersection(arg1, arg2, arg3, ...)`

Retourneert een enkele matrix of een object met de algemene elementen van de para meters.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matrix of object |De eerste waarde die moet worden gebruikt voor het zoeken van algemene elementen. |
| Arg2 |Ja |matrix of object |De tweede waarde die moet worden gebruikt voor het zoeken van algemene elementen. |
| aanvullende argumenten |Nee |matrix of object |Aanvullende waarden die moeten worden gebruikt voor het zoeken van algemene elementen. |

### <a name="return-value"></a>Retourwaarde

Een matrix of object met de algemene elementen.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) ziet u hoe u het snij punt gebruikt met matrices en objecten:

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| objectOutput | Object | {"een": "a", "drie": "c"} |
| arrayOutput | Matrix | ["twee", "drie"] |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

## <a name="json"></a>json

`json(arg1)`

Retourneert een JSON-object.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |string |De waarde die moet worden geconverteerd naar JSON. |

### <a name="return-value"></a>Retourwaarde

Het JSON-object van de opgegeven teken reeks of een leeg object wanneer **Null** is opgegeven.

### <a name="remarks"></a>Opmerkingen

Als u een parameter waarde of variabele in het JSON-object moet toevoegen, gebruikt u de functie [concat](template-functions-string.md#concat) om de teken reeks te maken die u aan de functie doorgeeft.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) ziet u hoe u de json-functie gebruikt met matrices en objecten:

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| jsonOutput | Object | {"a": "b"} |
| nullOutput | Booleaans | Waar |
| paramOutput | Object | {"a": "demo waarde"}

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

## <a name="last"></a>laatste

`last (arg1)`

Retourneert het laatste element van de matrix, of het laatste teken van de teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matrix of teken reeks |De waarde voor het ophalen van het laatste element of teken. |

### <a name="return-value"></a>Retourwaarde

Het type (teken reeks, int, matrix of object) van het laatste element in een matrix of het laatste teken van een teken reeks.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) ziet u hoe u de laatste functie gebruikt met een matrix en teken reeks.

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Tekenreeks | drie |
| stringOutput | Tekenreeks | e |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

## <a name="length"></a>length

`length(arg1)`

Retourneert het aantal elementen in een matrix, tekens in een teken reeks of hoofd niveau-eigenschappen in een-object.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matrix, teken reeks of object |De matrix die moet worden gebruikt voor het ophalen van het aantal elementen, de teken reeks die moet worden gebruikt voor het ophalen van het aantal tekens of het object dat moet worden gebruikt voor het ophalen van het aantal eigenschappen op hoofd niveau. |

### <a name="return-value"></a>Retourwaarde

Een int. 

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) ziet u hoe lengte met een matrix en teken reeks kan worden gebruikt:

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

U kunt deze functie gebruiken met een matrix om het aantal iteraties op te geven bij het maken van resources. In het volgende voor beeld verwijst de para meter **siteNames** naar een matrix met namen die moeten worden gebruikt bij het maken van de websites.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Zie [meerdere exemplaren van resources maken in azure Resource Manager](create-multiple-instances.md)voor meer informatie over het gebruik van deze functie met een matrix.

## <a name="max"></a>max.

`max(arg1)`

Retourneert de maximum waarde van een matrix met gehele getallen of een door komma's gescheiden lijst met gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matrix van gehele getallen of door komma's gescheiden lijst met gehele getallen |De verzameling om de maximum waarde op te halen. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de maximum waarde vertegenwoordigt.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) ziet u hoe u Max kunt gebruiken met een matrix en een lijst met gehele getallen:

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

## <a name="min"></a>min.

`min(arg1)`

Retourneert de minimum waarde van een matrix met gehele getallen of een door komma's gescheiden lijst met gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matrix van gehele getallen of door komma's gescheiden lijst met gehele getallen |De verzameling om de minimum waarde op te halen. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de minimum waarde vertegenwoordigt.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) ziet u hoe min met een matrix en een lijst met gehele getallen moet worden gebruikt:

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

## <a name="range"></a>bereik

`range(startingInteger, numberOfElements)`

Hiermee maakt u een matrix met gehele getallen van een begin-geheel getal dat een aantal items bevat.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| startingInteger |Ja |int |Het eerste geheel getal in de matrix. |
| numberofElements |Ja |int |Het aantal gehele getallen in de matrix. |

### <a name="return-value"></a>Retourwaarde

Een matrix met gehele getallen.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/range.json) ziet u hoe u de functie Range gebruikt:

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| rangeOutput | Matrix | [5, 6, 7] |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Retourneert een matrix met alle elementen na het opgegeven getal in de matrix, of retourneert een teken reeks met alle tekens na het opgegeven getal in de teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |matrix of teken reeks |De matrix of teken reeks die moet worden gebruikt voor het overs Laan. |
| numberToSkip |Ja |int |Het aantal elementen of tekens dat moet worden overgeslagen. Als deze waarde 0 of kleiner is, worden alle elementen of tekens in de waarde geretourneerd. Als deze groter is dan de lengte van de matrix of teken reeks, wordt een lege matrix of teken reeks geretourneerd. |

### <a name="return-value"></a>Retourwaarde

Een matrix of teken reeks.

### <a name="example"></a>Voorbeeld

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) slaat het opgegeven aantal elementen in de matrix over en het opgegeven aantal tekens in een teken reeks.

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Matrix | ["drie"] |
| stringOutput | Tekenreeks | 2 3 |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Retourneert een matrix met het opgegeven aantal elementen vanaf het begin van de matrix, of een teken reeks met het opgegeven aantal tekens vanaf het begin van de teken reeks.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |matrix of teken reeks |De matrix of teken reeks waaruit de elementen moeten worden afgeleid. |
| numberToTake |Ja |int |Het aantal elementen of tekens dat moet worden uitgevoerd. Als deze waarde 0 of kleiner is, wordt een lege matrix of teken reeks geretourneerd. Als deze groter is dan de lengte van de opgegeven matrix of teken reeks, worden alle elementen in de matrix of teken reeks geretourneerd. |

### <a name="return-value"></a>Retourwaarde

Een matrix of teken reeks.

### <a name="example"></a>Voorbeeld

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) haalt het opgegeven aantal elementen uit de matrix en tekens uit een teken reeks.

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Matrix | [' één ', ' twee '] |
| stringOutput | Tekenreeks | op |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

Retourneert een enkele matrix of een object met alle elementen van de para meters. Dubbele waarden of sleutels zijn slechts eenmaal opgenomen.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |matrix of object |De eerste waarde die moet worden gebruikt voor het toevoegen van elementen. |
| Arg2 |Ja |matrix of object |De tweede waarde die moet worden gebruikt voor het toevoegen van elementen. |
| aanvullende argumenten |Nee |matrix of object |Aanvullende waarden die moeten worden gebruikt voor het toevoegen van elementen. |

### <a name="return-value"></a>Retourwaarde

Een matrix of object.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) ziet u hoe u Union gebruikt met matrices en objecten:

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| objectOutput | Object | {"One": "a", "twee": "b", "drie": "C2", "vier": "d", "vijf": "e"} |
| arrayOutput | Matrix | [' één ', ' twee ', ' drie ', ' vier '] |

In dit als voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

In dit als voorbeeldsjabloon wilt implementeren met PowerShell, gebruikt u:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

## <a name="next-steps"></a>Volgende stappen

* Zie voor een beschrijving van de secties in een Azure Resource Manager-sjabloon, [Authoring Azure Resource Manager-sjablonen](template-syntax.md).
* U kunt meerdere sjablonen samenvoegen, Zie [gekoppelde sjablonen gebruiken met Azure Resource Manager](linked-templates.md).
* Op een opgegeven aantal keren herhalen bij het maken van een type resource, Zie [meerdere exemplaren van resources maken in Azure Resource Manager](create-multiple-instances.md).
* Zie [een toepassing implementeren met Azure Resource Manager sjabloon](deploy-powershell.md)voor meer informatie over het implementeren van de sjabloon die u hebt gemaakt.

