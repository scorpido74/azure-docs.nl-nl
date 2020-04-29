---
title: 'Patroon: veld eigenschappen in een beleids definitie'
description: Dit Azure Policy patroon bevat een voor beeld van het gebruik van veld eigenschappen in een beleids definitie.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: e65767dd9cbe7b2192c21f779643289e5a7fc45e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77172861"
---
# <a name="azure-policy-pattern-field-properties"></a>Azure Policy patroon: veld eigenschappen

De [veld](../concepts/definition-structure.md#fields) operator evalueert de opgegeven eigenschap of [alias](../concepts/definition-structure.md#aliases) naar een opgegeven waarde voor een bepaalde [voor waarde](../concepts/definition-structure.md#conditions).

## <a name="sample-policy-definition"></a>Voor beeld van beleids definitie

Met deze beleids definitie kunt u toegestane regio's definiëren die voldoen aan de geografische locatie vereisten van uw organisatie. De toegestane resources worden gedefinieerd in de para meter **listOfAllowedLocations** (_matrix_). Resources die overeenkomen met de definitie, worden [geweigerd](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

De **veld** operator wordt drie keer gebruikt binnen de [logische operator](../concepts/definition-structure.md#logical-operators) **overzet**.

- Het eerste gebruik evalueert de `location` eigenschap met de **notIn** -voor waarde naar de para meter **listOfAllowedLocations** . **notIn** werkt zoals verwacht een _matrix_ en de para meter is een _matrix_. Als de `location` van de gemaakte of bijgewerkte resource zich niet in de goedgekeurde lijst bevindt, resulteert dit element in waar.
- Met het tweede gebruik wordt ook de `location` eigenschap geëvalueerd, maar wordt de **notEquals** -voor waarde gebruikt om te zien of de resource _globaal_is. Als het `location` van de gemaakte of bijgewerkte resource niet _globaal_is, resulteert dit element in waar.
- Met het laatste gebruik wordt de `type` eigenschap geëvalueerd en de **notEquals** -voor waarde voor het valideren van het resource type is niet _micro soft. AzureActiveDirectory/b2cDirectories_. Als dat niet het geval is, resulteert dit element in waar.

Als alle drie voorwaarde-instructies in de logische operator **overzet** de waarde True evalueren, wordt het maken of bijwerken van de resource geblokkeerd door Azure Policy.

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).