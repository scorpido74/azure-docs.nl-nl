---
title: 'Patroon: logische Opera tors in een beleids definitie'
description: Dit Azure Policy patroon biedt voor beelden van het gebruik van logische Opera tors in een beleids definitie.
ms.date: 04/15/2020
ms.topic: sample
ms.openlocfilehash: 691383b1f8ae34bbd51ce7f4f9310980e3c66537
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81272505"
---
# <a name="azure-policy-pattern-logical-operators"></a>Azure Policy patroon: logische Opera tors

Een beleids definitie kan verschillende voorwaardelijke instructies bevatten. Mogelijk hebt u elke instructie nodig om waar te zijn of wilt u deze alleen waar nodig hebben. Ter ondersteuning van deze behoeften heeft de taal [logische Opera tors](../concepts/definition-structure.md#logical-operators) voor **not**, **overzet**en **anyOf**. Ze zijn optioneel en kunnen worden genest om complexe scenario's te maken.

## <a name="sample-1-one-logical-operator"></a>Voor beeld 1: één logische operator

Met deze beleids definitie worden CosmosDB-accounts geëvalueerd om te zien of automatische failovers en meerdere schrijf locaties zijn geconfigureerd. Als dat niet het geval is, wordt de [controle](../concepts/effects.md#audit) geactiveerd en wordt er een logboek vermelding gemaakt wanneer de niet-compatibele resource wordt gemaakt of bijgewerkt.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>Voor beeld 1: uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

De **policyRule. If** -blok gebruikt één **overzet** om ervoor te zorgen dat alle drie de voor waarden waar zijn.
Alleen wanneer aan al deze voor waarden wordt voldaan, wordt **de trigger geactiveerd.**

## <a name="sample-2-multiple-logical-operators"></a>Voor beeld 2: meerdere logische Opera tors

Deze beleids definitie evalueert bronnen voor een naamgevings patroon. Als een resource niet overeenkomt, wordt deze [geweigerd](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>Voor beeld 2: uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Deze **policyRule. If** -blok bevat ook één **overzet**, maar elke voor waarde wordt verpakt met de **niet** -logische operator. De voorwaardelijke waarde in de **niet** -logische operator evalueert eerst en evalueert vervolgens de **niet** om te bepalen of de volledige component True of False is. Als beide **niet** -logische Opera tors als waar worden geëvalueerd, wordt het beleid geactiveerd.

## <a name="sample-3-combining-logical-operators"></a>Voor beeld 3: logische Opera tors combi neren

Deze beleids definitie evalueert Java lente-accounts om te zien of tracering niet is ingeschakeld of dat tracering de status geslaagd heeft.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json":::

### <a name="sample-3-explanation"></a>Voor beeld 3: uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-3.json" range="6-28" highlight="3,8":::

Dit **policyRule. If** -blok bevat de logische Opera tors **overzet** en **anyOf** . De logische operator **anyOf** evalueert waar als een opgenomen voor waarde waar is. Omdat het _type_ is ingesteld op de kern van de **overzet**, moet het altijd de waarde True evalueren. Als het _type_ en een van de voor waarden in de **anyOf** waar zijn, wordt het beleids effect geactiveerd.

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).