---
title: 'Patroon: Groepsbeleidsdefinities met initiatieven'
description: Dit Azure-beleidspatroon geeft een voorbeeld van hoe beleidsdefinities worden gegroepeerd in een initiatief
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 41c2b0cf3b8f677cdc408e85088c3ca6c2049d6b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172854"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Azure-beleidspatroon: groepsbeleidsdefinities

Een initiatief is een groep beleidsdefinities. Door gerelateerde beleidsdefinities samen te brengen in één object, u één toewijzing maken die meerdere toewijzingen zou zijn geweest.

## <a name="sample-initiative-definition"></a>Voorbeeld-initiatiefdefinitie

Dit initiatief implementeert twee beleidsdefinities, die elk de **tagName-** en **tagValue-parameters** bevatten. Het initiatief zelf heeft twee parameters: **costCenterValue** en **productNameValue**.
Deze initiatiefparameters worden elk verstrekt aan elk van de gegroepeerde beleidsdefinities. Dit ontwerp maximaliseert het hergebruik van de bestaande beleidsdefinities en beperkt het aantal toewijzingen dat is gemaakt om ze naar behoefte te implementeren.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Uitleg

#### <a name="initiative-parameters"></a>Initiatiefparameters

Een initiatief kan zijn eigen parameters definiëren die vervolgens worden doorgegeven aan de gegroepeerde beleidsdefinities.
In dit voorbeeld worden zowel **costCenterValue** als **productNameValue** gedefinieerd als initiatiefparameters. De waarden worden verstrekt wanneer het initiatief wordt toegewezen.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>Omvat beleidsdefinities

Elke opgenomen beleidsdefinitie moet de **beleidsdefinitiegevende id** en een **parametersarray** bieden als de beleidsdefinitie parameters accepteert. In het onderstaande fragment neemt de opgenomen beleidsdefinitie twee parameters: **tagName** en **tagValue**. **tagName** wordt gedefinieerd met een letterlijke, maar **tagValue** maakt gebruik van de parameter **costCenterValue** gedefinieerd door het initiatief. Deze doorwerking van waarden verbetert hergebruik.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).