---
title: Azure Resource Manager sjabloon functies-Logical | Microsoft Docs
description: Hierin worden de functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon om logische waarden te bepalen.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: tomfitz
ms.openlocfilehash: ea91798a1c0ca0aad729128ce4694a85165f3c3b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194787"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Logische functies voor Azure Resource Manager sjablonen

Resource Manager biedt verschillende functies voor het maken van vergelijkingen in uw sjablonen.

* [and](#and)
* [bool](#bool)
* [if](#if)
* [not](#not)
* [or](#or)

## <a name="and"></a>and

`and(arg1, arg2, ...)`

Controleert of alle parameter waarden waar zijn.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolean |De eerste waarde om te controleren of deze waar is. |
| Arg2 |Ja |boolean |De tweede waarde om te controleren of waar is. |
| aanvullende argumenten |Nee |boolean |Aanvullende argumenten om te controleren of deze waar zijn. |

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

| Name | Type | Value |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | Waar |
| notExampleOutput | Bool | False |

## <a name="bool"></a>bool

`bool(arg1)`

Zet de para meter om in een Boole-waarde.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
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

| Name | Type | Value |
| ---- | ---- | ----- |
| trueString | Bool | Waar |
| falseString | Bool | False |
| trueInt | Bool | Waar |
| falseInt | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Retourneert een waarde op basis van het feit of een voor waarde waar of onwaar is.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| condition |Ja |boolean |De waarde om te controleren of deze True of False is. |
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

| Name | Type | Value |
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

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolean |De waarde die moet worden geconverteerd. |

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

| Name | Type | Value |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | Waar |
| notExampleOutput | Bool | False |

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) gebruikt **niet** met [gelijk aan](resource-group-template-functions-comparison.md#equals).

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

| Name | Type | Value |
| ---- | ---- | ----- |
| checkNotEquals | Bool | Waar |

## <a name="or"></a>of

`or(arg1, arg2, ...)`

Controleert of een parameter waarde waar is.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |boolean |De eerste waarde om te controleren of deze waar is. |
| Arg2 |Ja |boolean |De tweede waarde om te controleren of waar is. |
| aanvullende argumenten |Nee |boolean |Aanvullende argumenten om te controleren of deze waar zijn. |

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

| Name | Type | Value |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | Waar |
| notExampleOutput | Bool | False |

## <a name="next-steps"></a>Volgende stappen

* Zie voor een beschrijving van de secties in een Azure Resource Manager-sjabloon, [Authoring Azure Resource Manager-sjablonen](resource-group-authoring-templates.md).
* U kunt meerdere sjablonen samenvoegen, Zie [gekoppelde sjablonen gebruiken met Azure Resource Manager](resource-group-linked-templates.md).
* Op een opgegeven aantal keren herhalen bij het maken van een type resource, Zie [meerdere exemplaren van resources maken in Azure Resource Manager](resource-group-create-multiple.md).
* Zie voor meer informatie over het implementeren van de sjabloon die u hebt gemaakt, [een toepassing implementeren met Azure Resource Manager-sjabloon](resource-group-template-deploy.md).

