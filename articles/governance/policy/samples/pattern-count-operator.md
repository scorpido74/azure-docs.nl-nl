---
title: 'Patroon: de operator van het aantal in een beleidsdefinitie'
description: Dit Azure-beleidspatroon geeft een voorbeeld van het gebruik van de operator van het aantal in een beleidsdefinitie.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 88c2d1083a92732ac56ca4d6da7087cc4220d9a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172945"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Azure-beleidspatroon: de operator van het aantal

De operator van de \[ \* \] [telling](../concepts/definition-structure.md#count) evalueert leden van een alias.

## <a name="sample-policy-definition"></a>Voorbeeldbeleidsdefinitie

Met deze beleidsdefinitie [worden](../concepts/effects.md#audit) netwerkbeveiligingsgroepen gecontroleerd die zijn geconfigureerd om binnenkomend Extern bureaublad-protocol (RDP)-verkeer toe te staan.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Uitleg

De kerncomponenten van de **teloperator** zijn _veld,_ _waar_en de voorwaarde. Elk wordt gemarkeerd in het onderstaande fragment.

- _veld_ vertelt tellen welke [alias](../concepts/definition-structure.md#aliases) leden van moeten evalueren. Hier kijken we naar de **\[ \* securityRules** alias _array_ van de netwerkbeveiligingsgroep.
- _waar_ de beleidstaal wordt gebruikt om te bepalen welke _arrayleden_ aan de criteria voldoen. In dit voorbeeld **groepeert** een alleLogische operator drie verschillende conditie-evaluaties van _aliasarray-eigenschappen:_ _richting,_ _toegang_en _destinationPortRange_.
- De telvoorwaarde in dit voorbeeld is **groter**. Count evalueert als waar wanneer een of meer leden van de _aliasarray_ overeenkomen met de _waar-clausule._

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).