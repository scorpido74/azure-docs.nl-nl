---
title: Sjabloonfuncties - numeriek
description: Beschrijft de functies die u moet gebruiken in een Azure Resource Manager-sjabloon om met getallen te werken.
ms.topic: conceptual
ms.date: 11/08/2017
ms.openlocfilehash: 2ca5c539036d002b83b8141132a0ebf2530dc6af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156341"
---
# <a name="numeric-functions-for-arm-templates"></a>Numerieke functies voor ARM-sjablonen

Resourcebeheer biedt de volgende functies voor het werken met gehele getallen in uw Azure Resource Manager-sjabloon (ARM):

* [Toevoegen](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [Int](#int)
* [Max](#max)
* [Min](#min)
* [Mod](#mod)
* [Mul](#mul)
* [sub](#sub)

<a id="add" />

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="add"></a>add
`add(operand1, operand2)`

Geeft als resultaat de som van de twee verstrekte gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
|operand1 |Ja |int |Eerste nummer toe te voegen. |
|operand2 |Ja |int |Tweede nummer toe te voegen. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de som van de parameters bevat.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) worden twee parameters toegevoegd.

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

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| addResult | Int | 8 |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

Geeft als resultaat de index van een iteratielus.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| loopNaam | Nee | tekenreeks | De naam van de lus voor het verkrijgen van de iteratie. |
| offset |Nee |int |Het getal dat moet worden toegevoegd aan de iteratiewaarde op basis van nul. |

### <a name="remarks"></a>Opmerkingen

Deze functie wordt altijd gebruikt met een **kopieerobject.** Als er geen waarde is opgegeven voor **compensatie,** wordt de huidige iteratiewaarde geretourneerd. De iteratiewaarde begint bij nul. U iteratielussen gebruiken bij het definiëren van resources of variabelen.

Met de eigenschap **loopName** u opgeven of copyIndex verwijst naar een resourceiteratie of eigenschapsiteratie. Als er geen waarde is opgegeven voor **loopName,** wordt de huidige iteratie van het resourcetype gebruikt. Geef een waarde op voor **loopName** wanneer u een eigenschap wijzigt.

Zie Meerdere exemplaren van resources maken [in Azure Resource Manager](copy-resources.md)voor een volledige beschrijving van hoe u **copyIndex**gebruikt.

Zie [Variabelen](template-syntax.md#variables)voor een voorbeeld van het gebruik van **copyIndex** bij het definiëren van een variabele.

### <a name="example"></a>Voorbeeld

In het volgende voorbeeld ziet u een kopieerlus en de indexwaarde die in de naam is opgenomen.

```json
"resources": [
  {
    "name": "[concat('examplecopy-', copyIndex())]",
    "type": "Microsoft.Web/sites",
    "copy": {
      "name": "websitescopy",
      "count": "[parameters('count')]"
    },
    ...
  }
]
```

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de huidige index van de iteratie vertegenwoordigt.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

Geeft als resultaat de gehele verdeling van de twee verstrekte gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |int |Het aantal wordt verdeeld. |
| operand2 |Ja |int |Het getal dat wordt gebruikt om te verdelen. Kan niet 0 zijn. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de divisie vertegenwoordigt.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) wordt de ene parameter door een andere parameter verdeeld.

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

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| divResult divResult | Int | 2 |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

<a id="float" />

## <a name="float"></a>float
`float(arg1)`

Hiermee converteert u de waarde naar een zwevend puntnummer. U gebruikt deze functie alleen wanneer u aangepaste parameters doorgeeft aan een toepassing, zoals een Logische App.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |tekenreeks of int |De waarde die moet worden omgezet naar een zwevend puntnummer. |

### <a name="return-value"></a>Retourwaarde
Een zwevend puntnummer.

### <a name="example"></a>Voorbeeld

In het volgende voorbeeld ziet u hoe u float gebruikt om parameters door te geven aan een Logic App:

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

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

Hiermee converteert u de opgegeven waarde naar een geheel getal.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ja |tekenreeks of int |De waarde die moet worden omgezet in een geheel getal. |

### <a name="return-value"></a>Retourwaarde

Een geheel van de geconverteerde waarde.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) wordt de door de gebruiker opgegeven parameterwaarde geconverteerd naar een geheel getal.

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

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| intResult | Int | 4 |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>Max
`max (arg1)`

Geeft als resultaat de maximale waarde van een matrix van gehele getallen of een door komma's gescheiden lijst met gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |array van gehele getallen, of door komma's gescheiden lijst van gehele getallen |De collectie om de maximale waarde te krijgen. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de maximale waarde uit de verzameling vertegenwoordigt.

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

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>min.
`min (arg1)`

Geeft als resultaat de minimumwaarde van een matrix van gehele getallen of een door komma's gescheiden lijst met gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |array van gehele getallen, of door komma's gescheiden lijst van gehele getallen |De collectie om de minimale waarde te krijgen. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de minimumwaarde uit de verzameling vertegenwoordigt.

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

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>Mod
`mod(operand1, operand2)`

Geeft als resultaat de rest van de gehele verdeling met behulp van de twee verstrekte gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |int |Het aantal wordt verdeeld. |
| operand2 |Ja |int |Het getal dat wordt gebruikt om te verdelen, kan niet 0 zijn. |

### <a name="return-value"></a>Retourwaarde
Een geheel getal dat de rest vertegenwoordigt.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) wordt de rest van het delen van de ene parameter door een andere parameter geretourneerd.

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

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| modResult modResult | Int | 1 |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>Mul
`mul(operand1, operand2)`

Geeft als resultaat de vermenigvuldiging van de twee verstrekte gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |int |Eerste getal dat zich vermenigvuldigt. |
| operand2 |Ja |int |Tweede getal om te vermenigvuldigen. |

### <a name="return-value"></a>Retourwaarde

Een geheel getal dat de vermenigvuldiging vertegenwoordigt.

### <a name="example"></a>Voorbeeld

Met de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) wordt de ene parameter vermenigvuldigd met een andere parameter.

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

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| mulResultaat | Int | 15 |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

Geeft als resultaat de aftrekking van de twee verstrekte gehele getallen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |int |Het getal dat wordt afgetrokken van. |
| operand2 |Ja |int |Het getal dat wordt afgetrokken. |

### <a name="return-value"></a>Retourwaarde
Een geheel getal dat de aftrekking vertegenwoordigt.

### <a name="example"></a>Voorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) wordt één parameter van een andere parameter afgetrokken.

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

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| subResultaat | Int | 4 |

Als u deze voorbeeldsjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

Gebruik het als volgt om deze voorbeeldsjabloon met PowerShell te implementeren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>Volgende stappen
* Zie [Sjablonen voor Azure Resource Manager ontwerpen](template-syntax.md)voor een beschrijving van de secties in een Azure Resource Manager-sjabloon.
* Zie [Gekoppelde sjablonen gebruiken met Azure Resource Manager](linked-templates.md)als u meerdere sjablonen wilt samenvoegen.
* Zie Meerdere exemplaren van resources maken [in Azure Resource Manager](copy-resources.md)als u een bepaald aantal keren wilt herhalen bij het maken van een type resource.
* Zie Een toepassing implementeren met Azure [Resource Manager-sjabloon](deploy-powershell.md)als u wilt zien hoe u de sjabloon implementeren die u hebt gemaakt.

