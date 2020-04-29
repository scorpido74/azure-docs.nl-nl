---
title: 'Patroon: groeps beleids definities met initiatieven'
description: Dit Azure Policy patroon bevat een voor beeld van het groeperen van beleids definities in een initiatief
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 41c2b0cf3b8f677cdc408e85088c3ca6c2049d6b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77172854"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Azure Policy patroon: groeps beleids definities

Een initiatief is een groep beleids definities. Door gerelateerde beleids definities in één object te groeperen, kunt u één toewijzing maken die meerdere toewijzingen zou hebben.

## <a name="sample-initiative-definition"></a>Voor beeld-initiatief definitie

Dit initiatief implementeert twee beleids definities die elk de para meters **tagName** en **tagValue** . Het initiatief zelf heeft twee para meters: **costCenterValue** en **productNameValue**.
Deze initiatief parameters worden elk gegeven aan elk van de gegroepeerde beleids definities. Dit ontwerp maximaliseert het opnieuw gebruiken van de bestaande beleids definities en beperkt het aantal toewijzingen dat wordt gemaakt om ze zo nodig te implementeren.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Uitleg

#### <a name="initiative-parameters"></a>Initiatiefparameters

Een initiatief kan de eigen para meters definiëren die vervolgens worden door gegeven aan de gegroepeerde beleids definities.
In dit voor beeld worden zowel **costCenterValue** als **productNameValue** gedefinieerd als initiatief parameters. De waarden worden gegeven wanneer het initiatief is toegewezen.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>Bevat beleids definities

Elke opgenomen beleids definitie moet de **policyDefinitionId** en een **parameter** matrix opgeven als de beleids definitie para meters accepteert. In het onderstaande fragment worden met de opgenomen beleids definitie twee para meters gebruikt: **tagName** en **tagValue**. **tagName** is gedefinieerd met een letterlijke waarde, maar **tagValue** gebruikt de para meter **costCenterValue** die is gedefinieerd door het initiatief. Deze Passthrough van waarden verbetert hergebruik.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).