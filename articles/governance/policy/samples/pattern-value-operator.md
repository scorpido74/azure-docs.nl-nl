---
title: 'Patroon: de waarde-operator in een beleids definitie'
description: Dit Azure Policy patroon bevat een voor beeld van het gebruik van de waarde-operator in een beleids definitie.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: ace7b7cd4a765cdb8c7aa764b52b180c60508ab2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77172784"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Azure Policy patroon: de waarde-operator

De operator [waarde](../concepts/definition-structure.md#value) evalueert [para meters](../concepts/definition-structure.md#parameters), [ondersteunde sjabloon functies](../concepts/definition-structure.md#policy-functions)of letterlijke waarden naar een opgegeven waarde voor een bepaalde [voor waarde](../concepts/definition-structure.md#conditions).

> [!WARNING]
> Als het resultaat van een _sjabloon functie_ een fout is, mislukt de beleids evaluatie. Een mislukte evaluatie is een impliciete **weigering**. Zie voor meer informatie [sjabloon fouten voor komen](../concepts/definition-structure.md#avoiding-template-failures).

## <a name="sample-policy-definition"></a>Voor beeld van beleids definitie

Deze beleids definitie voegt of vervangt de tag die is opgegeven in de para meter **tagName** (_teken reeks_) op resources en neemt de waarde voor **tagName** over van de resource groep waarin de resource zich bevindt. Deze evaluatie treedt op wanneer de resource wordt gemaakt of bijgewerkt. Als [wijzigings](../concepts/effects.md#modify) effect kan het herstel worden uitgevoerd op bestaande resources via een [herstel taak](../how-to/remediate-resources.md).

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

De **waarde** -operator wordt gebruikt binnen de **policyRule. If** -blok in **Eigenschappen**. In dit voor beeld wordt de [logische operator](../concepts/definition-structure.md#logical-operators) **overzet** gebruikt om te controleren of beide voorwaardelijke instructies waar moeten zijn voor het effect, de **wijziging**moeten worden uitgevoerd.

**waarde** evalueert het resultaat van de sjabloon functie [resourceGroup ()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) naar de voor waarde **notEquals** van een lege waarde. Als de naam van het label in **tagName** voor de bovenliggende resource groep bestaat, wordt de voorwaardelijke expressie geëvalueerd naar waar.

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).