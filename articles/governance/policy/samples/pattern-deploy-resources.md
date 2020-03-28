---
title: 'Patroon: resources implementeren met een beleidsdefinitie'
description: Dit Azure-beleidspatroon geeft een voorbeeld van hoe u resources met een beleidsdefinitie implementeert.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: a8b6528afbd21c7c667e48965574c9b48c403654
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172672"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Azure-beleidspatroon: resources implementeren

Met het effect [deployIfNotExists](../concepts/effects.md#deployifnotexists) u een [Azure Resource Manager-sjabloon](../../../azure-resource-manager/templates/overview.md) implementeren bij het maken of bijwerken van een resource die niet voldoet. Deze benadering kan de voorkeur krijgen boven het gebruik van het [weigeringseffect,](../concepts/effects.md#deny) omdat resources hierdoor kunnen worden gemaakt, maar zorgt ervoor dat de wijzigingen worden aangebracht om ze compliant te maken.

## <a name="sample-policy-definition"></a>Voorbeeldbeleidsdefinitie

Met deze beleidsdefinitie wordt de `type` **veldoperator** gebruikt om de resource te evalueren die is gemaakt of bijgewerkt. Wanneer die bron een _Microsoft.Network/virtualNetworks_is, zoekt het beleid naar een netwerkwatcher in de locatie van de nieuwe of bijgewerkte bron. Als een overeenkomende netwerkwatcher zich niet bevindt, wordt de sjabloon Resourcebeheer geïmplementeerd om de ontbrekende bron te maken.

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>Uitleg

#### <a name="existencecondition"></a>bestaanVoorwaarde

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

Het blok **properties.policyRule.then.details** vertelt Azure Policy waar u naar moet zoeken met betrekking tot de gemaakte of bijgewerkte bron in de **eigenschappen.policyRule.if** block. In dit voorbeeld moet een netwerkwatcher in de resourcegroep **networkWatcherRG** bestaan met **een veld** `location` dat gelijk is aan de locatie van de nieuwe of bijgewerkte bron. Met `field()` behulp van de functie kan het **bestaanVoorwaarde** om `location` eigenschappen toegang te krijgen op de nieuwe of bijgewerkte bron, met name de eigenschap.

#### <a name="roledefinitionids"></a>rolDefinitieId's

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

De eigenschap **roleDefinitionIds** _array_ in het blok **properties.policyRule.then.details** vertelt de beleidsdefinitie welke rechten de beheerde identiteit nodig heeft om de meegeleverde resourcemanagersjabloon te implementeren. Deze eigenschap moet worden ingesteld op rollen die de machtigingen die nodig zijn door de sjabloon implementatie bevatten, maar moet gebruik maken van het concept van 'principe van de minste bevoegdheden' en alleen de benodigde bewerkingen en niets meer.

#### <a name="deployment-template"></a>Implementatiesjabloon

Het **implementatiegedeelte** van de beleidsdefinitie heeft een **eigenschappenblok** dat de drie kerncomponenten definieert:

- **modus** - Met deze eigenschap wordt de [implementatiemodus](../../../azure-resource-manager/templates/deployment-modes.md) van de sjabloon ingesteld.

- **sjabloon** - Deze eigenschap bevat de sjabloon zelf. In dit voorbeeld stelt de parameter **locatiesjabloon** de locatie in van de nieuwe netwerkwatcherbron.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **parameters** - Deze eigenschap definieert parameters die aan de **sjabloon**worden verstrekt. De parameternamen moeten overeenkomen met wat in **de sjabloon**is gedefinieerd. In dit voorbeeld wordt de parameter **de naam locatie** genoemd die overeenkomt. De waarde van `field()` de **locatie** gebruikt de functie opnieuw om de waarde van de geëvalueerde resource te krijgen, het virtuele netwerk in het blok **policyRule.if.**

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).