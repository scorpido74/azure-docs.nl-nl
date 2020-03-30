---
title: Auteursbeleid voor arrayeigenschappen op resources
description: Leer werken met matrixparameters en matrixtaalexpressies, evalueer de [*]-alias en om elementen toe te passen met Azure Policy-definitieregels.
ms.date: 11/26/2019
ms.topic: how-to
ms.openlocfilehash: 991d159f6444133d902382bc9ca43bc2acd201e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280662"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Auteursbeleid voor arrayeigenschappen op Azure-resources

Azure Resource Manager-eigenschappen worden vaak gedefinieerd als tekenreeksen en booleaans. Wanneer er een één-op-één relatie bestaat, worden complexe eigenschappen in plaats daarvan gedefinieerd als arrays. In Azure Policy worden arrays op verschillende manieren gebruikt:

- Het type van een [definitieparameter](../concepts/definition-structure.md#parameters)om meerdere opties te bieden
- Onderdeel van een [beleidsregel](../concepts/definition-structure.md#policy-rule) met de voorwaarden **in** of **nietIn**
- Onderdeel van een beleidsregel die [ \[ \* \] ](../concepts/definition-structure.md#understanding-the--alias) de alias evalueert om te evalueren:
  - Scenario's zoals **Geen,** **Any**of **Alles**
  - Complexe scenario's met **telling**
- In het [toevoegeffect](../concepts/effects.md#append) vervangen of toevoegen aan een bestaande array

Dit artikel behandelt elk gebruik door Azure Policy en bevat verschillende voorbeelddefinities.

## <a name="parameter-arrays"></a>Parameterarrays

### <a name="define-a-parameter-array"></a>Een parameterarray definiëren

Als u een parameter als een array definieert, u de beleidsflexibiliteit behouden wanneer er meer dan één waarde nodig is.
Met deze beleidsdefinitie u elke locatie voor de **parameter toegestaanlocaties** en standaardwaarden naar _eastus2:_

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Als **tekst** _tekenreeks_was, kan slechts één waarde worden ingesteld bij het toewijzen van het beleid. Als dit beleid is toegewezen, zijn resources in het bereik alleen toegestaan binnen één Azure-gebied. De meeste beleidsdefinities moeten een lijst van goedgekeurde opties mogelijk maken, zoals het toestaan van _eastus2_, _eastus_en _westus2_.

Als u de beleidsdefinitie wilt maken om meerdere opties toe te staan, gebruikt u het _arraytype_ **type**. Hetzelfde beleid kan als volgt worden herschreven:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Zodra een beleidsdefinitie is opgeslagen, kan de **eigenschap type** op een parameter niet worden gewijzigd.

Deze nieuwe parameterdefinitie heeft meer dan één waarde tijdens beleidstoewijzing. Met de arrayeigenschap **toegestaanWaarden** gedefinieerd, zijn de waarden die beschikbaar zijn tijdens de toewijzing verder beperkt tot de vooraf gedefinieerde lijst met keuzes. Gebruik van **toegestane Waarden** is optioneel.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Waarden doorgeven aan een parameterarray tijdens toewijzing

Wanneer u het beleid via de Azure-portal toestelt, wordt een parameter van **de typearray** _array_ weergegeven als één tekstvak. De hint zegt: "Gebruik ; om waarden te scheiden. (bijv. Londen; New York)". Als u de toegestane locatiewaarden van _eastus2,_ _eastus_en _westus2_ wilt doorgeven aan de parameter, gebruikt u de volgende tekenreeks:

`eastus2;eastus;westus2`

De indeling voor de parameterwaarde is anders wanneer u Azure CLI, Azure PowerShell of de REST-API gebruikt. De waarden worden doorgegeven via een JSON-tekenreeks die ook de naam van de parameter bevat.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Als u deze tekenreeks bij elke SDK wilt gebruiken, gebruikt u de volgende opdrachten:

- Azure CLI: opdracht [az-beleidstoewijzing maken](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) met **parameterparams**
- Azure PowerShell: Cmdlet New-AzPolicyAssignment with parameter PolicyParameter Azure PowerShell: Cmdlet New-AzPolicyAssignment with parameter PolicyParameter Azure PowerShell: Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) with parameter **PolicyParameter** Azure Power
- REST-API: in de _bewerking PUT_ [maken](/rest/api/resources/policyassignments/create) als onderdeel van de hoofdtekst van aanvraag als de waarde van de eigenschap **properties.parameters**

## <a name="policy-rules-and-arrays"></a>Beleidsregels en arrays

### <a name="array-conditions"></a>Matrixvoorwaarden

De [beleidsregelvoorwaarden](../concepts/definition-structure.md#conditions) waarmee een
**arraytype** parameter kan `in` worden `notIn`gebruikt, is beperkt tot en . _array_ Neem de volgende beleidsdefinitie met voorwaarde `equals` als voorbeeld:

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

Als u probeert deze beleidsdefinitie via de Azure-portal te maken, wordt dit foutbericht als volgt weergegeven:

- "Het beleid {GUID}' kan niet worden geparaiseerd vanwege validatiefouten. Controleer of beleidsparameters correct zijn gedefinieerd. De binnenste uitzondering 'Evaluatieresultaat van taaluitdrukking '[parameters('toegestaneLocaties')]' is type 'Array', het verwachte type is 'String'.'."

Het **type** verwachte type `equals` voorwaarde is _tekenreeks_. Aangezien **allowedLocations** wordt gedefinieerd als _typearray,_ evalueert de beleidsengine de taalexpressie en gooit de fout. **type** Met `in` de `notIn` en de voorwaarde verwacht de beleidsengine de **typearray** _array_ in de taalexpressie. Als u dit foutbericht wilt oplossen, wijzigt u `equals` een van beide `in` of `notIn`.

### <a name="evaluating-the--alias"></a>De [*] alias evalueren

Aliassen ** \[ \* ** die aan hun naam zijn gekoppeld, geven aan dat het **type** een _array_is. In plaats van de waarde van ** \[ \* ** de gehele array te evalueren, is het mogelijk om elk element van de array afzonderlijk te evalueren, met logisch EN tussen hen. Er zijn drie standaardscenario's die dit per artikel evaluatie is nuttig in: _Geen_, _Any_, of _Alle_ elementen overeenkomen. Gebruik voor complexe scenario's [het aantal](../concepts/definition-structure.md#count).

De beleidsengine activeert het **effect** in **dan** alleen wanneer de **als-regel** als waar wordt geëvalueerd.
Dit feit is belangrijk om te ** \[ \* ** begrijpen in de context van de manier waarop elk afzonderlijk element van de array evalueert.

De voorbeeldbeleidsregel voor de onderstaande scenariotabel:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

De **ipRules-array** is als volgt voor de onderstaande scenariotabel:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Voor elke voorwaarde hieronder, vervangen `<field>` door `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

De volgende resultaten zijn het resultaat van de combinatie van de voorwaarde en de voorbeeldbeleidsregel en array van bestaande waarden hierboven:

|Voorwaarde |Resultaat | Scenario |Uitleg |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Niets |None |Eén arrayelement evalueert als vals (127.0.0.1 != 127.0.0.1) en één als waar (127.0.0.1 != 192.168.1.1), dus de **voorwaarde notEquals** is _vals_ en het effect wordt niet geactiveerd. |
|`{<field>,"notEquals":"10.0.4.1"}` |Beleidseffect |None |Beide matrixelementen evalueren als waar (10.0.4.1 != 127.0.0.1 en 10.0.4.1 != 192.168.1.1), zodat de **voorwaarde notEquals** _waar_ is en het effect wordt geactiveerd. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Beleidseffect |Een of meer overeenkomst |Eén arrayelement evalueert als vals (127.0.0.1 != 127.0.0.1) en één als waar (127.0.0.1 != 192.168.1.1), dus de **voorwaarde notEquals** is _vals_. De logische operator evalueert als waar **(niet** _vals),_ dus het effect wordt geactiveerd. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Niets |Een of meer overeenkomst |Beide matrixelementen evalueren als waar (10.0.4.1 != 127.0.0.1 en 10.0.4.1 != 192.168.1.1), dus de **voorwaarde notEquals** is _waar_. De logische operator evalueert als false **(niet** _waar),_ dus het effect wordt niet geactiveerd. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Beleidseffect |Niet alle overeenkomen |Eén arrayelement evalueert als waar (127.0.0.1 == 127.0.0.1) en één als vals (127.0.0.1 == 192.168.1.1), dus de **voorwaarde Gelijken** is _vals_. De logische operator evalueert als waar **(niet** _vals),_ dus het effect wordt geactiveerd. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Beleidseffect |Niet alle overeenkomen |Beide matrixelementen evalueren als false (10.0.4.1 == 127.0.0.1 en 10.0.4.1 == 192.168.1.1).1), dus de **voorwaarde Gelijken** is _vals_. De logische operator evalueert als waar **(niet** _vals),_ dus het effect wordt geactiveerd. |
|`{<field>,"Equals":"127.0.0.1"}` |Niets |Alle overeenkomen |Eén arrayelement evalueert als waar (127.0.0.1 == 127.0.0.1) en één als vals (127.0.0.1 == 192.168.1.1), zodat de **voorwaarde Gelijken** _vals_ is en het effect niet wordt geactiveerd. |
|`{<field>,"Equals":"10.0.4.1"}` |Niets |Alle overeenkomen |Beide matrixelementen evalueren als false (10.0.4.1 == 127.0.0.1 en 10.0.4.1 == 192.168.1.1.1), dus de **voorwaarde Gelijken** is _vals_ en het effect wordt niet geactiveerd. |

## <a name="the-append-effect-and-arrays"></a>Het toeteappende effect en de arrays

Het [toesteleffect](../concepts/effects.md#append) gedraagt zich anders, afhankelijk van of ** \[ \* ** het veld **details een** alias is of niet.

- Wanneer u ** \[ \* ** geen alias hebt, vervangt de toevoegen de hele array door de **eigenschap value**
- Wanneer ** \[ \* ** een alias de **waardeeigenschap** toevoegt aan de bestaande array of de nieuwe array maakt

Zie de voorbeelden [voor het toevoegen van voorbeelden](../concepts/effects.md#append-examples)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Voorbeelden bekijken bij [Azure Policy-voorbeelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Begrijpen hoe [u programmatisch beleid maken.](programmatically-create.md)
- Meer informatie over het [herstellen van niet-conforme resources.](remediate-resources.md)
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)
