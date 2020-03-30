---
title: Sjabloonfuncties - logisch
description: Beschrijft de functies die u moet gebruiken in een Azure Resource Manager-sjabloon om logische waarden te bepalen.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: f058baa32e5f93a4177913287a5e9873fa7a9acb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156307"
---
# <a name="logical-functions-for-arm-templates"></a>Logische functies voor ARM-sjablonen

Resource Manager biedt verschillende functies voor het maken van vergelijkingen in uw Azure Resource Manager -sjablonen (ARM).

* [En](#and)
* [bool](#bool)
* [Als](#if)
* [Niet](#not)
* [Of](#or)

## <a name="and"></a>en

`and(arg1, arg2, ...)`

Hiermee wordt gecontroleerd of alle parameterwaarden waar zijn.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |booleaans |De eerste waarde om te controleren of waar is. |
| arg2 |Ja |booleaans |De tweede waarde om te controleren of waar is. |
| aanvullende argumenten |Nee |booleaans |Aanvullende argumenten om te controleren of waar zijn. |

### <a name="return-value"></a>Retourwaarde

**Geeft als** resultaat True als alle waarden waar zijn; anders, **False**.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ziet u hoe u logische functies gebruikt.

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

De uitvoer uit het voorgaande voorbeeld is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| enExampleOutput | Booleaanse waarde | False |
| orExampleOutput | Booleaanse waarde | True |
| notExampleOutput | Booleaanse waarde | False |

## <a name="bool"></a>Booleaanse waarde

`bool(arg1)`

Hiermee converteert u de parameter naar een booleaan.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |tekenreeks of int |De waarde om te converteren naar een booleaan. |

### <a name="return-value"></a>Retourwaarde
Een booleaan van de geconverteerde waarde.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) ziet u hoe u bool gebruikt met een tekenreeks of geheelgetal.

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

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| trueString trueString trueString trueString | Booleaanse waarde | True |
| falseString falseString falseString falseString | Booleaanse waarde | False |
| trueInt | Booleaanse waarde | True |
| falseInt | Booleaanse waarde | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Geeft als resultaat een waarde op basis van de vraag of een voorwaarde waar of onwaar is.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| Voorwaarde |Ja |booleaans |De waarde om te controleren of het waar of onwaar is. |
| trueValue |Ja | tekenreeks, int, object of array |De waarde om terug te keren wanneer de voorwaarde waar is. |
| falseValue |Ja | tekenreeks, int, object of array |De waarde om terug te keren wanneer de voorwaarde vals is. |

### <a name="return-value"></a>Retourwaarde

Geeft als resultaat de tweede parameter wanneer de eerste parameter **True**is ; anders geeft de derde parameter een resultaat.

### <a name="remarks"></a>Opmerkingen

Wanneer de voorwaarde **True**is, wordt alleen de werkelijke waarde geëvalueerd. Wanneer de voorwaarde **Onwaar**is, wordt alleen de valse waarde geëvalueerd. Met de functie **als** u expressies opnemen die alleen voorwaardelijk geldig zijn. U bijvoorbeeld verwijzen naar een resource die onder één voorwaarde bestaat, maar niet onder de andere voorwaarde. Een voorbeeld van het voorwaardelijk evalueren van expressies wordt weergegeven in de volgende sectie.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) `if` ziet u hoe u de functie gebruikt.

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

De uitvoer uit het voorgaande voorbeeld is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| yesOutput | Tekenreeks | ja |
| noOutput | Tekenreeks | nee |
| objectOutput | Object | { "test": "waarde1" } |

In de volgende [voorbeeldsjabloon](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) ziet u hoe u deze functie gebruikt met expressies die alleen voorwaardelijk geldig zijn.

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

Converteert booleaanse waarde naar de tegenovergestelde waarde.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |booleaans |De waarde die u wilt converteren. |

### <a name="return-value"></a>Retourwaarde

Geeft als resultaat **True** wanneer de parameter **Onwaar**is. **Retourneert Onwaar** wanneer de parameter **True is**.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ziet u hoe u logische functies gebruikt.

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

De uitvoer uit het voorgaande voorbeeld is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| enExampleOutput | Booleaanse waarde | False |
| orExampleOutput | Booleaanse waarde | True |
| notExampleOutput | Booleaanse waarde | False |

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) wordt **niet** met [gelijken gebruikt.](template-functions-comparison.md#equals)

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

De uitvoer uit het voorgaande voorbeeld is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| checkNotEquals | Booleaanse waarde | True |

## <a name="or"></a>of

`or(arg1, arg2, ...)`

Hiermee wordt gecontroleerd of een parameterwaarde waar is.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |booleaans |De eerste waarde om te controleren of waar is. |
| arg2 |Ja |booleaans |De tweede waarde om te controleren of waar is. |
| aanvullende argumenten |Nee |booleaans |Aanvullende argumenten om te controleren of waar zijn. |

### <a name="return-value"></a>Retourwaarde

Geeft als resultaat **True** als een waarde waar is; anders, **False**.

### <a name="examples"></a>Voorbeelden

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) ziet u hoe u logische functies gebruikt.

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

De uitvoer uit het voorgaande voorbeeld is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| enExampleOutput | Booleaanse waarde | False |
| orExampleOutput | Booleaanse waarde | True |
| notExampleOutput | Booleaanse waarde | False |

## <a name="next-steps"></a>Volgende stappen

* Zie [Sjablonen voor Azure Resource Manager ontwerpen](template-syntax.md)voor een beschrijving van de secties in een Azure Resource Manager-sjabloon.
* Zie [Gekoppelde sjablonen gebruiken met Azure Resource Manager](linked-templates.md)als u meerdere sjablonen wilt samenvoegen.
* Zie Meerdere exemplaren van resources maken [in Azure Resource Manager](copy-resources.md)als u een bepaald aantal keren wilt herhalen bij het maken van een type resource.
* Zie Een toepassing implementeren [met Azure Resource Manager-sjabloon](deploy-powershell.md)als u wilt zien hoe u de sjabloon implementeren die u hebt gemaakt.

