---
title: 'Patroon: de waardeoperator in een beleidsdefinitie'
description: Dit Azure-beleidspatroon geeft een voorbeeld van hoe u de waardeoperator in een beleidsdefinitie gebruiken.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: ace7b7cd4a765cdb8c7aa764b52b180c60508ab2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172784"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Azure-beleidspatroon: de waardeoperator

De [waardeoperator](../concepts/definition-structure.md#value) evalueert [parameters](../concepts/definition-structure.md#parameters), [ondersteunde sjabloonfuncties](../concepts/definition-structure.md#policy-functions)of literals tot een opgegeven waarde voor een bepaalde [voorwaarde](../concepts/definition-structure.md#conditions).

> [!WARNING]
> Als het resultaat van een _sjabloonfunctie_ een fout is, mislukt de beleidsevaluatie. Een mislukte evaluatie is een impliciete **ontkenning**. Zie [Sjabloonfouten vermijden voor](../concepts/definition-structure.md#avoiding-template-failures)meer informatie .

## <a name="sample-policy-definition"></a>Voorbeeldbeleidsdefinitie

Met deze beleidsdefinitie wordt de tag toegevoegd die is opgegeven in de **parametertagName** _(tekenreeks)_ op resources en wordt de waarde voor **tagName** overgenomen van de resourcegroep waarin de resource zich bevindt. Deze evaluatie vindt plaats wanneer de resource wordt gemaakt of bijgewerkt. Als [wijzigingseffect](../concepts/effects.md#modify) kan de herstelprocedure worden uitgevoerd op bestaande resources via een [hersteltaak](../how-to/remediate-resources.md).

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

De **waardeoperator** wordt gebruikt binnen de **policyRule.if** block within **properties**. In dit voorbeeld wordt de [logische operator](../concepts/definition-structure.md#logical-operators) **allOf** gebruikt om aan te geven dat beide voorwaardelijke instructies waar moeten zijn voor het effect, **wijzigen**, om plaats te vinden.

**waarde** evalueert het resultaat van de [sjabloonfunctieresourceGroep()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) naar de voorwaarde **notEquals** van een lege waarde. Als de tagnaam in **tagName** in de bovenliggende resourcegroep bestaat, wordt de voorwaardelijke evaluatie naar true geëvalueerd.

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).