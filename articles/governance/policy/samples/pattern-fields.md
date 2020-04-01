---
title: 'Patroon: veldeigenschappen in een beleidsdefinitie'
description: Dit Azure-beleidspatroon geeft een voorbeeld van het gebruik van veldeigenschappen in een beleidsdefinitie.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: e65767dd9cbe7b2192c21f779643289e5a7fc45e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172861"
---
# <a name="azure-policy-pattern-field-properties"></a>Azure-beleidspatroon: veldeigenschappen

De [veldoperator](../concepts/definition-structure.md#fields) evalueert de opgegeven eigenschap of [alias](../concepts/definition-structure.md#aliases) naar een opgegeven waarde voor een bepaalde [voorwaarde](../concepts/definition-structure.md#conditions).

## <a name="sample-policy-definition"></a>Voorbeeldbeleidsdefinitie

Met deze beleidsdefinitie u toegestane regio's definiëren die voldoen aan de geolocatievereisten van uw organisatie. De toegestane resources worden gedefinieerd in **parameterlijstAllowedLocations** _(array)._ Resources die overeenkomen met de definitie [worden geweigerd](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

De **veldoperator** wordt drie keer gebruikt binnen de [logische operator](../concepts/definition-structure.md#logical-operators) **allOf**.

- Het eerste gebruik `location` evalueert de eigenschap met de **voorwaarde nietIn** aan de parameter **listOfAllowedLocations.** **notIn** werkt zoals het verwacht een _array_ en de parameter is een _array_. Als `location` de van de gemaakte of bijgewerkte resource niet in de goedgekeurde lijst staat, wordt dit element geëvalueerd in true.
- Het tweede gebruik evalueert ook de `location` eigenschap, maar gebruikt de voorwaarde **notEquals** om te zien of de resource _globaal_is. Als `location` de van de gemaakte of bijgewerkte resource niet _globaal_is, wordt dit element geëvalueerd in true.
- Het laatste gebruik `type` evalueert de eigenschap en gebruikt de **voorwaarde notEquals** om het resourcetype te valideren, niet _Microsoft.AzureActiveDirectory/b2cDirectories_. Als dit niet het geval is, wordt dit element geëvalueerd.

Als alle drie de voorwaardeverklaringen in de logische operator **allOf** true evalueren, wordt het maken of bijwerken van de resource geblokkeerd door Azure Policy.

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).