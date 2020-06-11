---
title: 'Sjabloon functies: objecten'
description: Hierin worden de functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon voor het werken met objecten.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fede4d6c71e45b119e500d4c9c6f91765d052036
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84676791"
---
# <a name="object-functions-for-arm-templates"></a>Object functies voor ARM-sjablonen

Resource Manager biedt verschillende functies voor het werken met objecten in uw Azure Resource Manager-sjabloon (ARM).

* [daarin](#contains)
* [leeg](#empty)
* [Snij punt](#intersection)
* [JSON](#json)
* [lange](#length)
* [Réunion](#union)

## <a name="contains"></a>bevat

`contains(container, itemToFind)`

Controleert of een matrix een waarde bevat, een object bevat een sleutel of een teken reeks bevat een subtekenreeks. De teken reeks vergelijking is hoofdletter gevoelig. Als er echter wordt getest of een object een sleutel bevat, is de vergelijking niet hoofdletter gevoelig.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| container |Ja |matrix, object of teken reeks |De waarde die de te zoeken waarde bevat. |
| itemToFind |Ja |teken reeks of int |De waarde die moet worden gevonden. |

### <a name="return-value"></a>Retourwaarde

**Waar** als het item is gevonden; anders **False**.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) ziet u hoe u deze kunt gebruiken met verschillende typen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| stringTrue | Booleaanse waarde | True |
| stringFalse | Booleaanse waarde | False |
| objectTrue | Booleaanse waarde | True |
| objectFalse | Booleaanse waarde | False |
| arrayTrue | Booleaanse waarde | True |
| arrayFalse | Booleaanse waarde | False |

## <a name="empty"></a>leeg

`empty(itemToTest)`

Bepaalt of een matrix, een object of een teken reeks leeg is.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |matrix, object of teken reeks |De waarde die moet worden gecontroleerd of deze leeg is. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de waarde leeg is; anders **False**.

### <a name="example"></a>Voorbeeld

Met de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) wordt gecontroleerd of een matrix, een object en een teken reeks leeg zijn.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| arrayEmpty | Booleaanse waarde | True |
| objectEmpty | Booleaanse waarde | True |
| stringEmpty | Booleaanse waarde | True |

## <a name="intersection"></a>Snij punt

`intersection(arg1, arg2, arg3, ...)`

Retourneert een enkele matrix of een object met de algemene elementen van de para meters.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |matrix of object |De eerste waarde die moet worden gebruikt voor het zoeken van algemene elementen. |
| Arg2 |Ja |matrix of object |De tweede waarde die moet worden gebruikt voor het zoeken van algemene elementen. |
| aanvullende argumenten |Nee |matrix of object |Aanvullende waarden die moeten worden gebruikt voor het zoeken van algemene elementen. |

### <a name="return-value"></a>Retourwaarde

Een matrix of object met de algemene elementen.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) ziet u hoe u het snij punt gebruikt met matrices en objecten:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| objectOutput | Object | {"een": "a", "drie": "c"} |
| arrayOutput | Matrix | ["twee", "drie"] |

## <a name="json"></a>json

`json(arg1)`

Retourneert een JSON-object.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |tekenreeks |De waarde die moet worden geconverteerd naar JSON. |

### <a name="return-value"></a>Retourwaarde

Het JSON-object van de opgegeven teken reeks of een leeg object wanneer **Null** is opgegeven.

### <a name="remarks"></a>Opmerkingen

Als u een parameter waarde of variabele in het JSON-object moet toevoegen, gebruikt u de functie [concat](template-functions-string.md#concat) om de teken reeks te maken die u aan de functie doorgeeft.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) ziet u hoe u de json-functie gebruikt. U kunt een teken reeks door geven die het object vertegenwoordigt of **Null** gebruiken als er geen waarde nodig is.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "jsonObject1": {
            "type": "string",
            "defaultValue": "null"
        },
        "jsonObject2": {
            "type": "string",
            "defaultValue": "{\"a\": \"b\"}"
        },
        "testValue": {
            "type": "string",
            "defaultValue": "demo value"
        }
    },
    "resources": [
    ],
    "outputs": {
        "jsonOutput1": {
            "type": "bool",
            "value": "[empty(json(parameters('jsonObject1')))]"
        },
        "jsonOutput2": {
            "type": "object",
            "value": "[json(parameters('jsonObject2'))]"
        },
        "paramOutput": {
            "type": "object",
            "value": "[json(concat('{\"a\": \"', parameters('testValue'), '\"}'))]"
        }
    }
}
```

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| jsonOutput1 | Boolean-waarde | True |
| jsonOutput2 | Object | {"a": "b"} |
| paramOutput | Object | {"a": "demo waarde"}

## <a name="length"></a>lengte

`length(arg1)`

Retourneert het aantal elementen in een matrix, tekens in een teken reeks of hoofd niveau-eigenschappen in een-object.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |matrix, teken reeks of object |De matrix die moet worden gebruikt voor het ophalen van het aantal elementen, de teken reeks die moet worden gebruikt voor het ophalen van het aantal tekens of het object dat moet worden gebruikt voor het ophalen van het aantal eigenschappen op hoofd niveau. |

### <a name="return-value"></a>Retourwaarde

Een int.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) ziet u hoe lengte met een matrix en teken reeks kan worden gebruikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="union"></a>Réunion

`union(arg1, arg2, arg3, ...)`

Retourneert een enkele matrix of een object met alle elementen van de para meters. Dubbele waarden of sleutels zijn slechts eenmaal opgenomen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |matrix of object |De eerste waarde die moet worden gebruikt voor het toevoegen van elementen. |
| Arg2 |Ja |matrix of object |De tweede waarde die moet worden gebruikt voor het toevoegen van elementen. |
| aanvullende argumenten |Nee |matrix of object |Aanvullende waarden die moeten worden gebruikt voor het toevoegen van elementen. |

### <a name="return-value"></a>Retourwaarde

Een matrix of object.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) ziet u hoe u Union gebruikt met matrices en objecten:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| objectOutput | Object | {"One": "a", "twee": "b", "drie": "C2", "vier": "d", "vijf": "e"} |
| arrayOutput | Matrix | [' één ', ' twee ', ' drie ', ' vier '] |

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor een beschrijving van de secties in een Azure Resource Manager sjabloon.
