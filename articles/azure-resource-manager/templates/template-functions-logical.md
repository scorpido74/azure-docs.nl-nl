---
title: Sjabloon functies-logisch
description: Hierin worden de functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon om logische waarden te bepalen.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 0072593e7d7830e75e2386bcfdd2907a873c7a87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192311"
---
# <a name="logical-functions-for-arm-templates"></a>Logische functies voor ARM-sjablonen

Resource Manager biedt verschillende functies voor het maken van vergelijkingen in uw Azure Resource Manager-sjablonen (ARM).

* [maar](#and)
* [booleaans](#bool)
* [If](#if)
* [ten](#not)
* [of](#or)

## <a name="and"></a>en

`and(arg1, arg2, ...)`

Controleert of alle parameter waarden waar zijn.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |booleaans |De eerste waarde om te controleren of deze waar is. |
| Arg2 |Ja |booleaans |De tweede waarde om te controleren of waar is. |
| aanvullende argumenten |Nee |booleaans |Aanvullende argumenten om te controleren of deze waar zijn. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als alle waarden waar zijn. anders **False**.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ziet u hoe u logische functies gebruikt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

De uitvoer van het vorige voor beeld is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| andExampleOutput | Booleaanse waarde | False |
| orExampleOutput | Booleaanse waarde | True |
| notExampleOutput | Booleaanse waarde | False |

## <a name="bool"></a>booleaans

`bool(arg1)`

Zet de para meter om in een Boole-waarde.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |teken reeks of int |De waarde die moet worden geconverteerd naar een Boole. |

### <a name="return-value"></a>Retourwaarde
Een Boolean van de geconverteerde waarde.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) ziet u hoe u BOOL gebruikt met een teken reeks of een geheel getal.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| trueString | Booleaanse waarde | True |
| falseString | Booleaanse waarde | False |
| trueInt | Booleaanse waarde | True |
| falseInt | Booleaanse waarde | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Retourneert een waarde op basis van het feit of een voor waarde waar of onwaar is.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| regeling |Ja |booleaans |De waarde om te controleren of deze True of False is. |
| trueValue |Ja | teken reeks, int, object of matrix |De waarde die moet worden geretourneerd als de voor waarde waar is. |
| falseValue |Ja | teken reeks, int, object of matrix |De waarde die moet worden geretourneerd als de voor waarde ONWAAR is. |

### <a name="return-value"></a>Retourwaarde

Retourneert een tweede para meter wanneer de eerste para meter **True**is; anders retourneert de derde para meter.

### <a name="remarks"></a>Opmerkingen

Als de voor waarde **waar**is, wordt alleen de waarde True geëvalueerd. Als de voor waarde **Onwaar**is, wordt alleen de waarde ONWAAR geëvalueerd. Met de functie **als** kunt u expressies toevoegen die alleen voorwaarde geldig zijn. U kunt bijvoorbeeld verwijzen naar een resource die zich onder één voor waarde bevindt, maar niet onder de andere voor waarde. In de volgende sectie ziet u een voor beeld van een conditioneel evalueren van expressies.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) ziet u hoe u `if` de functie gebruikt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

De uitvoer van het vorige voor beeld is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| yesOutput | Tekenreeks | ja |
| geen uitvoer | Tekenreeks | nee |
| objectOutput | Object | {"test": "waarde1"} |

In de volgende [voorbeeld sjabloon](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) ziet u hoe u deze functie kunt gebruiken met expressies die alleen voorwaarde geldig zijn.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "logAnalytics": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "condition": "[not(empty(parameters('logAnalytics')))]",
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>not

`not(arg1)`

Zet Boole-waarde om in tegenovergestelde waarde.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |booleaans |De waarde die moet worden geconverteerd. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als de para meter **False**is. Retourneert **Onwaar** wanneer de para meter **True**is.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ziet u hoe u logische functies gebruikt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

De uitvoer van het vorige voor beeld is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| andExampleOutput | Booleaanse waarde | False |
| orExampleOutput | Booleaanse waarde | True |
| notExampleOutput | Booleaanse waarde | False |

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) gebruikt **niet** met [gelijk aan](template-functions-comparison.md#equals).

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

## <a name="or"></a>of

`or(arg1, arg2, ...)`

Controleert of een parameter waarde waar is.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Arg1 |Ja |booleaans |De eerste waarde om te controleren of deze waar is. |
| Arg2 |Ja |booleaans |De tweede waarde om te controleren of waar is. |
| aanvullende argumenten |Nee |booleaans |Aanvullende argumenten om te controleren of deze waar zijn. |

### <a name="return-value"></a>Retourwaarde

Retourneert **waar** als een wille keurige waarde waar is; anders **False**.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ziet u hoe u logische functies gebruikt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

De uitvoer van het vorige voor beeld is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| andExampleOutput | Booleaanse waarde | False |
| orExampleOutput | Booleaanse waarde | True |
| notExampleOutput | Booleaanse waarde | False |

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor een beschrijving van de secties in een Azure Resource Manager sjabloon.

