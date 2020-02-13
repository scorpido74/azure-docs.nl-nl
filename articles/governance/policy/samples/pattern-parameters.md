---
title: 'Patroon: para meters in een beleids definitie'
description: Dit Azure Policy patroon bevat een voor beeld van het gebruik van para meters in een beleids definitie.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 4921bb216ef67b561bc8986cf48239e6448beafc
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172805"
---
# <a name="azure-policy-pattern-parameters"></a>Azure Policy patroon: para meters

Een beleids definitie kan dynamisch worden gemaakt om het aantal beleids definities te beperken dat nodig is met behulp van [para meters](../concepts/definition-structure.md#parameters). De para meter wordt gedefinieerd tijdens de beleids toewijzing. Para meters hebben een aantal vooraf gedefinieerde eigenschappen die de para meter en de manier waarop deze worden gebruikt, beschrijven.

## <a name="sample-1-string-parameters"></a>Voor beeld 1: teken reeks parameters

Deze beleids definitie gebruikt twee para meters, **tagName** en **tagValue** om in te stellen wat de beleids toewijzing op zoek is naar bronnen. Met deze indeling kan het beleid worden gebruikt voor een wille keurig aantal combi Naties van label namen en tags. u hoeft slechts één beleids definitie te onderhouden.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Voor beeld 1: uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

In dit gedeelte van de beleids definitie wordt de para meter **tagName** gedefinieerd als een _teken reeks_ en wordt een beschrijving opgegeven voor het gebruik ervan.

De para meter wordt vervolgens gebruikt in de **policyRule. If** -blok om het beleid dynamisch te maken. Hier wordt het gebruikt om het veld te definiëren dat wordt geëvalueerd. Dit is een tag met de waarde **tagName**.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Voor beeld 2: matrix parameters

Deze beleids definitie maakt gebruik van één para meter, **listOfBandwidthinMbps**om te controleren of de resource voor Express route-Circuit de band breedte-instelling heeft geconfigureerd voor een van de goedgekeurde waarden. Als deze niet overeenkomt, wordt het maken of bijwerken van de resource [geweigerd](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Voor beeld 2: uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

In dit gedeelte van de beleids definitie wordt de para meter **listOfBandwidthinMbps** gedefinieerd als een _matrix_ en wordt een beschrijving opgegeven voor het gebruik ervan. Als _matrix_, worden er meerdere waarden gevonden.

De para meter wordt vervolgens gebruikt in de **policyRule. If** -blok. Als _matrix_ parameter moet een _matrix_
[voor waarde](../concepts/definition-structure.md#conditions) **in** of **notIn** worden gebruikt.
Hier wordt het gebruikt voor de alias **serviceProvider. bandwidthInMbps** als een van de gedefinieerde waarden.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).