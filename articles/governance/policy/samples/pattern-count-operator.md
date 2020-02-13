---
title: 'Patroon: de operator Count in een beleids definitie'
description: Dit Azure Policy patroon bevat een voor beeld van het gebruik van de operator Count in een beleids definitie.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 88c2d1083a92732ac56ca4d6da7087cc4220d9a5
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172945"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Azure Policy patroon: de operator Count

De operator [Count](../concepts/definition-structure.md#count) evalueert leden van een \[\*\] alias.

## <a name="sample-policy-definition"></a>Voor beeld van beleids definitie

Met deze beleids definitie worden netwerk beveiligings groepen [gecontroleerd](../concepts/effects.md#audit) die zijn geconfigureerd om inkomend Remote Desktop Protocol verkeer (RDP) toe te staan.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Uitleg

De kern onderdelen van de operator **Count** zijn _veld_, _waar_en de voor waarde. Elk is gemarkeerd in het onderstaande fragment.

- _veld_ geeft aan in welke [alias](../concepts/definition-structure.md#aliases) leden moeten worden geëvalueerd. Hier bekijken we de **securityRules-\[\*\]** alias- _matrix_ van de netwerk beveiligings groep.
- _waarbij_ de beleids taal wordt gebruikt om te definiëren welke _matrix_ leden voldoen aan de criteria. In dit voor beeld groeperen een logische operator **overzet** drie verschillende voor waarden van de alias _matrix_ eigenschappen: _Direction_, _Access_en _destinationPortRange_.
- De voor waarde aantal in dit voor beeld is **groter**. Count evalueert als true wanneer een of meer leden van de alias _matrix_ overeenkomen met de component _where_ .

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).