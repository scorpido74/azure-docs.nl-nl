---
title: Beleid voor het schrijven van matrix eigenschappen voor bronnen
description: Meer informatie over het werken met matrix parameters en matrix-taal expressies, de alias [*] evalueren en elementen toevoegen met Azure Policy definitie regels.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: c67982197c0161d99f29747d6fd11166cba86079
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576894"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Beleid voor het schrijven van matrix eigenschappen op Azure-resources

Azure Resource Manager eigenschappen worden meestal gedefinieerd als teken reeksen en Booleaanse waarden. Als er sprake is van een een-op-veel-relatie, worden complexe eigenschappen in plaats daarvan gedefinieerd als matrices. In Azure Policy worden matrices op verschillende manieren gebruikt:

- Het type [definitie parameter](../concepts/definition-structure.md#parameters), om meerdere opties te bieden
- Onderdeel van een [beleids regel](../concepts/definition-structure.md#policy-rule) met de voor waarden **in** of **notIn**
- Onderdeel van een beleids regel waarmee de [ \[ \* \] alias](../concepts/definition-structure.md#understanding-the--alias) wordt geëvalueerd om te evalueren:
  - Scenario's zoals **geen**, **alle**of **alle**
  - Complexe scenario's met **aantal**
- In het [effect toevoegen](../concepts/effects.md#append) om te vervangen of toe te voegen aan een bestaande matrix

In dit artikel wordt elk gebruik door Azure Policy beschreven en worden verschillende voorbeeld definities geboden.

## <a name="parameter-arrays"></a>Parameter matrices

### <a name="define-a-parameter-array"></a>Een parameter matrix definiëren

Als u een para meter als een matrix definieert, kan het beleid flexibel zijn wanneer er meer dan één waarde nodig is.
Met deze beleids definitie kunt u één locatie voor de para meter **allowedLocations** en de standaard waarden voor _eastus2_:

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

Als **type** is _teken reeks_, kan slechts één waarde worden ingesteld wanneer het beleid wordt toegewezen. Als dit beleid is toegewezen, zijn resources in het bereik alleen toegestaan binnen één Azure-regio. De meeste beleids definities moeten een lijst met goedgekeurde opties toestaan, zoals het toestaan van _eastus2_, _oostus_en _westus2_.

Als u de beleids definitie wilt maken om meerdere opties toe te staan, gebruikt u het _matrix_ **type**. Hetzelfde beleid kan als volgt worden herschreven:

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
> Zodra een beleids definitie is opgeslagen, kan de eigenschap **type** van een para meter niet worden gewijzigd.

Deze nieuwe parameter definitie heeft meer dan één waarde tijdens de beleids toewijzing. Met de matrix eigenschap **allowedValues** gedefinieerd, worden de waarden die beschikbaar zijn tijdens de toewijzing, verder beperkt tot de vooraf gedefinieerde lijst met opties. Het gebruik van **allowedValues** is optioneel.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Waarden door geven aan een parameter matrix tijdens toewijzing

Wanneer u het beleid toewijst via de Azure Portal, wordt een para meter van het **type** _matrix_ weer gegeven als één tekstvak. De hint zegt ' use; om waarden te scheiden. (bijvoorbeeld Londen; New York) ". Gebruik de volgende teken reeks om de toegestane locatie waarden van _eastus2_, _oostus_en _westus2_ aan de para meter door te geven:

`eastus2;eastus;westus2`

De notatie voor de parameter waarde is anders wanneer u Azure CLI, Azure PowerShell of de REST API gebruikt. De waarden worden door gegeven via een JSON-teken reeks die ook de naam van de para meter bevat.

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

Gebruik de volgende opdrachten om deze teken reeks te gebruiken voor elke SDK:

- Azure CLI: opdracht [AZ Policy Assignment Create](/cli/azure/policy/assignment#az-policy-assignment-create) with para meter **params**
- Azure PowerShell: cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) met para meter **PolicyParameter**
- REST API: in de _put_ -bewerking [maken](/rest/api/resources/policyassignments/create) als onderdeel van de aanvraag tekst als de waarde van de **Eigenschappen. para meters** -eigenschap

## <a name="policy-rules-and-arrays"></a>Beleids regels en-matrices

### <a name="array-conditions"></a>Matrix voorwaarden

De beleids regel [voorwaarden](../concepts/definition-structure.md#conditions) waarvoor het _matrix_ 
 **type** van de para meter mag worden gebruikt, zijn beperkt tot `in` en `notIn` . Neem de volgende beleids definitie met voor waarde `equals` als voor beeld:

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

Het maken van deze beleids definitie via de Azure Portal leidt tot een fout, zoals dit fout bericht:

- "Het beleid {GUID} kan niet worden geparametriseerde vanwege validatie fouten. Controleer of de beleids parameters juist zijn gedefinieerd. Het evaluatie resultaat van de interne uitzonde ring van de taal expressie [para meters (' allowedLocations ')] ' is van het type ' matrix ', het verwachte type is ' String '. '

Het verwachte **type** voor waarde `equals` is _teken reeks_. Omdat **allowedLocations** is gedefinieerd als **type** _matrix_, evalueert de beleids engine de expressie van de taal en genereert de fout. Met de `in` `notIn` voor-voor waarde verwacht de beleids engine de **type** _matrix_ in de taal expressie. Als u dit fout bericht wilt oplossen, wijzigt `equals` u in `in` of `notIn` .

### <a name="evaluating-the--alias"></a>De alias [*] evalueren

Aliassen die aan **\[\*\]** hun naam zijn gekoppeld, geven aan dat het **type** een _matrix_is. In plaats van de waarde van de volledige matrix te evalueren, **\[\*\]** is het mogelijk om elk element van de matrix afzonderlijk te evalueren, met logische en daartussen. Er zijn drie standaard scenario's waarin de evaluatie per item kan worden gebruikt: _geen_, _any_of _alle_ elementen komen niet overeen. Voor complexe scenario's gebruikt u [Count](../concepts/definition-structure.md#count).

De beleids engine activeert alleen het **effect** in **then** wanneer de **if** -regel als waar evalueert.
Dit feit is belang rijk om inzicht te krijgen in de context van de manier waarop **\[\*\]** elk afzonderlijk element van de matrix wordt geëvalueerd.

De voorbeeld beleidsregel voor de scenario tabel hieronder:

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

De **ipRules** -matrix is als volgt voor de scenario tabel hieronder:

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

Voor elk voor beeld hieronder, vervangt u door `<field>` `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"` .

De volgende resultaten zijn het resultaat van de combi natie van de voor waarde en de beleids regel voor het voor beeld en de matrix van bestaande waarden hierboven:

|Conditie |Resultaat | Scenario |Uitleg |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Niets |Geen overeenkomst |Eén matrix element evalueert als onwaar (127.0.0.1! = 127.0.0.1) en een als waar (127.0.0.1! = 192.168.1.1), zodat de **notEquals** -voor waarde _False_ is en het effect niet wordt geactiveerd. |
|`{<field>,"notEquals":"10.0.4.1"}` |Beleids effect |Geen overeenkomst |Beide matrix elementen evalueren als True (10.0.4.1! = 127.0.0.1 en 10.0.4.1! = 192.168.1.1), dus de **notEquals** -voor waarde is _True_ en het effect wordt geactiveerd. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Beleids effect |Een of meer overeenkomsten |Eén matrix element evalueert als onwaar (127.0.0.1! = 127.0.0.1) en een als waar (127.0.0.1! = 192.168.1.1), zodat de **notEquals** -voor waarde _False_is. De logische operator evalueert als True (**niet** _waar_), zodat het effect wordt geactiveerd. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Niets |Een of meer overeenkomsten |Beide matrix elementen evalueren als True (10.0.4.1! = 127.0.0.1 en 10.0.4.1! = 192.168.1.1), dus de **notEquals** -voor waarde is _True_. De logische operator evalueert als onwaar (**niet** _waar_), dus het effect wordt niet geactiveerd. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Beleids effect |Niet alle overeenkomsten |Eén matrix element evalueert als True (127.0.0.1 = = 127.0.0.1) en een als onwaar (127.0.0.1 = = 192.168.1.1), dus is de waarde **equals** _False_. De logische operator evalueert als True (**niet** _waar_), zodat het effect wordt geactiveerd. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Beleids effect |Niet alle overeenkomsten |Beide matrix elementen evalueren als onwaar (10.0.4.1 = = 127.0.0.1 en 10.0.4.1 =/192.168.1.1), zodat de waarde **equals is ingesteld** op _False_. De logische operator evalueert als True (**niet** _waar_), zodat het effect wordt geactiveerd. |
|`{<field>,"Equals":"127.0.0.1"}` |Niets |Alle overeenkomsten |Eén matrix element evalueert als True (127.0.0.1 = = 127.0.0.1) en een als onwaar (127.0.0.1 = = 192.168.1.1), dus is de waarde **equals** _False_ en wordt het effect niet geactiveerd. |
|`{<field>,"Equals":"10.0.4.1"}` |Niets |Alle overeenkomsten |Beide matrix elementen evalueren als False (10.0.4.1 = = 127.0.0.1 en 10.0.4.1 = 192.168.1.1), dus de voor waarde **equals** is _False_ en het effect wordt niet geactiveerd. |

## <a name="modifying-arrays"></a>Matrices wijzigen

De [toevoeg](../concepts/effects.md#append) -en [wijzigings](../concepts/effects.md#modify) eigenschappen voor een resource tijdens het maken of bijwerken. Wanneer u werkt met matrix eigenschappen, is het gedrag van deze effecten afhankelijk van of de bewerking probeert de alias te wijzigen  **\[\*\]** of niet:

> [!NOTE]
> Het gebruiken van het `modify` effect met aliassen is momenteel beschikbaar als **Preview-versie**.

|Alias |Effect | Resultaat |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | Azure Policy voegt de volledige matrix toe die is opgegeven in de effect Details als deze ontbreken. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` met `add` bewerking | Azure Policy voegt de volledige matrix toe die is opgegeven in de effect Details als deze ontbreken. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` met `addOrReplace` bewerking | Azure Policy voegt de volledige matrix die is opgegeven in de effect Details, toe als de bestaande matrix ontbreekt of wordt vervangen. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | Azure Policy voegt het matrixlid toe dat is opgegeven in de effect Details. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` met `add` bewerking | Azure Policy voegt het matrixlid toe dat is opgegeven in de effect Details. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` met `addOrReplace` bewerking | Azure Policy verwijdert alle bestaande matrix leden en voegt het matrixlid toe dat is opgegeven in de effect Details. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | Azure Policy voegt een waarde toe aan de `action` eigenschap van elk matrixlid. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` met `add` bewerking | Azure Policy voegt een waarde toe aan de `action` eigenschap van elk matrixlid. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` met `addOrReplace` bewerking | Azure Policy voegt de bestaande `action` eigenschap van elk matrixlid of vervangt deze. |

Zie voor meer informatie de [Append-voor beelden](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](programmatically-create.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
