---
title: 'Patroon: parameters in een beleidsdefinitie'
description: Dit Azure-beleidspatroon geeft een voorbeeld van het gebruik van parameters in een beleidsdefinitie.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 4921bb216ef67b561bc8986cf48239e6448beafc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172805"
---
# <a name="azure-policy-pattern-parameters"></a>Azure-beleidspatroon: parameters

Een beleidsdefinitie kan dynamisch worden gemaakt om het aantal beleidsdefinities te verminderen dat nodig is met behulp van [parameters](../concepts/definition-structure.md#parameters). De parameter wordt gedefinieerd tijdens beleidstoewijzing. Parameters hebben een set vooraf gedefinieerde eigenschappen die de parameter beschrijven en hoe deze wordt gebruikt.

## <a name="sample-1-string-parameters"></a>Voorbeeld 1: Tekenreeksparameters

Deze beleidsdefinitie gebruikt twee parameters, **tagName** en **tagValue** om in te stellen wat de beleidstoewijzing zoekt op resources. Met deze indeling kan het beleid worden gebruikt voor een willekeurig aantal combinaties van tagnamen en tagwaarde, maar slechts één beleidsdefinitie behouden.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Voorbeeld 1: Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

In dit gedeelte van de beleidsdefinitie wordt de parameter **tagName** gedefinieerd als een _tekenreeks_ en wordt een beschrijving gegeven voor het gebruik ervan.

De parameter wordt vervolgens gebruikt in de **policyRule.if** block om het beleid dynamisch te maken. Hier wordt het gebruikt om het veld te definiëren dat wordt geëvalueerd, dat is een tag met de waarde van **tagName**.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Voorbeeld 2: Matrixparameters

Deze beleidsdefinitie gebruikt één parameter, **listOfBandwidthinMbps,** om te controleren of de Express Route Circuit-bron de bandbreedteinstelling heeft geconfigureerd op een van de goedgekeurde waarden. Als deze niet overeenkomt, wordt het maken of bijwerken van de resource [geweigerd.](../concepts/effects.md#deny)

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Voorbeeld 2: Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

In dit gedeelte van de beleidsdefinitie wordt de parameter **ListOfBandwidthinMbps** gedefinieerd als een _array_ en wordt een beschrijving gegeven voor het gebruik ervan. Als _een array,_ het heeft meerdere waarden aan te passen.

De parameter wordt vervolgens gebruikt in het **blok policyRule.if.** Als _matrixparameter_ moet een
[matrixvoorwaarde](../concepts/definition-structure.md#conditions) **in** of **nietIn** worden gebruikt. _array_
Hier wordt het gebruikt tegen de **alias serviceProvider.bandwidthInMbps** als een van de gedefinieerde waarden.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).