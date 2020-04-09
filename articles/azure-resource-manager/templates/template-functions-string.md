---
title: Sjabloonfuncties - tekenreeks
description: Beschrijft de functies die u moet gebruiken in een Azure Resource Manager-sjabloon om met tekenreeksen te werken.
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c0517375b273384f263e8ba421995d4afb6c193b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982411"
---
# <a name="string-functions-for-arm-templates"></a>Tekenreeksfuncties voor ARM-sjablonen

Resourcebeheer biedt de volgende functies voor het werken met tekenreeksen in uw Azure Resource Manager-sjablonen (ARM):Resource Manager provides the following functions for working with strings in your Azure Resource Manager (ARM) templates:

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [Concat](#concat)
* [Bevat](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [Lege](#empty)
* [endsMet](#endswith)
* [Eerste](#first)
* [Formaat](#format)
* [Guid](#guid)
* [indexVan](#indexof)
* [Laatste](#last)
* [lastIndexOf](#lastindexof)
* [Lengte](#length)
* [nieuwGuid](#newguid)
* [padLeft](#padleft)
* [Vervangen](#replace)
* [Overslaan](#skip)
* [split](#split)
* [startMet](#startswith)
* [Tekenreeks](#string)
* [Subtekenreeks](#substring)
* [Nemen](#take)
* [toLower](#tolower)
* [Toupper](#toupper)
* [Trim](#trim)
* [uniqueString](#uniquestring)
* [Uri](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)

## <a name="base64"></a>base64

`base64(inputString)`

Geeft als resultaat de basis64-weergave van de invoertekenreeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| invoertekenreeks |Ja |tekenreeks |De waarde om terug te keren als een basis64 vertegenwoordiging. |

### <a name="return-value"></a>Retourwaarde

Een tekenreeks met de base64-weergave.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) ziet u hoe u de functie base64 gebruikt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| base64Output | Tekenreeks | b25lLCB0d28sIHRocmVl |
| toStringOutput | Tekenreeks | Een twee drie |
| toJsonOutput | Object | {"een": "a", "twee": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Converteert een basis64-weergave naar een JSON-object.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| base64Waarde |Ja |tekenreeks |De basis64-weergave om te converteren naar een JSON-object. |

### <a name="return-value"></a>Retourwaarde

Een JSON-object.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) wordt de functie base64ToJson gebruikt om een base64-waarde om te zetten:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| base64Output | Tekenreeks | b25lLCB0d28sIHRocmVl |
| toStringOutput | Tekenreeks | Een twee drie |
| toJsonOutput | Object | {"een": "a", "twee": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Hiermee converteert u een basis64-weergave naar een tekenreeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| base64Waarde |Ja |tekenreeks |De basis64-weergave om te converteren naar een tekenreeks. |

### <a name="return-value"></a>Retourwaarde

Een tekenreeks van de geconverteerde base64-waarde.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) wordt de functie base64ToString gebruikt om een base64-waarde om te zetten:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| base64Output | Tekenreeks | b25lLCB0d28sIHRocmVl |
| toStringOutput | Tekenreeks | Een twee drie |
| toJsonOutput | Object | {"een": "a", "twee": "b"} |

## <a name="concat"></a>Concat

`concat (arg1, arg2, arg3, ...)`

Combineert meerdere tekenreekswaarden en retourneert de samengevoegde tekenreeks, of combineert meerdere arrays en retourneert de samengevoegde array.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |tekenreeks of array |De eerste tekenreeks of array voor samenvoeging. |
| aanvullende argumenten |Nee |tekenreeks of array |Extra tekenreeksen of arrays in opeenvolgende volgorde voor samenvoeging. |

Deze functie kan een willekeurig aantal argumenten bevatten en kan tekenreeksen of arrays voor de parameters accepteren. U echter niet zowel arrays als tekenreeksen opgeven voor parameters. Snaren worden alleen samengevoegd met andere snaren.

### <a name="return-value"></a>Retourwaarde

Een tekenreeks of array met samengevoegde waarden.

### <a name="examples"></a>Voorbeelden

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

## <a name="contains"></a>bevat

`contains (container, itemToFind)`

Hiermee wordt gecontroleerd of een array een waarde bevat, een object een sleutel of een tekenreeks een subtekenreeks bevat. De snaarvergelijking is hoofdlettergevoelig. Wanneer u echter test of een object een sleutel bevat, is de vergelijking hoofdletters ongevoelig.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| container |Ja |array, object of tekenreeks |De waarde die de te vinden waarde bevat. |
| itemToFind |Ja |tekenreeks of int |De waarde om te vinden. |

### <a name="return-value"></a>Retourwaarde

**True** als het object is gevonden; anders, **False**.

### <a name="examples"></a>Voorbeelden

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

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Hiermee converteert u een waarde naar een gegevens-URI.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| tekenreeksToConvert |Ja |tekenreeks |De waarde die moet worden omgezet in een URI voor gegevens. |

### <a name="return-value"></a>Retourwaarde

Een tekenreeks die is opgemaakt als een gegevens-URI.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) wordt een waarde geconverteerd naar een gegevens-URI en wordt een gegevens-URI geconverteerd naar een tekenreeks:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| dataUriOutput | Tekenreeks | gegevens:tekst/vlakte;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Tekenreeks | Hallo mensen! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Hiermee converteert u een opgemaakte waarde voor gegevens URI naar een tekenreeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| datauritoconvert |Ja |tekenreeks |De URI-waarde voor gegevens om te converteren. |

### <a name="return-value"></a>Retourwaarde

Een tekenreeks met de geconverteerde waarde.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) wordt een waarde geconverteerd naar een gegevens-URI en wordt een gegevens-URI geconverteerd naar een tekenreeks:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| dataUriOutput | Tekenreeks | gegevens:tekst/vlakte;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Tekenreeks | Hallo mensen! |

## <a name="empty"></a>leeg

`empty(itemToTest)`

Hiermee bepaalt u of een array, object of tekenreeks leeg is.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |array, object of tekenreeks |De waarde om te controleren of deze leeg is. |

### <a name="return-value"></a>Retourwaarde

**Geeft als** resultaat True als de waarde leeg is; anders, **False**.

### <a name="examples"></a>Voorbeelden

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

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Hiermee bepaalt u of een tekenreeks eindigt met een waarde. De vergelijking is case-ongevoelig.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |tekenreeks |De waarde die het te vinden item bevat. |
| tekenreeksToFind |Ja |tekenreeks |De waarde om te vinden. |

### <a name="return-value"></a>Retourwaarde

**True** als het laatste teken of de laatste tekens van de tekenreeks overeenkomen met de waarde; anders, **False**.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) ziet u hoe u de functies startsWith en ends with gebruikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| startTrue | Booleaanse waarde | True |
| startsCapTrue | Booleaanse waarde | True |
| startFalse | Booleaanse waarde | False |
| eindigtTrue | Booleaanse waarde | True |
| endsCapTrue | Booleaanse waarde | True |
| eindigtFalse | Booleaanse waarde | False |

## <a name="first"></a>Eerste

`first(arg1)`

Geeft als resultaat het eerste teken van de tekenreeks of het eerste element van de array.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |array of tekenreeks |De waarde om het eerste element of teken op te halen. |

### <a name="return-value"></a>Retourwaarde

Een tekenreeks van het eerste teken of het type (tekenreeks, int, array of object) van het eerste element in een array.

### <a name="examples"></a>Voorbeelden

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

## <a name="format"></a>formaat

`format(formatString, arg1, arg2, ...)`

Hiermee maakt u een opgemaakte tekenreeks op basis van invoerwaarden.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| formatString | Ja | tekenreeks | De tekenreeks voor samengestelde opmaak. |
| arg1 | Ja | tekenreeks, geheel getal of booleaan | De waarde die moet worden opgenomen in de opgemaakte tekenreeks. |
| aanvullende argumenten | Nee | tekenreeks, geheel getal of booleaan | Extra waarden die u wilt opnemen in de opgemaakte tekenreeks. |

### <a name="remarks"></a>Opmerkingen

Gebruik deze functie om een tekenreeks in uw sjabloon op te maken. Het gebruikt dezelfde opmaakopties als de methode [System.String.Format](/dotnet/api/system.string.format) in .NET.

### <a name="examples"></a>Voorbeelden

In de volgende voorbeeldsjabloon ziet u hoe u de opmaakfunctie gebruikt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "greeting": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "name": {
            "type": "string",
            "defaultValue": "User"
        },
        "numberToFormat": {
            "type": "int",
            "defaultValue": 8175133
        }
    },
    "resources": [
    ],
    "outputs": {
        "formatTest": {
            "type": "string",
            "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| opmaakTest | Tekenreeks | Hallo, gebruiker. Opgemaakt getal: 8.175.133 |

## <a name="guid"></a>Guid

`guid(baseString, ...)`

Hiermee maakt u een waarde in de indeling van een wereldwijd unieke id op basis van de opgegeven waarden als parameters.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| basistekenreeks |Ja |tekenreeks |De waarde die wordt gebruikt in de hashfunctie om de GUID te maken. |
| extra parameters indien nodig |Nee |tekenreeks |U zoveel tekenreeksen toevoegen als nodig is om de waarde te maken die het niveau van uniciteit aangeeft. |

### <a name="remarks"></a>Opmerkingen

Deze functie is handig wanneer u een waarde moet maken in de indeling van een wereldwijd unieke id. U geeft parameterwaarden op die het bereik van uniciteit voor het resultaat beperken. U opgeven of de naam uniek is voor een abonnement, resourcegroep of implementatie.

De geretourneerde waarde is geen willekeurige tekenreeks, maar het resultaat van een hashfunctie op de parameters. De geretourneerde waarde is 36 tekens lang. Het is niet wereldwijd uniek. Als u een nieuwe GUID wilt maken die niet is gebaseerd op die hashwaarde van de parameters, gebruikt u de functie [newGuid.](#newguid)

In de volgende voorbeelden ziet u hoe u guid gebruikt om een unieke waarde te creëren voor veelgebruikte niveaus.

Uniek voor een abonnement

```json
"[guid(subscription().subscriptionId)]"
```

Uniek scoped voor resourcegroep

```json
"[guid(resourceGroup().id)]"
```

Uniek voor implementatie voor een resourcegroep

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Retourwaarde

Een tekenreeks met 36 tekens in de indeling van een wereldwijd unieke id.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) worden resultaten geretourneerd van guid:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "guidPerSubscription": {
            "value": "[guid(subscription().subscriptionId)]",
            "type": "string"
        },
        "guidPerResourceGroup": {
            "value": "[guid(resourceGroup().id)]",
            "type": "string"
        },
        "guidPerDeployment": {
            "value": "[guid(resourceGroup().id, deployment().name)]",
            "type": "string"
        }
    }
}
```

## <a name="indexof"></a>indexVan

`indexOf(stringToSearch, stringToFind)`

Geeft als resultaat de eerste positie van een waarde binnen een tekenreeks. De vergelijking is case-ongevoelig.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |tekenreeks |De waarde die het te vinden item bevat. |
| tekenreeksToFind |Ja |tekenreeks |De waarde om te vinden. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de positie van het te vinden item vertegenwoordigt. De waarde is op nul gebaseerd. Als het item niet is gevonden, wordt -1 geretourneerd.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) ziet u hoe u de functies indexOf en lastIndexOf gebruikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString firstString FirstString FirstString | Int | 2 |
| laatstetekenreeks | Int | 0 |
| notFound notFound | Int | -1 |

## <a name="last"></a>Laatste

`last (arg1)`

Geeft als resultaat het laatste teken van de tekenreeks of het laatste element van de array.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |array of tekenreeks |De waarde om het laatste element of teken op te halen. |

### <a name="return-value"></a>Retourwaarde

Een tekenreeks van het laatste teken of het type (tekenreeks, int, array of object) van het laatste element in een array.

### <a name="examples"></a>Voorbeelden

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

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Geeft als resultaat de laatste positie van een waarde binnen een tekenreeks. De vergelijking is case-ongevoelig.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |tekenreeks |De waarde die het te vinden item bevat. |
| tekenreeksToFind |Ja |tekenreeks |De waarde om te vinden. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de laatste positie van het te vinden item vertegenwoordigt. De waarde is op nul gebaseerd. Als het item niet is gevonden, wordt -1 geretourneerd.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) ziet u hoe u de functies indexOf en lastIndexOf gebruikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString firstString FirstString FirstString | Int | 2 |
| laatstetekenreeks | Int | 0 |
| notFound notFound | Int | -1 |

## <a name="length"></a>lengte

`length(string)`

Geeft als resultaat het aantal tekens in een tekenreeks, elementen in een array of eigenschappen op basisniveau in een object.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |array, tekenreeks of object |De array die moet worden gebruikt voor het verkrijgen van het aantal elementen, de tekenreeks die moet worden gebruikt voor het verkrijgen van het aantal tekens of het object dat moet worden gebruikt voor het verkrijgen van het aantal eigenschappen op basisniveau. |

### <a name="return-value"></a>Retourwaarde

Een int. 

### <a name="examples"></a>Voorbeelden

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

## <a name="newguid"></a>nieuwGuid

`newGuid()`

Geeft als resultaat een waarde in de notatie van een wereldwijd unieke id. **Deze functie kan alleen worden gebruikt in de standaardwaarde voor een parameter.**

### <a name="remarks"></a>Opmerkingen

U deze functie alleen gebruiken in een expressie voor de standaardwaarde van een parameter. Als u deze functie ergens anders in een sjabloon gebruikt, wordt een fout geretourneerd. De functie is niet toegestaan in andere delen van de sjabloon omdat deze elke keer dat deze wordt aangeroepen een andere waarde retourneert. Het implementeren van dezelfde sjabloon met dezelfde parameters zou niet betrouwbaar dezelfde resultaten opleveren.

De nieuweGuid-functie verschilt van de [guid-functie](#guid) omdat er geen parameters voor nodig zijn. Wanneer u guid aanroept met dezelfde parameter, wordt telkens dezelfde id geretourneerd. Gebruik guid wanneer u op betrouwbare wijze dezelfde GUID moet genereren voor een specifieke omgeving. Gebruik newGuid wanneer u telkens een andere id nodig hebt, zoals het implementeren van resources naar een testomgeving.

De nieuweGuid-functie gebruikt de [Guid-structuur](/dotnet/api/system.guid) in het .NET Framework om de wereldwijd unieke id te genereren.

Als u de optie gebruikt [om een eerdere succesvolle implementatie opnieuw te implementeren](rollback-on-error.md)en de eerdere implementatie een parameter bevat die newGuid gebruikt, wordt de parameter niet opnieuw geëvalueerd. In plaats daarvan wordt de parameterwaarde van de eerdere implementatie automatisch opnieuw gebruikt in de rollback-implementatie.

In een testomgeving moet u mogelijk herhaaldelijk resources implementeren die slechts korte tijd leven. In plaats van het construeren van unieke namen, u newGuid met [uniqueString](#uniquestring) gebruiken om unieke namen te maken.

Wees voorzichtig met het opnieuw implementeren van een sjabloon die is gebaseerd op de nieuweGuid-functie voor een standaardwaarde. Wanneer u de parameter opnieuw implementeert en geen waarde voor de parameter geeft, wordt de functie opnieuw geëvalueerd. Als u een bestaande resource wilt bijwerken in plaats van een nieuwe resource wilt maken, geeft u de parameterwaarde door van de eerdere implementatie.

### <a name="return-value"></a>Retourwaarde

Een tekenreeks met 36 tekens in de indeling van een wereldwijd unieke id.

### <a name="examples"></a>Voorbeelden

In de volgende voorbeeldsjabloon wordt een parameter met een nieuwe id weergegeven.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "guidOutput": {
            "type": "string",
            "value": "[parameters('guidValue')]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld varieert voor elke implementatie, maar is vergelijkbaar met:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| guidOutput | tekenreeks | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

In het volgende voorbeeld wordt de functie newGuid gebruikt om een unieke naam voor een opslagaccount te maken. Deze sjabloon werkt mogelijk voor een testomgeving waarin het opslagaccount korte tijd bestaat en niet opnieuw wordt geïmplementeerd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "variables": {
        "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "West US",
            "apiVersion": "2018-07-01",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "nameOutput": {
            "type": "string",
            "value": "[variables('storageName')]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld varieert voor elke implementatie, maar is vergelijkbaar met:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| naamUitvoer | tekenreeks | storagenziwvyru7uxie |


## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Geeft als resultaat een tekenreeks met rechts uitgelijnde tekenreeks door tekens aan de linkerkant toe te voegen totdat de totale opgegeven lengte wordt bereikt.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| valueToPad |Ja |tekenreeks of int |De waarde om rechts uit te lijnen. |
| totalLengte |Ja |int |Het totale aantal tekens in de geretourneerde tekenreeks. |
| paddingTeken |Nee |één teken |Het teken dat moet worden gebruikt voor links-opvulling totdat de totale lengte is bereikt. De standaardwaarde is een spatie. |

Als de oorspronkelijke tekenreeks langer is dan het aantal tekens dat moet worden gevonden, worden er geen tekens toegevoegd.

### <a name="return-value"></a>Retourwaarde

Een tekenreeks met ten minste het aantal opgegeven tekens.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) ziet u hoe u de door de gebruiker opgegeven parameterwaarde paderen door het nulteken toe te voegen totdat het totale aantal tekens is bereikt. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123"
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[padLeft(parameters('testString'),10,'0')]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| tekenreeksUitvoer | Tekenreeks | 0000000123 |

## <a name="replace"></a>vervangen

`replace(originalString, oldString, newString)`

Retourneert een nieuwe tekenreeks met alle exemplaren van een tekenreeks die door een andere tekenreeks zijn vervangen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| origineletekenreeks |Ja |tekenreeks |De waarde waarin alle exemplaren van een tekenreeks worden vervangen door een andere tekenreeks. |
| oldString (oldString) |Ja |tekenreeks |De tekenreeks die uit de oorspronkelijke tekenreeks moet worden verwijderd. |
| newString |Ja |tekenreeks |De tekenreeks die moet worden toegevoegd in plaats van de verwijderde tekenreeks. |

### <a name="return-value"></a>Retourwaarde

Een tekenreeks met de vervangen tekens.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) ziet u hoe u alle streepjes uit de door de gebruiker verstrekte tekenreeks verwijdert en hoe u een deel van de tekenreeks vervangen door een andere tekenreeks.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123-123-1234"
        }
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'-', '')]"
        },
        "secondOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| eerstOutput | Tekenreeks | 1231231234 |
| secondOutput | Tekenreeks | 123-123-xxxx |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Retourneert een tekenreeks met alle tekens na het opgegeven aantal tekens of een array met alle elementen na het opgegeven aantal elementen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Originalvalue |Ja |array of tekenreeks |De array of tekenreeks die u wilt gebruiken om over te slaan. |
| nummerToSkip |Ja |int |Het aantal elementen of tekens dat moet worden overgeslagen. Als deze waarde 0 of minder is, worden alle elementen of tekens in de waarde geretourneerd. Als deze groter is dan de lengte van de array of tekenreeks, wordt een lege array of tekenreeks geretourneerd. |

### <a name="return-value"></a>Retourwaarde

Een array of tekenreeks.

### <a name="examples"></a>Voorbeelden

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

## <a name="split"></a>split

`split(inputString, delimiter)`

Retourneert een array met tekenreeksen die de subtekenreeksen van de invoertekenreeks bevat die zijn afgebakend door de opgegeven scheidingstekens.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| invoertekenreeks |Ja |tekenreeks |De snaar om te splitsen. |
| scheidingsteken |Ja |tekenreeks of reeks tekenreeksen |De scheidingsteken die moet worden gebruikt voor het splitsen van de tekenreeks. |

### <a name="return-value"></a>Retourwaarde

Een scala aan snaren.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) wordt de invoertekenreeks gesplitst met een komma en met een komma of een puntkomma.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstString": {
            "type": "string",
            "defaultValue": "one,two,three"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "one;two,three"
        }
    },
    "variables": {
        "delimiters": [ ",", ";" ]
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "array",
            "value": "[split(parameters('firstString'),',')]"
        },
        "secondOutput": {
            "type": "array",
            "value": "[split(parameters('secondString'),variables('delimiters'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| eerstOutput | Matrix | ["één", "twee", "drie"] |
| secondOutput | Matrix | ["één", "twee", "drie"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Hiermee bepaalt u of een tekenreeks begint met een waarde. De vergelijking is case-ongevoelig.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |tekenreeks |De waarde die het te vinden item bevat. |
| tekenreeksToFind |Ja |tekenreeks |De waarde om te vinden. |

### <a name="return-value"></a>Retourwaarde

**True** als het eerste teken of de eerste tekens van de tekenreeks overeenkomen met de waarde; anders, **False**.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) ziet u hoe u de functies startsWith en ends with gebruikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| startTrue | Booleaanse waarde | True |
| startsCapTrue | Booleaanse waarde | True |
| startFalse | Booleaanse waarde | False |
| eindigtTrue | Booleaanse waarde | True |
| endsCapTrue | Booleaanse waarde | True |
| eindigtFalse | Booleaanse waarde | False |

## <a name="string"></a>tekenreeks

`string(valueToConvert)`

Hiermee converteert u de opgegeven waarde naar een tekenreeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ja | Alle |De waarde die u wilt omzetten in tekenreeks. Elk type waarde kan worden geconverteerd, inclusief objecten en arrays. |

### <a name="return-value"></a>Retourwaarde

Een tekenreeks van de geconverteerde waarde.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) ziet u hoe u verschillende typen waarden converteert naar tekenreeksen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testObject": {
            "type": "object",
            "defaultValue": {
                "valueA": 10,
                "valueB": "Example Text"
            }
        },
        "testArray": {
            "type": "array",
            "defaultValue": [
                "a",
                "b",
                "c"
            ]
        },
        "testInt": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "resources": [],
    "outputs": {
        "objectOutput": {
            "type": "string",
            "value": "[string(parameters('testObject'))]"
        },
        "arrayOutput": {
            "type": "string",
            "value": "[string(parameters('testArray'))]"
        },
        "intOutput": {
            "type": "string",
            "value": "[string(parameters('testInt'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| objectOutput | Tekenreeks | {"valueA":10,"valueB":"Voorbeeldtekst"} |
| arrayOutput | Tekenreeks | ["a","b","c"] |
| intOutput | Tekenreeks | 5 |

## <a name="substring"></a>Subtekenreeks

`substring(stringToParse, startIndex, length)`

Retourneert een subtekenreeks die begint bij de opgegeven tekenpositie en het opgegeven aantal tekens bevat.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| stringToParse |Ja |tekenreeks |De oorspronkelijke tekenreeks waaruit de subtekenreeks wordt geëxtraheerd. |
| Startindex |Nee |int |De op nul gebaseerde begintekenpositie voor de subtekenreeks. |
| lengte |Nee |int |Het aantal tekens voor de subtekenreeks. Moet verwijzen naar een locatie in de tekenreeks. Moet nul of meer zijn. |

### <a name="return-value"></a>Retourwaarde

De substring. Of een lege tekenreeks als de lengte nul is.

### <a name="remarks"></a>Opmerkingen

De functie mislukt wanneer de subtekenreeks zich uitstrekt tot voorbij het einde van de tekenreeks of wanneer de lengte kleiner is dan nul. In het volgende voorbeeld wordt de fout 'De index- en lengteparameters moeten verwijzen naar een locatie binnen de tekenreeks' mislukt. De indexparameter: '0', de lengteparameter: '11', de lengte van de tekenreeksparameter: '10'.".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) wordt een subtekenreeks uit een parameter geëxtraheerd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        }
    },
    "resources": [],
    "outputs": {
        "substringOutput": {
            "value": "[substring(parameters('testString'), 4, 3)]",
            "type": "string"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| subtekenreeksUitvoer | Tekenreeks | twee |

## <a name="take"></a>Nemen

`take(originalValue, numberToTake)`

Retourneert een tekenreeks met het opgegeven aantal tekens vanaf het begin van de tekenreeks of een array met het opgegeven aantal elementen vanaf het begin van de array.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Originalvalue |Ja |array of tekenreeks |De array of tekenreeks om de elementen uit te nemen. |
| nummerToTake |Ja |int |Het aantal elementen of tekens dat u moet nemen. Als deze waarde 0 of minder is, wordt een lege array of tekenreeks geretourneerd. Als deze groter is dan de lengte van de opgegeven array of tekenreeks, worden alle elementen in de array of tekenreeks geretourneerd. |

### <a name="return-value"></a>Retourwaarde

Een array of tekenreeks.

### <a name="examples"></a>Voorbeelden

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

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Hiermee converteert u de opgegeven tekenreeks naar kleine letters.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| stringToChange |Ja |tekenreeks |De waarde die moet worden omgezet in kleine letters. |

### <a name="return-value"></a>Retourwaarde

De tekenreeks is omgezet in kleine letters.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) wordt een parameterwaarde geconverteerd naar kleine letters en hoofdletters.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| toLowerOutput | Tekenreeks | Een twee drie |
| toUpperOutput | Tekenreeks | Een twee drie |

## <a name="toupper"></a>Toupper

`toUpper(stringToChange)`

Hiermee converteert u de opgegeven tekenreeks naar hoofdletters.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| stringToChange |Ja |tekenreeks |De waarde die moet worden omgezet in hoofdletters. |

### <a name="return-value"></a>Retourwaarde

De tekenreeks is omgezet in hoofdletters.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) wordt een parameterwaarde geconverteerd naar kleine letters en hoofdletters.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| toLowerOutput | Tekenreeks | Een twee drie |
| toUpperOutput | Tekenreeks | Een twee drie |

## <a name="trim"></a>Trim

`trim (stringToTrim)`

Hiermee verwijdert u alle voorloop- en achterliggende witruimtetekens uit de opgegeven tekenreeks.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| stringToTrim |Ja |tekenreeks |De waarde om bij te snijden. |

### <a name="return-value"></a>Retourwaarde

De tekenreeks zonder voorloop- en slepende witruimtetekens.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) worden de witspatietekens uit de parameter bijgemaakt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "    one two three   "
        }
    },
    "resources": [],
    "outputs": {
        "return": {
            "type": "string",
            "value": "[trim(parameters('testString'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| terugkeren | Tekenreeks | Een twee drie |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Hiermee maakt u een deterministische hash-tekenreeks op basis van de opgegeven waarden als parameters. 

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| basistekenreeks |Ja |tekenreeks |De waarde die wordt gebruikt in de hashfunctie om een unieke tekenreeks te maken. |
| extra parameters indien nodig |Nee |tekenreeks |U zoveel tekenreeksen toevoegen als nodig is om de waarde te maken die het niveau van uniciteit aangeeft. |

### <a name="remarks"></a>Opmerkingen

Deze functie is handig wanneer u een unieke naam voor een resource moet maken. U geeft parameterwaarden op die het bereik van uniciteit voor het resultaat beperken. U opgeven of de naam uniek is voor een abonnement, resourcegroep of implementatie. 

De geretourneerde waarde is geen willekeurige tekenreeks, maar het resultaat van een hash-functie. De geretourneerde waarde is 13 tekens lang. Het is niet wereldwijd uniek. U de waarde combineren met een voorvoegsel van uw naamgevingsconventie om een naam te maken die zinvol is. In het volgende voorbeeld wordt de opmaak van de geretourneerde waarde weergegeven. De werkelijke waarde varieert per gegeven parameters.

    tcvhiyu5h2o5o

In de volgende voorbeelden ziet u hoe u uniqueString gebruiken om een unieke waarde te creëren voor veelgebruikte niveaus.

Uniek voor een abonnement

```json
"[uniqueString(subscription().subscriptionId)]"
```

Uniek scoped voor resourcegroep

```json
"[uniqueString(resourceGroup().id)]"
```

Uniek voor implementatie voor een resourcegroep

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

In het volgende voorbeeld ziet u hoe u een unieke naam maakt voor een opslagaccount op basis van uw resourcegroep. Binnen de resourcegroep is de naam niet uniek als deze op dezelfde manier is gebouwd.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Als u elke keer dat u een sjabloon implementeert een nieuwe unieke naam moet maken en niet van plan bent de bron bij te werken, u de [utcNow-functie](template-functions-date.md#utcnow) met unieke Tekenreeks gebruiken. Je zou deze aanpak kunnen gebruiken in een testomgeving. Zie [utcNow](template-functions-date.md#utcnow)voor een voorbeeld .

### <a name="return-value"></a>Retourwaarde

Een tekenreeks met 13 tekens.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) worden de resultaten geretourneerd van de unieke tekenreeks:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

## <a name="uri"></a>Uri

`uri (baseUri, relativeUri)`

Creëert een absolute URI door de baseUri en de relativeUri-tekenreeks te combineren.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| baseUri |Ja |tekenreeks |De basis uri string. Let op het gedrag met betrekking tot de afhandeling van de trailing slash ('/'), zoals beschreven naar aanleiding van deze tabel.  |
| relativeUri |Ja |tekenreeks |De relatieve uri string toe te voegen aan de basis uri string. |

* Als **baseUri** eindigt in een trailing slash, het resultaat is gewoon **baseUri** gevolgd door **relativeUri**.

* Als **baseUri** niet eindigt in een trailing slash een van de twee dingen gebeurt.  

   * Als **baseUri** heeft geen slashes op alle (afgezien van de "/" in de buurt van de voorkant) het resultaat is gewoon **baseUri** gevolgd door **relativeUri**.

   * Als **baseUri** heeft een aantal slashes, maar eindigt niet met een slash, alles van de laatste slash verder wordt verwijderd uit **baseUri** en het resultaat is **baseUri** gevolgd door **relativeUri**.
     
Hier volgen enkele voorbeelden:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
Voor volledige details worden de **parameters baseUri** en **relativeuri** opgelost zoals gespecificeerd in [RFC 3986, sectie 5](https://tools.ietf.org/html/rfc3986#section-5).

### <a name="return-value"></a>Retourwaarde

Een tekenreeks die de absolute URI voor de basis- en relatieve waarden vertegenwoordigt.

### <a name="examples"></a>Voorbeelden

In het volgende voorbeeld ziet u hoe u een koppeling naar een geneste sjabloon maakt op basis van de waarde van de bovenliggende sjabloon.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) ziet u hoe u uri, uriComponent en uriComponentToString gebruikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| uriOutput | Tekenreeks | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Tekenreeks | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Tekenreeks | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Codeert een URI.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| stringToEncode |Ja |tekenreeks |De waarde die moet worden gecodeerd. |

### <a name="return-value"></a>Retourwaarde

Een tekenreeks van de URI gecodeerde waarde.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) ziet u hoe u uri, uriComponent en uriComponentToString gebruikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| uriOutput | Tekenreeks | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Tekenreeks | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Tekenreeks | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Retourneert een tekenreeks van een URI-gecodeerde waarde.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Ja |tekenreeks |De URI-gecodeerde waarde om te converteren naar een tekenreeks. |

### <a name="return-value"></a>Retourwaarde

Een gedecodeerde tekenreeks met uri-gecodeerde waarde.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) ziet u hoe u uri, uriComponent en uriComponentToString gebruikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| uriOutput | Tekenreeks | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | Tekenreeks | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Tekenreeks | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>Volgende stappen
* Zie [Sjablonen voor Azure Resource Manager ontwerpen](template-syntax.md)voor een beschrijving van de secties in een Azure Resource Manager-sjabloon.
* Zie [Gekoppelde sjablonen gebruiken met Azure Resource Manager](linked-templates.md)als u meerdere sjablonen wilt samenvoegen.
* Zie Meerdere exemplaren van resources maken [in Azure Resource Manager](copy-resources.md)als u een bepaald aantal keren wilt herhalen bij het maken van een type resource.
* Zie Een toepassing implementeren [met Azure Resource Manager-sjabloon](deploy-powershell.md)als u wilt zien hoe u de sjabloon implementeren die u hebt gemaakt.

