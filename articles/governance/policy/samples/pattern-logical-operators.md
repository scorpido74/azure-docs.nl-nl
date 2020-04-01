---
title: 'Patroon: Logische operatoren in een beleidsdefinitie'
description: Dit Azure-beleidspatroon bevat voorbeelden van het gebruik van de logische operatoren in een beleidsdefinitie.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 8e57efaea81848c6b2d0188dbf3f91e06ed74c67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172847"
---
# <a name="azure-policy-pattern-logical-operators"></a>Azure-beleidspatroon: logische operatoren

Een beleidsdefinitie kan verschillende voorwaardelijke verklaringen bevatten. U elke verklaring nodig hebben om waar te zijn of slechts sommige van hen nodig hebben om waar te zijn. Om deze behoeften te ondersteunen, heeft de taal [logische operatoren](../concepts/definition-structure.md#logical-operators) voor **niet**, **allOf**, en **anyOf**. Ze zijn optioneel en kunnen worden genest om complexe scenario's te maken.

## <a name="sample-1-one-logical-operator"></a>Voorbeeld 1: Eén logische operator

Met deze beleidsdefinitie worden CosmosDB-accounts geëvalueerd om te zien of automatische failovers en meerdere schrijflocaties zijn geconfigureerd. Als dit niet het [audit](../concepts/effects.md#audit) zo is, worden de controle-ups geactiveerd en maakt u een logboekvermelding wanneer de niet-compatibele bron wordt gemaakt of bijgewerkt.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>Voorbeeld 1: Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

De **policyRule.als** blok één **allOf** gebruikt om ervoor te zorgen dat alle drie de voorwaarden waar zijn.
Pas wanneer al deze voorwaarden worden geëvalueerd tot waar, wordt het **auditeffect** geactiveerd.

## <a name="sample-2-multiple-logical-operators"></a>Voorbeeld 2: Meerdere logische operatoren

Met deze beleidsdefinitie worden resources voor een naamgevingspatroon geëvalueerd. Als een resource niet overeenkomt, wordt [deze geweigerd.](../concepts/effects.md#deny)

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>Voorbeeld 2: Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Deze **policyRule.if** block bevat ook een **allOf,** maar elke voorwaarde is verpakt met de **niet** logische operator. De voorwaardelijke binnen de **niet** logische operator evalueert eerst en evalueert vervolgens de **niet** om te bepalen of de hele clausule waar of onwaar is. Als beide **niet** logische operatoren naar true worden geëvalueerd, wordt het beleidseffect geactiveerd.

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).