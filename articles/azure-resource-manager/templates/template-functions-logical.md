---
title: Sjabloon functies-logisch
description: Hierin worden de functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon om logische waarden te bepalen.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 48c6a05c5a449626c66f9d75c8578b217906d8e0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483958"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Logische functies voor Azure Resource Manager sjablonen

Resource Manager biedt verschillende functies voor het maken van vergelijkingen in uw sjablonen.

* [and](#and)
* [bool](#bool)
* [if](#if)
* [not](#not)
* [or](#or)

## <a name="and"></a>en de

`and(arg1, arg2, ...)`

Controleert of alle parameter waarden waar zijn.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |booleaans |De eerste waarde om te controleren of deze waar is. |
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

| Name | Type | Waarde |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Onwaar |
| orExampleOutput | Bool | Waar |
| notExampleOutput | Bool | Onwaar |

## <a name="bool"></a>bool

`bool(arg1)`

Zet de para meter om in een Boole-waarde.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |teken reeks of int |De waarde die moet worden geconverteerd naar een Boole. |

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| trueString | Bool | Waar |
| falseString | Bool | Onwaar |
| trueInt | Bool | Waar |
| falseInt | Bool | Onwaar |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Retourneert een waarde op basis van het feit of een voor waarde waar of onwaar is.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| condition |Ja |booleaans |De waarde om te controleren of deze True of False is. |
| trueValue |Ja | teken reeks, int, object of matrix |De waarde die moet worden geretourneerd als de voor waarde waar is. |
| falseValue |Ja | teken reeks, int, object of matrix |De waarde die moet worden geretourneerd als de voor waarde ONWAAR is. |

### <a name="return-value"></a>Retourwaarde

Retourneert een tweede para meter wanneer de eerste para meter **True**is; anders retourneert de derde para meter.

### <a name="remarks"></a>Opmerkingen

Als de voor waarde **waar**is, wordt alleen de waarde True geëvalueerd. Als de voor waarde **Onwaar**is, wordt alleen de waarde ONWAAR geëvalueerd. Met de functie **als** kunt u expressies toevoegen die alleen voorwaarde geldig zijn. U kunt bijvoorbeeld verwijzen naar een resource die zich onder één voor waarde bevindt, maar niet onder de andere voor waarde. In de volgende sectie ziet u een voor beeld van een conditioneel evalueren van expressies.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) ziet u hoe u de `if` functie gebruikt.

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

| Name | Type | Waarde |
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

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |booleaans |De waarde die moet worden geconverteerd. |

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

| Name | Type | Waarde |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Onwaar |
| orExampleOutput | Bool | Waar |
| notExampleOutput | Bool | Onwaar |

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
```

De uitvoer van het vorige voor beeld is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| checkNotEquals | Bool | Waar |

## <a name="or"></a>of

`or(arg1, arg2, ...)`

Controleert of een parameter waarde waar is.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |booleaans |De eerste waarde om te controleren of deze waar is. |
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

| Name | Type | Waarde |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Onwaar |
| orExampleOutput | Bool | Waar |
| notExampleOutput | Bool | Onwaar |

## <a name="next-steps"></a>Volgende stappen

* Zie voor een beschrijving van de secties in een Azure Resource Manager-sjabloon, [Authoring Azure Resource Manager-sjablonen](template-syntax.md).
* U kunt meerdere sjablonen samenvoegen, Zie [gekoppelde sjablonen gebruiken met Azure Resource Manager](linked-templates.md).
* Op een opgegeven aantal keren herhalen bij het maken van een type resource, Zie [meerdere exemplaren van resources maken in Azure Resource Manager](create-multiple-instances.md).
* Zie voor meer informatie over het implementeren van de sjabloon die u hebt gemaakt, [een toepassing implementeren met Azure Resource Manager-sjabloon](deploy-powershell.md).

