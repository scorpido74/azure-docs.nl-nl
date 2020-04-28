---
title: Sjabloon functies-numeriek
description: Hierin worden de functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon om te werken met getallen.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: dc15ade453fc5ea4dc031ced0377892f4f8cf27d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192345"
---
# <a name="numeric-functions-for-arm-templates"></a>Numerieke functies voor ARM-sjablonen

Resource Manager biedt de volgende functies voor het werken met gehele getallen in uw Azure Resource Manager ARM-sjabloon:

* [toe](#add)
* [Functie copyindex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [aantal](#max)
* [Haal](#min)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

## <a name="add"></a>add

`add(operand1, operand2)`

Retourneert de som van de twee door gegeven gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
|operand1 |Ja |int |Het eerste nummer dat moet worden toegevoegd. |
|operand2 |Ja |int |Het tweede nummer dat moet worden toegevoegd. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de som van de para meters bevat.

### <a name="example"></a>Voorbeeld

Met de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) worden twee para meters toegevoegd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| addResult | Int | 8 |

## <a name="copyindex"></a>Functie copyindex

`copyIndex(loopName, offset)`

Retourneert de index van een herhalings lus.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| lusinstructie | Nee | tekenreeks | De naam van de lus voor het ophalen van de iteratie. |
| offset |Nee |int |Het getal dat moet worden toegevoegd aan de op nul gebaseerde iteratie waarde. |

### <a name="remarks"></a>Opmerkingen

Deze functie wordt altijd gebruikt met een **Kopieer** object. Als er geen waarde wordt opgegeven voor **Offset**, wordt de huidige iteratie waarde geretourneerd. De iteratie waarde begint bij nul.

Met de eigenschap **lusinstructie** kunt u opgeven of functie copyindex verwijst naar een resource herhaling of eigenschaps herhaling. Als er geen waarde wordt gegeven voor de **lusinstructie**, wordt het huidige bron type herhaling gebruikt. Geef een waarde voor de **lusinstructie** op wanneer u een eigenschap wilt herhalen.

Zie voor meer informatie over het gebruik van kopiëren:

* [Resource iteratie in ARM-sjablonen](copy-resources.md)
* [Eigenschaps herhaling in ARM-sjablonen](copy-properties.md)
* [Variabele herhaling in ARM-sjablonen](copy-variables.md)
* [Uitvoer herhaling in ARM-sjablonen](copy-outputs.md)

### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u een lus Copy en de index waarde die is opgenomen in de naam.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {}
}
```

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de huidige index van de herhaling vertegenwoordigt.

## <a name="div"></a>div

`div(operand1, operand2)`

Retourneert de deling van het gehele getal van de twee door gegeven gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |int |Het getal dat wordt verdeeld. |
| operand2 |Ja |int |Het getal dat wordt gebruikt om te delen. Mag niet 0 zijn. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de deling vertegenwoordigt.

### <a name="example"></a>Voorbeeld

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) deelt één para meter door een andere para meter.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 8,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| divResult | Int | 2 |

## <a name="float"></a>float

`float(arg1)`

Zet de waarde om in een getal met drijvende komma. U kunt deze functie alleen gebruiken bij het door geven van aangepaste para meters aan een toepassing, zoals een logische app.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |teken reeks of int |De waarde die moet worden geconverteerd naar een getal met een drijvende komma. |

### <a name="return-value"></a>Retourwaarde

Een drijvende-komma waarde.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u hoe float wordt gebruikt om para meters door te geven aan een logische app:

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
            "custom1": {
                "value": "[float('3.0')]"
            },
            "custom2": {
                "value": "[float(3)]"
            },
```

## <a name="int"></a>int

`int(valueToConvert)`

Hiermee wordt de opgegeven waarde geconverteerd naar een geheel getal.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ja |teken reeks of int |De waarde die moet worden geconverteerd naar een geheel getal. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal van de geconverteerde waarde.

### <a name="example"></a>Voorbeeld

Met de volgende [voorbeeld sjabloon wordt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) de door de gebruiker ingevoerde parameter waarde geconverteerd naar een geheel getal.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToConvert": {
            "type": "string",
            "defaultValue": "4"
        }
    },
    "resources": [
    ],
    "outputs": {
        "intResult": {
            "type": "int",
            "value": "[int(parameters('stringToConvert'))]"
        }
    }
}
```

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| intResult | Int | 4 |

## <a name="max"></a>aantal

`max (arg1)`

Retourneert de maximum waarde van een matrix met gehele getallen of een door komma's gescheiden lijst met gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |matrix van gehele getallen of door komma's gescheiden lijst met gehele getallen |De verzameling om de maximum waarde op te halen. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de maximum waarde uit de verzameling voor stelt.

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

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>min.

`min (arg1)`

Retourneert de minimum waarde van een matrix met gehele getallen of een door komma's gescheiden lijst met gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |matrix van gehele getallen of door komma's gescheiden lijst met gehele getallen |De verzameling om de minimum waarde op te halen. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de minimum waarde uit de verzameling voor stelt.

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

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="mod"></a>mod

`mod(operand1, operand2)`

Retourneert de rest van de deling van het gehele getal met behulp van de twee door gegeven gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |int |Het getal dat wordt verdeeld. |
| operand2 |Ja |int |Het getal dat wordt gebruikt om te delen, mag niet 0 zijn. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat het restant voor stelt.

### <a name="example"></a>Voorbeeld

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) retourneert het restant van het delen van één para meter door een andere para meter.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| modResult | Int | 1 |

## <a name="mul"></a>mul

`mul(operand1, operand2)`

Retourneert de vermenigvuldiging van de twee door gegeven gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |int |Het eerste getal dat moet worden vermenigvuldigd. |
| operand2 |Ja |int |Het tweede getal dat moet worden vermenigvuldigd. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de vermenigvuldiging vertegenwoordigt.

### <a name="example"></a>Voorbeeld

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) vermenigvuldigt één para meter met een andere para meter.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

## <a name="sub"></a>sub

`sub(operand1, operand2)`

Retourneert de aftrekking van de twee door gegeven gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |int |Het getal dat wordt afgetrokken van. |
| operand2 |Ja |int |Het getal dat wordt afgetrokken. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de aftrekking vertegenwoordigt.

### <a name="example"></a>Voorbeeld

Met de volgende [voorbeeld sjabloon wordt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) een para meter van een andere para meter afgetrokken.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| subresultaat | Int | 4 |

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor een beschrijving van de secties in een Azure Resource Manager sjabloon.
* Als u een bepaald aantal keer wilt herhalen bij het maken van een type resource, raadpleegt u [meerdere exemplaren van resources maken in azure Resource Manager](copy-resources.md).
