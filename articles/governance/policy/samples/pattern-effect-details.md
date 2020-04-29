---
title: 'Patroon: effecten van een beleids definitie'
description: Dit Azure Policy patroon bevat een voor beeld van het gebruik van de verschillende effecten van een beleids definitie.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 1a9aec50bd328b76271d54f7830c75e0848d3cde
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80372641"
---
# <a name="azure-policy-pattern-effects"></a>Azure Policy patroon: effecten

Azure Policy heeft een aantal [effecten](../concepts/effects.md) die bepalen hoe de service reageert op niet-compatibele resources. Sommige effecten zijn eenvoudig en vereisen geen extra eigenschappen in de beleids definitie, terwijl anderen verschillende eigenschappen nodig hebben.

## <a name="sample-1-simple-effect"></a>Voor beeld 1: eenvoudig effect

Deze beleids definitie controleert of het label dat in para meter **tagName** is gedefinieerd, bestaat op de geëvalueerde resource. Als het label nog niet bestaat, wordt het [wijzigings](../concepts/effects.md#modify) effect geactiveerd om de tag toe te voegen met de waarde in para meter **tagValue**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>Voor beeld 1: uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="40-50":::

Een **wijzigings** effect vereist het **policyRule. then. Details** -blok waarmee **roleDefinitionIds** en **bewerkingen**worden gedefinieerd. Met deze para meters wordt Azure Policy welke functies nodig zijn om het label toe te voegen en de resource te herstellen en te **wijzigen** welke bewerking moet worden uitgevoerd. In dit voor beeld wordt **operation** de bewerking _toegevoegd_ en worden de para meters gebruikt om de tag en de waarde ervan in te stellen.

## <a name="sample-2-complex-effect"></a>Voor beeld 2: complex effect

Met deze beleids definitie wordt elke virtuele machine gecontroleerd op het moment dat een uitbrei ding, gedefinieerd in de para meters **Uitgever** en **type**, niet bestaat. [AuditIfNotExists](../concepts/effects.md#auditifnotexists) wordt gebruikt om een resource te controleren die aan de virtuele machine is gerelateerd om te zien of er een exemplaar bestaat dat overeenkomt met de gedefinieerde para meters. In dit voor beeld wordt het type **extensies** gecontroleerd.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>Voor beeld 2: uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

Voor een **auditIfNotExists** -effect is het **policyRule. then. Details** -blok vereist om zowel een **type** als de **existenceCondition** te definiëren waarnaar moet worden gezocht. De **existenceCondition** gebruikt beleids taal elementen, zoals [logische Opera tors](../concepts/definition-structure.md#logical-operators), om te bepalen of er een overeenkomende gerelateerde resource bestaat. In dit voor beeld worden de waarden die worden gecontroleerd op elke [alias](../concepts/definition-structure.md#aliases) gedefinieerd in para meters.

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).