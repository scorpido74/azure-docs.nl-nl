---
title: 'Patroon: Effecten van een beleidsdefinitie'
description: Dit Azure-beleidspatroon geeft een voorbeeld van het gebruik van de verschillende effecten van een beleidsdefinitie.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 1a9aec50bd328b76271d54f7830c75e0848d3cde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372641"
---
# <a name="azure-policy-pattern-effects"></a>Azure-beleidspatroon: effecten

Azure Policy heeft een aantal [effecten](../concepts/effects.md) die bepalen hoe de service reageert op niet-compatibele resources. Sommige effecten zijn eenvoudig en vereisen geen extra eigenschappen in de beleidsdefinitie, terwijl andere verschillende eigenschappen vereisen.

## <a name="sample-1-simple-effect"></a>Voorbeeld 1: Eenvoudig effect

Met deze beleidsdefinitie wordt gecontroleerd of de tag die is gedefinieerd in **parametertagName** bestaat op de geëvalueerde resource. Als de tag nog niet bestaat, wordt het [wijzigingseffect](../concepts/effects.md#modify) geactiveerd om de tag toe te voegen met de waarde in **parametertagValue**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>Voorbeeld 1: Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="40-50":::

Voor **een wijzigingseffect** is het **blok policyRule.then.details** vereist dat **roleDefinitionIds** en **operations definieert.** Deze parameters informeren Azure Policy welke rollen nodig zijn om de tag toe te voegen en de resource te herstellen en die de bewerking **wijzigen** om uit te voeren. In dit voorbeeld wordt de **bewerking** _toegevoegd_ en worden de parameters gebruikt om de tag en de waarde ervan in te stellen.

## <a name="sample-2-complex-effect"></a>Voorbeeld 2: Complex effect

Deze beleidsdefinitie controleert elke virtuele machine voor wanneer een extensie, gedefinieerd in parameters **uitgever** en **type,** niet bestaat. Het maakt gebruik van [auditIfNotExists](../concepts/effects.md#auditifnotexists) om een resource met betrekking tot de virtuele machine te controleren om te zien of er een instantie bestaat die overeenkomt met de gedefinieerde parameters. In dit voorbeeld wordt het type **extensies** gecontroleerd.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>Voorbeeld 2: Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

Een **auditIfNotExists** effect vereist de **policyRule.then.details** blok om zowel een **type** als het bestaan te **definiërenVoorwaarde** om naar te zoeken. Het **bestaanVoorwaarde** gebruikt beleidstaalelementen, zoals [logische operatoren,](../concepts/definition-structure.md#logical-operators)om te bepalen of er een overeenkomende gerelateerde resource bestaat. In dit voorbeeld worden de waarden die voor elke [alias](../concepts/definition-structure.md#aliases) worden gecontroleerd, gedefinieerd in parameters.

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).